# mplayer-waybar

I stream internet radio using mplayer and wanted to display the artist and song name in waybar/sway.

mplayer can present the artist and song name using ICY. Below, I use sed to strip all none-ICY information:

    mplayer -nocache -afm ffmpeg http:/xx.xx.xx:2290/ |& sed -ne "s/^[[:space:]]*ICY Info: StreamTitle='[[:space:]]*//" -e "s/';//p"

I append the following to the previous command to send the string to a named pipe (see `mkfifo <filename>`):
  
    > /tmp/way-bar-radio-fifo
    
In waybar's `config` I have:

    "custom/mplayer": {
      "exec": "~/bin/waybar-radio.sh",
      "interval": 0,
      "format": "{}", 
      "tooltip": false
    }
    
And `~/bin/waybar-radio.sh` contains:

    #!/bin/bash
    pipe=/tmp/way-bar-radio-fifo
    [[ -f $pipe ]] || mkfifo $pipe
    trap "rm -f $pipe" EXIT

    while read; do echo "$REPLY"; done 0<> $pipe

That's it! This worked for me.
