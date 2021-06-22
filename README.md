FFmpeg README
=============

FFmpeg is a collection of libraries and tools to process multimedia content
such as audio, video, subtitles and related metadata.

### This fork of FFmpeg has a single addition: handling unexpected stream terminations.

The file libavformat/utils.c has the routine av_read_frame() modified to call the 
library client's I/O Interrupt Callback within the stream packet reading forever
loop, inside av_read_frame(). By installing one's own I/O Interrupt Callback an
application can monitor IP and USB streams for the unexpected event of stream loss.
Unexpected stream termination is identified by a call to av_read_frame() simply taking 
too long. When calling av_read_frame() with an installed I/O Interrupt Callback, every
loop attempt to read from the stream also receives an I/O Interrupt Callback. Within
the I/O Interrupt Callback the library client simply checks the clock - if this read
is taking too long, return true to signal a stream's unexpected termination. 

The I/O Interrupt Callback looks like:

  `int interrupt_callback( void *context );`
  
And it is installed by setting pointers on the AVFormatContext:
 
  `AVFormatContext* p_format_context = avformat_alloc_context();
  p_format_context->interrupt_callback.callback = FFVideo_FrameMgr::interrupt_callback;
  p_format_context->interrupt_callback.opaque = mp_frameMgr;`

This is the only other modification to FFmpeg. 

## Libraries

* `libavcodec` provides implementation of a wider range of codecs.
* `libavformat` implements streaming protocols, container formats and basic I/O access.
* `libavutil` includes hashers, decompressors and miscellaneous utility functions.
* `libavfilter` provides a mean to alter decoded Audio and Video through chain of filters.
* `libavdevice` provides an abstraction to access capture and playback devices.
* `libswresample` implements audio mixing and resampling routines.
* `libswscale` implements color conversion and scaling routines.

## Tools

* [ffmpeg](https://ffmpeg.org/ffmpeg.html) is a command line toolbox to
  manipulate, convert and stream multimedia content.
* [ffplay](https://ffmpeg.org/ffplay.html) is a minimalistic multimedia player.
* [ffprobe](https://ffmpeg.org/ffprobe.html) is a simple analysis tool to inspect
  multimedia content.
* Additional small tools such as `aviocat`, `ismindex` and `qt-faststart`.

## Documentation

The offline documentation is available in the **doc/** directory.

The online documentation is available in the main [website](https://ffmpeg.org)
and in the [wiki](https://trac.ffmpeg.org).

### Examples

Coding examples are available in the **doc/examples** directory.

## License

FFmpeg codebase is mainly LGPL-licensed with optional components licensed under
GPL. Please refer to the LICENSE file for detailed information.

## Contributing

Patches should be submitted to the ffmpeg-devel mailing list using
`git format-patch` or `git send-email`. Github pull requests should be
avoided because they are not part of our review process and will be ignored.
