[![Stargazers repo roster for @jdeath/Hubspace-Homeassistant](https://git-lister.onrender.com/api/stars/jdeath/Hubspace-Homeassistant?limit=30)](https://github.com/jdeath/Hubspace-Homeassistant/stargazers)

Please ask support questions in homeassistant forums: https://community.home-assistant.io/t/add-support-for-hubspace-by-afero/306645


### Breaking Change:
Now supports services for capabily not provided by the itegration. See Services section below

Thanks to @dloveall this release will automatically discover most devices. Specifying your freindlynames is still possible, but this now finds most models attached to your account. There may be some that are not auto discovered.

Since some of the internals were changed, so your light name may change. For instance, light.friendlyname might turn into light.freindlyname_2

To solve this, go to Settings->Devices and Services->Entities
find the light.friendlyname and delete it. then find the light.freindlyname_2 and rename it light.freindlyname

### Information :
99% of the issues are your friendlyname is incorrect, see Troubleshooting section below. Please use the issue section only for support of new devices.

Major rewrite. Now it caches tokens for 118s and shares authentication for all lights, thus makes fewer API calls

Supports on/off for a couple types of light strips: 'AL-TP-RGBCW-60-2116, AL-TP-RGBCW-60-2232'

RGB working for: '50291, 50292' and 11PR38120RGBWH1. No brigtness or White Colortemp yet

RGB (and maybe brightness) working for: '538551010, 538561010, 538552010, 538562010'

Light on/off/dim and fan on/off/low/med/high/full for '52133, 37833' fan and "Driskol 60 inch Fan" and "Zandra". Fan speed is controlled like a light dimmer. Fans are a real pain to support!

On/Off,Brightness: PIR switch (HPDA311CWB)

Outlets (HPKA315CWB) work with on/off on both outputs.
Single outlet (HPPA51CWB and HPPA11AWBA023) models handled correctly

Support for 4 outlet LTS-4G-W strip

Single non-dimming lightswitch (HPSA11CWB) works

Landscape Transformer (HB-200-1215WIFIB) works with on/off on all 3 outputs. System-wide Watts and voltage available as attribute in first output entity

Support for outdoor string lights: HB-10521-HS and maybe HB-17122-HS-WT

Hubspace/Defiant WiFi Deadbolt support: On=Lock, Off=Unlocked . Auto discover does not yet work, so friendlyname is required. Recommend using a template entity to shows up as a lock to Home Assistant, see below. I plan to make a local Bluetooth integration for the lock, but making slow progress.

I would like to update to cloud push, but right now polls the state every minute by default (can be overwritten with scan_interval). Please contact me if good with websockets. The websocket system pushes bad data at first, which messses up the connection. I need a way to ignore that data.

Could use an expert to make everything async. I tried to convert it over, but could not get it working.

_Thanks to everyone having starred my repo! To star it click on the image below, then it will be on top right. Thanks!_

[![Stargazers repo roster for @jdeath/Hubspace-Homeassistant](https://reporoster.com/stars/jdeath/Hubspace-Homeassistant)](https://github.com/jdeath/hubspace-homeassistant/stargazers)

### Installation

Preferred method: Add this repo as a custom repository in HACS. Add the hubspace integration

Manual method: copy the hubspace/ folder in the repo to `<config_dir>/custom_components/hubspace/`.

For either method, add the following entry in your `configuration.yaml`:

Do *not* name your lights in the app the same as a room you have defined or the logic will get tripped up: Office, Bedroom, etc   

the notes in () are comments, REMOVE them from your yaml
```yaml
light:
  - platform: hubspace
    username: your_hubspace_username #(probably your email address)
    password: your_hubspace_password
    debug: false #(use true if want debug output, if you have an unsupported light. set false if not needed)
    friendlynames: #(optional after v1.70)
      - 'BoysLight' #(the name of your light as shown in the app)
      - 'GirlsLight' #(the name of your light as shown in the app)
    roomnames: #(optional)
      - 'BoysRoom' #(the name of your room as shown in the app)
```

freindlynames is optional now, the integration should automatically find most lights. If it does not work, specify the friendlynames. The roomnames is optional, and friendlynames is not needed if used. It will add all devices in the room you made in the hubspace app. No support for this will be given, as added by a PR and not tested by me, but should work.

Friendlyname is listed in the Hubspace App. Click the Device, Click the Gear, Under General will list "Product Name" which is the friendlyname. The Room is the roomname if you prefer to add it that way.

### Troubleshooting
If you are having problems, first try renaming the device name in the App. Do not use spaces in the name of your lights. This code may also fail with names like Office, Bedroom, Fireplace. Make it something unique and not the same as a group. Hopefully this has been fixed, but still could be issues.

Also be sure to check the friendlyname of your light. It must match *exactly* as shown in the app, including uppercase/lowercase. Requiring the case to match may be a recent change by Hubspace

If you have a combo fan/light, it is confirmed to work best if you do not use friendlyname or roomnames. The automatic scan should pick it up. If you must use friendlyname, just add the name of the parent device from the hubspace app. Do not add the seperate names for the light and fan. The integration will make a light.friendlyname (the light) and light.friendlyname_fan (the fan). Better handling is a work in progress.

If you have an outlet or a transfomer, just add the name of the parent device from the hubspace app. Do not add the seperate outlet's/transformer's names. The integration will figure it out and make entities called light.friendlyname_outlet_X or light.freindlyname_transformer_X where X is 1,2,3 depending on how many outputs in has.

### Support for a new model

Please make a github issue if want support for a new model. I will need your help to test.

Easiest way to help is to download the Testhubspace.py (https://raw.githubusercontent.com/jdeath/Hubspace-Homeassistant/main/TestHubspace.py) and run it. It will prompt you for your hubspace username and password. It will output data, which you should copy and paste into the GitHub issue. The output has been anonymized, personal information has been removed or randomized.

If cannot run python3, get the entity loaded in homeassistant. Set debug:true in configuration as shown above. Click on the entity in homeassistant, expand the attributes, and send me the model and debug fields. This information is *not* anonymized. Best to PM me these on the homeassistant forums, as there is semi-private information in them. Send me these fields with the light set to on/off/etc (you may need to use the app). If that doesn't work, I may need better debug logs. Then you can add in your configuration.yaml (not in the hubspace section). Then you email me your homassistant.log 
```
logger:
  default: error
  logs:
    custom_components.hubspace: debug
```
you may already have the top two lines, just need to add the buttom two

### Fan Support
If you have a fan, just add the name of the parent device from the hubspace app. Do not add the seperate names for the light and fan. This is just how it works. Users have said the autoscan works best, so do not use friendlyname or roomnames

Since the fan is implimented as a light with a dimmer, you can use a template to make it appear as a fan. Replace "light.ceilingfan_fan" below with the name of your fan. Confirm you can use the fan before you make the template.  This is optional, only if you really want homeassistant to show the light as a fan entity. This was provided by a user, no support possible. Again, this is optional, only do this once you can control the fan entity normally before hand.
```
# Example configuration.yaml entry
fan:
  - platform: template
    fans:
      living_room_fan:
        friendly_name: "Fan"
        value_template: "{{ states('light.ceilingfan_fan') }}"
        percentage_template: "{{ (state_attr('light.ceilingfan_fan', 'brightness') / 255 * 100) | int }}"
        turn_on:
          service: homeassistant.turn_on
          entity_id: light.ceilingfan_fan
        turn_off:
          service: homeassistant.turn_off
          entity_id: light.ceilingfan_fan
        set_percentage:
          service: light.turn_on
          entity_id: light.ceilingfan_fan
          data_template:
            brightness: "{{ ( percentage / 100 * 255) | int }}"
        speed_count: 4   
```

### Transformer Support
System-wide watt and voltage setting available as attribute in the first output entity. Get watts in lovelace with a card with this entry:
```
- entity: Friendlyname_transformer_1
  type: attribute
  attribute: watts
  ```

## Lock support
The lock "light" will also report the battery status and last operation in the attributues. Optional: If you want to have the light show up as a lock in HA, make a template (but the battery is only available on the light). In your configuration.yaml add this (assumes your light is light.door_lock), click the magnifing glass in top right of HA, then hit >Temp to reload the template entity . Again, this is optional, only do this once you can control the light entity normally before hand.

Belore example assumes the freindlyname of your lock is door_lock (so it true homeassistant name is light.door_lock)
```
lock:
  - platform: template
    name: Back Door Lock
    optimistic: true
    unique_id: "BackDoorLockTemplate123"
    value_template: "{{ is_state('light.door_lock', 'on') }}"
    lock:
      service: light.turn_on
      target:
        entity_id: light.door_lock
    unlock:
      service: light.turn_off
      target:
        entity_id: light.door_lock
```

### Services:
The integration now has the capability to send a service. You may want this if there is a capability that is not supported by the integration. However, do not use this service, if just want to turn lights on/off. Use the standard light.turn_on , light.turn_off for that.
For example, if you want to send the rainbow effect to your rgb light:
```
service: hubspace.send_command
data:
  value: "rainbow"
  functionClass: color-sequence
  functionInstance: custom
target:
  entity_id: light.yourlightname  
  ```
For example, if you want to send the comfort breeze effect to your fan:
```
service: hubspace.send_command
data:
  value: "enabled"
  functionClass: toggle
  functionInstance: comfort-breeze
target:
  entity_id: light.yourlightname  
  ```  
You do this is the Developers Tools -> Services window. The GUI can be used to choose an entity.

How do you find out what the value, functionClass and functionInstance should be? Look at the output of running TestHubspace.py on your device. If you look around, you will see what your light supports in the "values" field. See https://github.com/jdeath/Hubspace-Homeassistant/blob/main/sample_data/11A21100WRGBWH1.json#L686 . The functionInstance is optional, as not all commands require it. There are some example outputs of TestHubspace.py in the sample_data/ directory of the repo. If you cannot run the TestHubspace.py, then turn on debugging (debug: true in your light: setup). Change the setting in the app, and look at the entity attributes in homeassistant. You should be able to see an entry that has the value, functionClass, etc.

You can make a button in lovelace to send any command you want. The lovelace GUI will do most of this, but not fill in the data correctly. A working example to turn on rainbow effect:
```
show_name: true
show_icon: true
type: button
tap_action:
  action: call-service
  service: hubspace.send_command
  data:
    value: "rainbow"
    functionClass: color-sequence
    functionInstance: custom
  target:
    entity_id: light.yourlightname
```
[![Star History Chart](https://api.star-history.com/svg?repos=jdeath/Hubspace-Homeassistant&type=Date)](https://star-history.com/#jdeath/Hubspace-Homeassistant&Date)
