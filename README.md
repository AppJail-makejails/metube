# MeTube

Web GUI for youtube-dl (using the [yt-dlp](https://github.com/yt-dlp/yt-dlp) fork) with playlist support. Allows you to download videos from YouTube and [dozens of other sites](https://github.com/yt-dlp/yt-dlp/blob/master/supportedsites.md).

github.com/alexta69/metube

<img src="https://raw.githubusercontent.com/alexta69/metube/master/favicon/android-chrome-384x384.png" alt="metube logo" width="30%" height="auto">

## How to use this Makejail

```sh
appjail makejail -j metube -f gh+AppJail-makejails/metube \
    -o virtualnet=":metube default" \
    -o nat \
    -o expose=8081
appjail start -s metube_dark_mode=true metube
```

### Arguments (stage: build):

* `metube_tag` (default: `13.2`): see [#tags](#tags).

### Arguments (stage: start)

* `metube_dark_mode` (default: `false`): if set to true, the UI will be in dark mode.
* `metube_download_dir` (default: `.`): path to where the downloads will be saved.
* `metube_audio_download_dir` (default: `${metube_download_dir}`): path to where audio-only downloads will be saved.
* `metube_download_dirs_indexable` (default: `false`): if true, the download dirs are indexable on the webserver.
* `metube_custom_dirs` (default: `true`): whether to enable downloading videos into custom directories within the `metube_download_dir` (or `metube_audio_download_dir`). When enabled, a drop-down appears next to the Add button to specify the download directory.
* `metube_create_custom_dirs` (default: `true`): whether to support automatically creating directories within the `metube_download_dir` (or `metube_audio_download_dir`) if they do not exist. When enabled, the download directory selector becomes supports free-text input, and the specified directory will be created recursively.
* `metube_state_dir` (default: `.`): path to where the queue persistence files will be saved. 
* `metube_temp_dir` (default: `.`): path where intermediary download files will be saved. 
* `metube_delete_file_on_trashcan` (default: `false`): if true, downloaded files are deleted on the server, when they are trashed from the "Completed" section of the UI.
* `metube_url_prefix` (default: `/`): base path for the web server (for use when hosting behind a reverse proxy).
* `metube_output_template` (default: `%(title)s.%(ext)s`): the template for the filenames of the downloaded videos, formatted according to [this spec](https://github.com/yt-dlp/yt-dlp/blob/master/README.md#output-template).
* `metube_output_template_chapter` (default: `%(title)s - %(section_number)s %(section_title)s.%(ext)s`): the template for the filenames of the downloaded videos, when split into chapters via postprocessors.
* `metube_ytdl_options` (default: `{}`): Additional options to pass to youtube-dl, in JSON format. [See available options here](https://github.com/yt-dlp/yt-dlp/blob/master/yt_dlp/YoutubeDL.py#L192). They roughly correspond to command-line options, though some do not have exact equivalents here, for example `--recode-video` has to be specified via `postprocessors`. Also note that dashes are replaced with underscores.

### Check current status

The custom stage `metube_status` can be used to run `top(1)` to check the status of MeTube.

```sh
appjail run -s metube_status metube
```

### Log

To view the log generated by the web application, run the custom stage `metube_log`.

```sh
appjail run -s metube_log metube
```

## How to build the Image

Make any changes you want to your image.

```
INCLUDE options/network.makejail
INCLUDE gh+AppJail-makejails/metube --file build.makejail
```

Build the jail:

```sh
appjail makejail -j metube -- \
    --metube_builder_options "${PWD}/options/network.makejail"
```

Remove unportable or unnecessary files and directories and export the jail:

```sh
appjail stop metube
appjail cmd local metube sh -c "rm -f var/log/*"
appjail cmd local metube sh -c "rm -f var/cache/pkg/*"
appjail cmd local metube sh -c "rm -f var/run/*"
appjail cmd local metube vi etc/rc.conf
appjail image export metube
```

### Arguments

* `metube_clone_builder` (default: `metube_clone`): Makejail builder name for the jail in charge of downloading MeTube.
* `metube_ui_builder` (default: `metube_ui`): Makejail builder name for the jail in charge of building the UI.
* `metube_builder_options` (mandatory): Makejail that contains options for Makejail builders. This Makejail is intended for passing network options. An absolute path must be passed.

## Tags

| Tag    | Arch    | Version           | Type   |
| ------ | ------- | ----------------- | ------ |
| `13.2` | `amd64` | `13.2-RELEASE-p1` | `thin` |
