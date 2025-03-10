# Undermount AC ESPHome HVAC Controller

Originally written by Mike Goubeaux, this fork is heavily modified for heat/cool with a commercially available hydronic radiant heat system for vans and the UndermountAC evaporator with heat coil.
NOT for Rixen controls, Mike has an excellent standalone thermostat for them in his github [https://github.com/mikegoubeaux/rixens-esphome](https://github.com/mikegoubeaux/rixens-esphome).

## NOTE: USE AT YOUR OWN RISK, this is preliminary and under active development

Please visit: [https://mikegoubeaux.github.io/UndermountAC](https://mikegoubeaux.github.io/UndermountAC) for details on the original system and hardware provided by UndermountAC.

The specific UndermountAC hardware used in this system is the 12V ducted evaporator system [https://undermountac.com/collections/hvac-ducted/products/12vacducted](https://undermountac.com/collections/hvac-ducted/products/12vacducted) and the Heat addition module with 4 way valve [https://undermountac.com/collections/heat-controls/products/heat-addition-module-relay-module-4-way-valve](https://undermountac.com/collections/heat-controls/products/heat-addition-module-relay-module-4-way-valve) and the ESPHome thermostat controller [https://undermountac.com/collections/thermostats/products/esphome-thermostat-controller-12-30v-dc](https://undermountac.com/collections/thermostats/products/esphome-thermostat-controller-12-30v-dc).

The floor temp sensor used is a One Wire (Dallas DS18B20).  It was installed in the floor alongside the NTC thermistor that the radiant heat system kit provided.  The One Wire device was selected as it was on-hand and known good.  An SHT3xx device can also be used, however it will need a separate I2C bus unless one with a different I2C address (chip select) can be procured.    

For retrofitting an existing floor the ESPHome integration [https://esphome.io/components/sensor/ntc.html](https://esphome.io/components/sensor/ntc.html) can be used with appropriate hardware (a search for "ntc esphome" gives some results) to add to the UndermountAC ESPHome box.  

For heat, the LED now pulsates Red, that can be customized by changing the code at the end of the file.  
