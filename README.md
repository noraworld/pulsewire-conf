# pulse-conf
PulseAudio config files.

## Setup
```shell
$ sudo rm -r /etc/pulse
$ sudo ln -s /path/to/pulse-conf/pulse /etc/pulse
$ sudo gpasswd -a ubuntu pulse
$ sudo gpasswd -a ubuntu pulse-access
```
