# Undermount AC ESPHome HVAC Controller

Originally written by Mike Goubeaux, this fork is heavily modified for heat/cool with a commercially available hydronic radiant heat system for vans and the UndermountAC evaporator with heat coil.
NOT for Rixen controls, Mike has an excellent standalone thermostat for them in his github [https://github.com/mikegoubeaux/rixens-esphome](https://github.com/mikegoubeaux/rixens-esphome).

## NOTE: USE AT YOUR OWN RISK, this is preliminary and under active development

Please visit: [https://mikegoubeaux.github.io/UndermountAC](https://mikegoubeaux.github.io/UndermountAC) for details on the original system and hardware provided by UndermountAC.

The specific UndermountAC hardware used in this system is the 12V ducted evaporator system [https://undermountac.com/collections/hvac-ducted/products/12vacducted](https://undermountac.com/collections/hvac-ducted/products/12vacducted) and the Heat addition module with 4 way valve [https://undermountac.com/collections/heat-controls/products/heat-addition-module-relay-module-4-way-valve](https://undermountac.com/collections/heat-controls/products/heat-addition-module-relay-module-4-way-valve) and the ESPHome thermostat controller [https://undermountac.com/collections/thermostats/products/esphome-thermostat-controller-12-30v-dc](https://undermountac.com/collections/thermostats/products/esphome-thermostat-controller-12-30v-dc).

The NextGen thermostat is not used, it is replaced by the ESPHome thermostat controller (I ordered it with the system along with heat capable option linked above).  However, the RS-485 cable from the thermostat is used (see below).  

The Heat addition module is used for all heating system relays due to system configuration and the need for three relays as only two extra are available on the ESPHome thermostat.  One relay controls the 4 way valve, the second controls the Espar boiler which also controls the main circulator pump (dry contact, closed for calling for heat) and the third controls the floor circulator pump (12V).

The Heat addition module connects to the ESPHome thermostat controller RS-485 port with the cable provided with the heat capable NextGen thermostat.  Disassemble the thermostat and remove the cable from the connector and the back of the thermostat (there is a grommet that the cable goes through that can be removed to get the connector through).

The floor temp sensor used is a One Wire (Dallas DS18B20).  It was installed in the floor alongside the NTC thermistor that the radiant heat system kit provided.  The One Wire device was selected as it was on-hand and known good.  An SHT3xx device can also be used, however it will need a separate I2C bus unless one with a different I2C address (chip select) can be procured.  

For retrofitting an existing floor the ESPHome integration [https://esphome.io/components/sensor/ntc.html](https://esphome.io/components/sensor/ntc.html) can be used with appropriate hardware (a search for "ntc esphome" gives some results) to add to the UndermountAC ESPHome box.  

Note that the ESPHome thermostat will need to be modified (involves soldering) for any floor sensor except and SH3xx style that has a different I2C address than the one used by UndermountAC.

For heat, the LED now pulsates Red, that can be customized by changing the code at the end of the file.  

10/4/2025 - these changes are even more specific to my installation than the rest of this project:

A new relay (controlled through Home Assistant) has been set up to interrupt power to the compressor (used as a workaround to false overvoltage events the compressor control board is giving me).

Two temparature sensors were added to the existing onboard_temperature sensor and the median of the three used for temperature control.

Evaporater in and out temperatures are now measured.  Future use will be to allow better control of the blower speed while preventing freeze-up.

A boiler supply temperature sensor was also added.

All of the new temperature sensors are onw-wire sensors.

Questions, comments, improvements?  Please post in discussions using the link above.
