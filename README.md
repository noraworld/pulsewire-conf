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

# Known issues
Here is a list of known issues that I'm not sure how to deal with so far.

* Choppy sound
* Sound vanishes even though a device makes the sound
