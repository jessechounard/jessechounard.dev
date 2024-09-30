+++
title = 'SDL3 Audio'
date = 2024-09-21T10:59:14-05:00
draft = false
+++
I've decided that I should start writing up some notes on my side project dev work. Well, here goes...

Lately I've been working on moving as much of my code as possible to using the new features of SDL3 when possible. Some of the changes are really great, and moving over means that I have to rely less on external dependencies which should make it easier to keep running in the future.

My latest project was moving over to using their new audio system instead of the code I'd built on top of OpenAL forever ago. It worked fine, but after Creative abandoned it (and later Apple deprecated it) I'd needed to rely on the OpenAL Soft project instead.

The new SDL3 audio features are pretty awesome. For a great introduction, I recommend watching this video that Ryan Gordon (@icculus) made on the topic:

{{< youtube MLau3hWJBeE >}}
<br />  
I started using the callback version of the API, where you'd register a callback method to notify you when the audio device was running low on samples and you needed to queue up some more to your audio stream. I just kept running into issues with race conditions because the callbacks will happen on a different thread. It was easier to switch to a push based system. I just check once per frame how many samples are queued up on each audio stream, and add a little more when necessary.

I'm using dr_mp3 and dr_wav from David Reid's [dr_libs](https://github.com/mackron/dr_libs) for loading mp3 and wav files, and Sean Barrett's [stb_vorbis](https://github.com/nothings/stb) for loading ogg vorbis files. I really love header-only libraries.

A fun little bonus is that the only mp3s I had handy on my machine are from the Baldur's Gate 3 soundtrack, so I've had some lovely songs to listen to while I debugged all of this.