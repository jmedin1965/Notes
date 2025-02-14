# zigbee2mqtt
Created Tuesday 17 September 2024

Adapter that greg bought SMLIGHT SLZB-06
----------------------------------------

I bought the POE SMLIGHT SLZB-06 zigbee adaptor so I can put it anywhere in the house as it also does wifi (which I didn't know it did)

Just plugged it in and it worked!
<https://smlight.tech/manual/slzb-06/>


How to restart zigbee2MQTT after a Home Assistant Restart
---------------------------------------------------------

REF: <https://community.home-assistant.io/t/how-to-restart-zigbee2mqtt-after-a-home-assistant-restart/695922>

Something like this:

	- id: '1565194745362'
	  alias: Status starting HA
	  trigger:
	  - event: start
		platform: homeassistant
	  action:
	  - service: hassio.addon_restart
		data:
		  addon: 45df7312_zigbee2mqtt

