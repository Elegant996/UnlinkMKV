=head1 NAME

unlinkmkv - automate the tedious process of unlinking segmented MKV files.

=head2 WHAT?

A segmented MKV is an MKV that utilizes external additional MKV files to create a "whole" MKV. A common example is that when an anime series uses the same introduction and ending in every episode, sometimes the encoder will break the introduction and ending into their own MKV files, and then "link" to the segments as chapters in each episode's individual MKV. The problem is that very few players/filters/splitters support this, so this script automates the mkvtoolnix tools to "Rebuild" each episode into "complete" MKVs.

=head2 VERSION

version 1.01

=head2 SYNOPSIS

Using UnlinkMKV to unlink segments, the chapters when viewing the video ARE retained. The MKV spec requires that the segmented files are in the same directory, so does this. It doesn't matter if you rename the files as long as all required files are in the same directory as the one being processed, segmenting is based on internal IDs. Depending on how the original files were made, you may need to use the fix-audio and fix-video options.

Say we have the files:

   Fate-Zero - 01.mkv
   Fate-Zero - OP1.mkv
   Fate-Zero - ED1.mkv

And "Fate-Zero - 01.mkv" links the opening and ending files in the "appropriate places."

   unlinkmkv --outdir "L:\Fate-Zero" Fate-Zero - 01.mkv

Doing so will generate a new file with the opening and ending built-in instead of externally linked like before, renaming the original file and replacing the old one with the new one. Obviously the new file will be the size of the original + opening + ending (bigger).

Now, we test the file in a video player. It's important to check for audio or video problems, especially when transitioning between where the segments would have come in. Often times this is the opening/ending and the main show, so seek inside both places and make sure sound is playing in both places, and the video looks fine. Let's
pretend our sound is totally missing in the main video, and the video is corrupt!

   unlinkmkv --fix-audio --fix-video --outdir "L:\Fate-Zero" Fate-Zero - 01.mkv

What happens is sometimes encoders use codecs that don't play well with matroska, and can't be assembled as they were -- either the codec (OGG) or the settings (thanks, encoder guy) being the cause. The fix-audio and fix-video option simply re-encodes the audio and video into a uniform format that plays nice, with settings that try to not reduce the quality very much (not noticable), nor make the files hugemassive. See the FFMPEG notes!

You can also use an absolute or relative path instead, so, assume you're in a directory containing the entire seeson of an anime including the segmented file parts:

   Fate-Zero - 01.mkv
   Fate-Zero - 02.mkv
   Fate-Zero - 03.mkv
   Fate-Zero - OP1.mkv
   Fate-Zero - ED1.mkv

You could process them all at once with:

   unlinkmkv --fix-audio --fix-video --outdir "L:\Fate-Zero" "D:\Videos"

=head2 INSTALLATION

This script was altered from its original counterpart to support Windows. It will not work on Linux based systems. The script is designed to use your current working directory if you specify no output directory and to use your first "TMP" folder listed in your environment variables for extraction.

=head2 DEPENDENCIES

This script requires:

   Perl        				5.8.9 through 5.18.2.2 (anything beyond is NOT supported)
   FFmpeg					Latest
   MKVToolnix  				6.0.0 or later
   String::CRC32			1.5 or later
   Win32::Console::ANSI		1.08 or later
   Win32:Socketpair			0.02 or later
   
   FFmpeg and MKVToolnix must be added to the Environment Variables Path in order for script or executable to function.
   

=head2 USAGE

   unlinkmkv {options} {file|path}

   Options:
   --tmpdir               Set a custom temporary/working folder, /tmp/umkv is the default
   --outdir               Output directory, required!
   --keep-workdir         Don't clean working directory when finished, useful for debugging.
   --fix-audio            Encode audio, not currently customizable; encodes to 320k AAC for the time being.
   --fix-video            Encode video, not currently customizable; encodes to 8-bit h264 at whatever the existing average bitrate was up to 110% higher then that.
   --fix-subtitles        Defaults to on. Sometimes groups don't use uniform subtitle styles and fonts in all segments, it's a good idea to leave this enabled.
   --playresx             Occassionally the original encoder uses different subtitle resolutions in the different segments. When combined, it causes problems. This forcibly
                          sets the X resolution for subtitle rendering.
   --playresy             Same as above, but for the Y axis (vertical).
   --ignore-default-flag  Occassionally the default chapter flag exists, but *all* chapters are disabled which confuses the script. Enable on those rare occassions.
   --(no-)chapters        The script does its best to adjust the chapters, but it's not quite perfect. This disables including chapters in the final file.


=head2 SUPPORT

Feel free to contact me via Github.

=head2 SPECIAL THANKS

I'd like to thank the original author, Garret Noling, for creating this script in the first place for use on Linux based systems.


=head2 COPYRIGHT AND LICENSE

Copyright (C) 2014 Shane Panke

This program is free software; you can redistribute it and/or modify it under the same terms as Perl itself.
