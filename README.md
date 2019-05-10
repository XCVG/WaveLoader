# WaveLoader

**A quick and dirty WAV file loader for .NET and Unity**

## Overview

This is a dirt-simple, freely licensed little chunk o' code that can be used to load a WAV file, parse its headers, and turn it into an AudioClip in Unity.

## Usage

If you just want to turn a WAV file into an audio clip in Unity, that's easy:

`AudioClip myAudioClip = WaveFile.Load(myByteArray).ToAudioClip("nameOfAudioClip");`

You can also pass in a path to your WAV file:

`AudioClip myAudioClip = WaveFile.Load("Path/To/My/Sound.wav").ToAudioClip("nameOfAudioClip");`

Or, if you have no interest in AudioClips, you can create a WaveFile object:

`WaveFile myWaveFile = WaveFile.Load(myByteArray);`

You can't do much other than look at it yet, though.

## Notes

The Unity-specific stuff is in WaveLoader.cs. Everything in WaveFile.cs doesn't depend on Unity. Yes, ToAudioClip is actually an extension method!

The code does a reasonable amount of error checking and should reject most things that don't look like valid wave files with a FormatException. It will also reject some formats it can't handle, but will misinterpret some as 32-bit float. This is because it treats *WAVE_FORMAT_EXTENSIBLE* as float data, because for some reason ffmpeg creates files with that in the header when you ask it to make 32-bit float files.

Unlike some of the other code snippets and libraries floating around out there, WaveLoader actually reads the entire RIFF header, parsing chunks it recognizes and ignoring ones it doesn't. That means it can handle non-standard and extra chunks in the file, although it does still require a well-formed *fmt* chunk.

I'm pretty sure this handles 8- and 24-bit WAV files correctly; they sound correct to my ears. But there might be some off-by-one lurking here or there. The 16- and 32-bit (PCM and float) formats are easier to deal with and I'm reasonably confident those are handled correctly. 

This isn't very well optimized for speed or memory. It makes a copy of the original byte array, which is safer if you want to keep the WaveFile object around, but wastes a significant amount of memory if you don't. It also does some magic with delegates in the conversion routine, which might actually be slower than conditionals but to be honest I haven't profiled it.

If I have time, I'll probably do some optimization, try to improve format detection, and maybe add support for the *list* chunk.

## Acknowledgements

Inspired by [Wav Utility for Unity](https://github.com/deadlyfingers/UnityWav), though mine is an all-new implementation.

I worked off the following sources to figure out how WAV files should be parsed:
- <http://soundfile.sapp.org/doc/WaveFormat/>
- <http://www-mmsp.ece.mcgill.ca/Documents/AudioFormats/WAVE/WAVE.html>
- <https://blogs.msdn.microsoft.com/dawate/2009/06/23/intro-to-audio-programming-part-2-demystifying-the-wav-format/>
- <https://trac.ffmpeg.org/wiki/audio%20types>
- <https://www.recordingblogs.com/wiki/list-chunk-of-a-wave-file>
- <https://www.recordingblogs.com/wiki/fact-chunk-of-a-wave-file>
- <https://en.wikipedia.org/wiki/Resource_Interchange_File_Format>
- <https://en.wikipedia.org/wiki/WAV>
- <http://wavefilegem.com/how_wave_files_work.html>

As well as a fair bit of experimentation with Audacity, ffmpeg, and a hex editor to figure out how WAV files *actually need to be parsed*.

## License

This library is licensed under the MIT License. See the included LICENSE file for details.