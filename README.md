# pulsewire-conf
PulseAudio & PipeWire config files.

Do you want to know the differences between PulseAudio and PipeWire? Take a look at the [Known issues](#known-issues) and "[Should I choose PulseAudio or PipeWire?](#should-i-choose-pulseaudio-or-pipewire)" sections below.



## Setup
Note that only one of PulseAudio or PipeWire can be used at the same time.

### Before the detailed setup
oFono will not be used for this configuration. So you need to disable it first.

```shell
sudo systemctl disable --now ofono
sudo systemctl mask ofono
```

Setup either [PulseAudio](#pulseaudio) or [PipeWire](#pipewire) according to the instructions below. You don't have to do both.

### PulseAudio
```shell
cd pulsewire-conf
sudo rm -r /etc/pulse
sudo ln -s $PWD/pulse /etc/pulse # for system-wide changes (see Synopsis below if you want to change the path)
systemctl --user unmask pulseaudio
systemctl --user --now disable pipewire{,-pulse}.{socket,service}
systemctl --user --now enable pulseaudio.service pulseaudio.socket
```

### PipeWire
```shell
cd pulsewire-conf
ln -s $PWD/pipewire $HOME/.config # for local changes (see Synopsis below if you want to change the path)
systemctl --user --now disable pulseaudio.{socket,service}
systemctl --user mask pulseaudio
systemctl --user --now enable pipewire{,-pulse}.{socket,service}
```

### After the detailed setup
```shell
sudo gpasswd -a $(whoami) pulse
sudo gpasswd -a $(whoami) pulse-access
sudo reboot
```



## Synopsis
You can change the paths of configuration files to link with a `ln` command depending on whether they're run locally or system-wide.

### PulseAudio
* `~/.config/pulse/default.pa` (for local changes)
* `/etc/pulse/default.pa` (for system-wide changes)
* `/etc/pulse/system.pa` (for system-wide changes)

[Ubuntu Manpage: default.pa - PulseAudio Sound Server Startup Script](https://manpages.ubuntu.com/manpages/bionic/man5/default.pa.5.html)

### PipeWire
* `$XDG_CONFIG_HOME/pipewire/pipewire.conf` (for local changes)
* `/etc/pipewire/pipewire.conf` (for system-wide changes)
* `/usr/share/pipewire/pipewire.conf` (default config path)

[Ubuntu Manpage: pipewire.conf - The PipeWire server configuration file](https://manpages.ubuntu.com/manpages/impish/man5/pipewire.conf.5.html)



## How to restart the PulseAudio process and daemon easily
You can use `bin/pa`.

```shell
bin/pa restart
```

For details on the other commands, see `bin/pa --help`.



## How to figure out the appropriate `default-fragments` and `default-fragment-size-msec`
You can use `bin/fragment-determiner`.

```shell
bin/fragment-determiner
```

<details>
<summary>Sample output</summary>

```
sink 0 "alsa_output.usb-ZOOM_Corporation_U-44-00.analog-surround-40" with sample spec s32le 4ch 48000Hz and channel map front-left,front-right,rear-left,rear-right
        default-fragments = 2
        default-fragment-size-msec = 250

source 1 "alsa_input.usb-ZOOM_Corporation_U-44-00.multichannel-input" with sample spec s32le 4ch 48000Hz and channel map front-left,front-right,rear-left,rear-right
        default-fragments = 2
        default-fragment-size-msec = 250

sink 1 "alsa_output.platform-bcm2835_audio.stereo-fallback" with sample spec s16le 2ch 48000Hz and channel map front-left,front-right
        default-fragments = 2
        default-fragment-size-msec = 31
```
</details>

Or

```shell
bin/fragment-determiner --verbose
```

<details>
<summary>Sample output</summary>

```
sink 0 "alsa_output.usb-ZOOM_Corporation_U-44-00.analog-surround-40" with sample spec s32le 4ch 48000Hz and channel map front-left,front-right,rear-left,rear-right
        values used in calculations:
                sample_rate = 48000
                alsa.resolution_bits = 32
                device.buffering.buffer_size = 1536000
                device.buffering.fragment_size = 768000

        default-fragments = 2
                  (buffer_size / (sample_rate * resolution_bit * 2) * 1000) / fragment_size_msec
                = (1536000 / (48000 * 32 * 2) * 1000) / 250
                = 2

        default-fragment-size-msec = 250
                  fragment_size / (sample_rate * resolution_bit * 2) * 1000
                = 768000 / (48000 * 32 * 2) * 1000
                = 250

source 1 "alsa_input.usb-ZOOM_Corporation_U-44-00.multichannel-input" with sample spec s32le 4ch 48000Hz and channel map front-left,front-right,rear-left,rear-right
        values used in calculations:
                sample_rate = 48000
                alsa.resolution_bits = 32
                device.buffering.buffer_size = 1536000
                device.buffering.fragment_size = 768000

        default-fragments = 2
                  (buffer_size / (sample_rate * resolution_bit * 2) * 1000) / fragment_size_msec
                = (1536000 / (48000 * 32 * 2) * 1000) / 250
                = 2

        default-fragment-size-msec = 250
                  fragment_size / (sample_rate * resolution_bit * 2) * 1000
                = 768000 / (48000 * 32 * 2) * 1000
                = 250

sink 1 "alsa_output.platform-bcm2835_audio.stereo-fallback" with sample spec s16le 2ch 48000Hz and channel map front-left,front-right
        values used in calculations:
                sample_rate = 48000
                alsa.resolution_bits = 16
                device.buffering.buffer_size = 96000
                device.buffering.fragment_size = 48000

        default-fragments = 2
                  (buffer_size / (sample_rate * resolution_bit * 2) * 1000) / fragment_size_msec
                = (96000 / (48000 * 16 * 2) * 1000) / 31
                = 2

        default-fragment-size-msec = 31
                  fragment_size / (sample_rate * resolution_bit * 2) * 1000
                = 48000 / (48000 * 16 * 2) * 1000
                = 31
```
</details>



## Known issues
Here is a list of known issues that I'm not sure how to deal with so far. It would be great if you could tell me how to fix these issues if you are familiar with PulseAudio and PipeWire.

### Both PulseAudio & PipeWire
* The sound gets occasionally choppy
* Sound vanishes even though a device continues to make the sound
* A Bluetooth connection gets disconnected unexpectedly, even when PulseAudio is being used
    * PipeWire is less likely to cause this problem than PulseAudio, in my experience

### PulseAudio
* An iPhone can't recognize its microphone in some apps when it's connected to PulseAudio

### PipeWire
* It can't switch a profile to HSP/HFP
* The sound gets slightly off
* It can't establish the connection on the client end, or the clients can't recognize it as the audio profile



## Should I choose PulseAudio or PipeWire?
From my perspective, I feel like PipeWire is more stable than PulseAudio. I recommend PipeWire if you don't have a specific reason, but it's also recommended to check out the [Known issues](#known-issues) above before you make a decision.



## License
All codes of this project are available under the MIT license. See the [LICENSE](/LICENSE) for more information.
