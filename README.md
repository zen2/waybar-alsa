# waybar-alsa
waybar custom module to control alsa devices

![Image](https://github.com/user-attachments/assets/b91b333f-7bc1-4a9c-ab2b-6cc1aac7fab4)

![Image](https://github.com/user-attachments/assets/ab43e9af-2dc0-4c4d-b467-17119d666f47)

![Image](https://github.com/user-attachments/assets/5b2885b9-df53-4f0a-9ea0-326e05f9d28e)

## alsa helper

Download the alsa helper that will be used by waybar custom modules using git clone or wget.
```
wget https://raw.githubusercontent.com/zen2/waybar-alsa/refs/heads/main/alsa
```

The helper usage:
```
alsa [OPTION]... volume-up [INC]|volume-down [INC]|mute|unmute|toggle
alsa [OPTION]... on|off|toggle|set VALUE|waybar-exec
alsa [OPTION]... list-cards|list-devices|list-controls

Alsa options:
 -c card      Select the alsa card by number or name
                List: /proc/asound/cards - Ex: 2|NVidia|PCH|Device
 -D device    Select the alsa device instead of the default one
                List: aplay -L - Ex: hdmi:CARD=NVidia,DEV=0
 -C control   Select the mixer control. Default to Master
                List: amixer scontrols - Ex: Mic|Line|Aux

Waybar options:
 -n           Make waybar text start with a new line if not muted
 -d           Use db level instead of percentage for text

Commands:
 volume-up INC   Raise the volume with optional INC
 volume-down INC Decrease the volume with optional INCof INC
                 INC set the volume step: [0-9]% or [0-9]dB (default to 2%)
 mute|off        Mute or set to off
 unmute|on       Unmute or set to on
 toggle          Toggle mute|unmute or on|off
 set VALUE       Set control to VALUE
 waybar-exec     To use for exec command of a waybar custom module

List commands:
 list-cards    list number/name of alsa cards that can be used with -c option
 list-devices  list alsa devices that can be used with -D option
 list-controls list alsa controls that can be used with -C option

Examples:
 alsa list-controls
 alsa volume-up 2dB
 alsa -c STX -C Mic mute
 alsa -C 'Mic Boost (+20dB)' toggle
 alsa -C Analog Output' set Headphones
```

## waybar-configuration

In order to use the alsa helper script the custom module configuration requires:
- "exec": "$HOME/.config/waybar/scripts/alsa waybar-exec",
- "exec-on-event": false,
- "return-type": "json",

The json output set:
- text to volume level (% or dB), empty if muted
- alt to mute or default to be used for format-icons selection
- class to mute or default to be used for style

Notes: format_icon for alt=default can be an array of icons that will be selected by the percentage of volume.

Exemple of custom modules configuration:
```
    "custom/alsa-master": {
       "exec": "$HOME/.config/waybar/scripts/alsa -n waybar-exec",
       "exec-on-event": false,
       "return-type": "json",
       "format" : "<span size='120%'>{icon}</span><span size='70%'>{text}</span>",
       "format-icons": {
         "muted": "",             // icon: volume-xmark
         "default": ["","",""]  // icons: volume-off, volume-low, volume-high
       },
       "tooltip": false,
       "on-click": "qasmixer &",
       "on-click-middle": "$HOME/.config/waybar/scripts/alsa toggle",
       "on-scroll-up": "$HOME/.config/waybar/scripts/alsa volume-up",
       "on-scroll-down": "$HOME/.config/waybar/scripts/alsa volume-down"
    },
    "custom/mic": {
       "exec": "$HOME/.config/waybar/scripts/alsa -C Mic -n waybar-exec",
       "exec-on-event": false,
       "return-type": "json",
       "format" : "<span size='120%'>{icon}</span><span size='70%'>{text}</span>",
       "format-icons": {
         "muted": "",              // icon: microphone-lines-slash
         "default": ""             // icon: microphone-lines
       },
       "tooltip": false,
       "on-click": "qasmixer &",
       "on-click-middle": "$HOME/.config/waybar/scripts/alsa -C Mic toggle",
       "on-click-right": "$HOME/.config/waybar/scripts/alsa -C 'Mic Boost (+20dB)' toggle",
       "on-scroll-up": "$HOME/.config/waybar/scripts/alsa -C Mic volume-up",
       "on-scroll-down": "$HOME/.config/waybar/scripts/alsa -C Mic volume-down"
    },
```
