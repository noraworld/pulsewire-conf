# pulse-conf
PulseAudio config files.

## Setup
```shell
cd pulse-conf
sudo rm -r /etc/pulse
sudo ln -s $PWD/pulse /etc/pulse
systemctl --user enable pulseaudio
sudo gpasswd -a $(whoami) pulse
sudo gpasswd -a $(whoami) pulse-access
```
