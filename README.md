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
Here is a list of known issues that I'm not sure how to deal with so far.

* The sound gets occasionally choppy
* Sound vanishes even though a device continues to make the sound
* An iPhone can't recognize its microphone in some apps when it's connected to PulseAudio



## License
All codes of this project are available under the MIT license. See the [LICENSE](/LICENSE) for more information.
