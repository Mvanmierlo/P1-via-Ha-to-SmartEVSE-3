# P1-via-Ha-to-SmartEVSE-3
<span style="color:red">
I push my SmartMeter (DSMR5) telegrams to Home Assistant with the P1 reader ethernet from Marcel Zuidwijk.<br /> 
As this hardware is E20 based and not ESP, thus not ESPHOME compatible I am not able to do this at this stage.<br />
</span>
<br />
<br />

The P1 telegram publishes information in regard of the state of the Grid including the load in Amperes per Phase with the vallues :
* 1-0:31.7.0(003A)
* 1-0:51.7.0(001A)
* 1-0:71.7.0(003*A)

Challenge here is that these vallues are allways positive, there is no difrentiation in regard of usage (get power from the grid) or (solar)production (push power to the grid) which from a metering perspectig should represented as a positive reading when usage and negative when producing.
This is also the way the SmartEVSE-3 expects the data to be pushed via the API (when using the SERKRI firmware).

As I am not using ESPHome capable hardware (where this conversion can be done in the ESPHome code and pushed directly via an API POST to the SmartEVSE) I need to build my conversion and API POST in HomeAssistant.

I am using these vallues of the P1 to determine if there is production or usage :

* 1-0:21.7.0(00.000kW)
* 1-0:41.7.0(00.000kW)
* 1-0:61.7.0(00.000kW)
* 1-0:22.7.0(00.649kW)
* 1-0:42.7.0(00.312kW)
* 1-0:62.7.0(00.872kW)

The 21,41,61 values being consumption from the grid and the 22,42,62 values being production to the grind
So simply said if the 1-0:22.7, 1-0:42.7 or 1-0:62.7 vallues are bigger than 0 we have production and I want to make my 1-0:31.7.0, 1-0:51.7.0 and 1-0:71.7.0 negative.

That's exactly what my config in configuration.yaml does and I added the API post there too.
The automations.yaml is calling the API post every 3 seconds so that my SmartEVSE is being updated every 3 seconds.

