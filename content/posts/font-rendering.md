+++
title = 'Font Rendering'
date = 2024-10-01T06:54:04-05:00
draft = false
+++

One of the inspirations for starting this blog was [Amit Patel's blog](https://www.redblobgames.com/blog/2024-09-08-sdf-font-spacing/), where he was recently writing about rendering SDF (signed distance field) fonts. I found it really interesting.

It turns out, rendering fonts is pretty complicated.

I had no idea just how much goes into it, as my first few games were written with XNA, which hid all of the complicated bits from you to make it easy to get started. Of course, most of us had no idea what we were doing and the results were terrible. Blurry letters from scaling prerendered bitmap fonts, nasty gaps because we didn't have any kerning into, and (at least in my case) some terrible font choices.

Since then I've used a bunch of tools for fonts. [BMFont](https://www.71squared.com/glyphdesigner) is a classic that's been shared among indies forever. I'm impressed to see that it's still getting updates in 2024. [Glyph Designer](https://www.71squared.com/glyphdesigner) is a commercial Mac only tool that does something similar generating prerendered bitmap fonts, but includes features like outlines and gradient effects. I don't see anything about recent updates, and most of the posts in their forums look about a decade old. I might see if my license key still works later and play with it, because I remember it being pretty slick.

For this week's experiment, I played around with [stb_truetype](https://github.com/nothings/stb/blob/master/stb_truetype.h). This is another of Sean Barrett's header-only C libraries, and this one makes it pretty easy to render fonts at runtime at the size you'll need for the resolution your game is running at. A big drawback to prerendered bitmap fonts is that unless the player is running at the resolution you designed for, you'll need to scale the font at runtime which can lead to a blurry mess.

Long story short, it was pretty easy and looks really great.

![Font rendered on a baseline](/font-rendering-images/baseline.jpg)

A couple of interesting discoveries.

When you give a Y coordinate to render the font, that's used as the baseline. The font will rise and fall above that point. (You can see that in the image above.) If you need to find a bounding box for your text, that's pretty easy to do with the library.

Kerning didn't quite work out of the box for me. It seems like there are two options included in fonts. `gpos` (the OpenType glyph positioning table) and `kern` (TrueType kerning table) are both supported by stb_truetype. gpos is considered to be the better option, and if it exists in the file, stb_truetype will use it instead of kern. Unfortunately, for some reason, a lot of the fonts I've been experimenting with include a gpos table, but all of the entries are zero even when the kern value is not. So it just doesn't work.

A fix I found on the stb github changes the code to check for gpos first, but if it's not present or it's zero, then check for kern. So we go from this:

```c
   if (info->gpos)
      xAdvance += stbtt__GetGlyphGPOSInfoAdvance(info, g1, g2);
   else if info->kern)
      xAdvance += stbtt__GetGlyphKernInfoAdvance(info, g1, g2);
```

To this:
```c
   if (info->gpos)
      xAdvance += stbtt__GetGlyphGPOSInfoAdvance(info, g1, g2);
   if (xAdvance == 0 && info->kern)
      xAdvance += stbtt__GetGlyphKernInfoAdvance(info, g1, g2);
```

Now it works great! Here is the result. Kerning makes a huge difference!

![A kerning example image](/font-rendering-images/kerning.jpg)

For future font experiments, I would like to investigate SDF fonts and I'm very curious about the [Slug Font Rendering Library](https://sluglibrary.com/).