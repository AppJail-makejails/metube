# MeTube

Web GUI for youtube-dl (using the [yt-dlp](https://github.com/yt-dlp/yt-dlp) fork) with playlist support. Allows you to download videos from YouTube and [dozens of other sites](https://github.com/yt-dlp/yt-dlp/blob/master/supportedsites.md).

github.com/alexta69/metube

<img src="https://imgur.com/13cGROD.png" alt="metube logo" width="30%" height="auto">

## How to use this Makejail

```sh
# Create the jail.
appjail makejail -j metube -f gh+AppJail-makejails/metube \
    -o virtualnet=":metube default" \
    -o nat \
    -o expose=8081
# Create any directory you need. The following are examples and all of them are
# optional to configure, but may be worth setting up depending on you and your
# environment.
appjail cmd local metube mkdir -p app/files/downloads
appjail cmd local metube mkdir -p app/files/audio
appjail cmd local metube mkdir -p app/files/state
# Remember to set the correct permissions. The UID of metube is 1001 and its GID is 1001.
appjail cmd local metube chown -Rf 1001:1001 app/files
# Pass environment variables as described in
# https://github.com/alexta69/metube#configuration-via-environment-variables
appjail enable metube start \
    -V DEFAULT_THEME=dark \
    -V DOWNLOAD_DIR=files/downloads \
    -V AUDIO_DOWNLOAD_DIR=files/audio \
    -V STATE_DIR=files/state \
    -V TEMP_DIR=tmp \
    -V DELETE_FILE_ON_TRASHCAN=true
# Start the jail.
appjail start metube
```

### Arguments (stage: build):

* `metube_tag` (default: `13.5`): see [#tags](#tags).
* `metube_ajspec` (default: `gh+AppJail-makejails/metube`): Entry point where the `appjail-ajspec(5)` file is located.

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

### Volumes

| Name         | Owner | Group | Perm | Type | Mountpoint  |
| ------------ | ----- | ----- | ---- | ---- | ----------- |
| metube-files | 1001  | 1001  |  -   |  -   | /app/files  |

## Tags

| Tag    | Arch    | Version        | Type   |
| ------ | ------- | -------------- | ------ |
| `13.5` | `amd64` | `13.5-RELEASE` | `thin` |
| `14.3` | `amd64` | `14.3-RELEASE` | `thin` |
