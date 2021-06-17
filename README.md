# Climate_IP - IP based VRF climate device for Home Assistant
Implementation of ClimateDevice for controlling IP based AC units.
This component is able to work with any AC unit which can be controlled with REST API.
At this moment it is configured to work with:
* Panasonic VRF CZ-FUN2

Support for any unit working with REST API can be easily added via YAML configuration file.

## Installation
1. Download all files from repo to newly created folder
2. move folder custom_components/climate_ip to your <ha_configuration_folder>
3. In __configuration.yaml__ file add section:
    * For new generation units (REST API, port 8888)
        ```
        - platform: climate_ip
          name: "Panasonic AC"
          config_file: 'panasonic_vrf.yaml'
          ip_address: 192.168.1.100
          token: ''
          cert: ''
          poll: true
          debug: false
          device_uid: '4'
        ```


## Configuration
1. Configuration parameters:

    | Param name        | description           |  obligatory         |
    | ------------- |-------------|-------------|
    | config_file      | YAML configuration filename |Yes
    | ip_address      | Device IP address (e.g. 192.178.1.200) |Yes
    | token           | Access token to the device        |Yes
    | cert_file   | certificate file name (default: ac14k_m.pem, Use __None__ to not use certification) | Usually Yes
    | mac      | MAC address of device | For 2878 devices
    | name      | Device name (by default this value is taken from YAML config file) | No
    | controller    | Controller type to use (default, and the only one for now: yaml)  | No
    | poll      | Enable/disable state polling. Default: Taken from YAML config. Enabled for old gen devices | No
    | debug      | Enable/disable more debugs. Default: False | No
    | device_uid | This device id of VRF's ids
2. You need to have your device __token__. Please use google to find a way to get it :-) 
2. YAML configuration
You can easily add, remove or modify any device paramter to meet device capabilities.
I hope that more detailed specification will be created *soon* :-D

## Using
### Default functionality
This component implements Home Assistant ClimateDevice class. Functionality enabled in HA by default:
* turn device on/off
* select fan mode
* select swing mode
* select target temperatures (min, max and target)
### Device specific functions
Device specific functionality is added as extra service called **climate.climate_ip_set_property**.
Every device attribute can be set using this service with proper params.

```
{
  "entity_id" : "climate.salon_ac",
  "power" : "on",
  "purify" : "on",
  "special_mode" : "comfort"
}
```
### Switches for special functions
To make controllig device as easy as possible user can create template switches for operations defined as __Switch__ (please see configuration file). 
Below is an example of template switch for Purify option
```
switch:
  - platform: template
    switches:
      purify:
        value_template: "{{ is_state_attr('climate.salon_ac', 'purify', 'on') }}"
        turn_on:
          service: climate.climate_ip_set_property
          data:
            entity_id: climate.salon_ac
            purify: 'on'
        turn_off:
          service: climate.climate_ip_set_property
          data:
            entity_id: climate.salon_ac
            purify: 'off'
```

