# class WLAN
This class provides a driver for the WiFi network processor in the module. Example usage:

```python
import network
import time
# setup as a station
wlan = network.WLAN(mode=network.WLAN.STA)
wlan.connect('your-ssid', auth=(network.WLAN.WPA2, 'your-key'))
while not wlan.isconnected():
    time.sleep_ms(50)
print(wlan.ifconfig())

# now use socket as usual
```

### Quick Usage Example

```python
import machine
from network import WLAN

# configure the WLAN subsystem in station mode (the default is AP)
wlan = WLAN(mode=WLAN.STA)
# go for fixed IP settings (IP, Subnet, Gateway, DNS)
wlan.ifconfig(config=('192.168.0.107', '255.255.255.0', '192.168.0.1', '192.168.0.1'))
wlan.scan()     # scan for available networks
wlan.connect(ssid='mynetwork', auth=(WLAN.WPA2, 'my_network_key'))
while not wlan.isconnected():
    pass
print(wlan.ifconfig())
```

### Constructors

#####<class><i>class</i> network.WLAN(id=0, ...)</class>

Create a WLAN object, and optionally configure it. See init for params of configuration.

{% hint style='info' %}
The WLAN constructor is special in the sense that if no arguments besides the `id` are given, it will return the already existing WLAN instance without re-configuring it. This is because WLAN is a system feature of the WiPy. If the already existing instance is not initialised it will do the same as the other constructors an will initialise it with default values.
{% endhint %}

### Methods

#####<function>wlan.init(mode, * , ssid=None, auth=None, channel=1, antenna=None, power_save=False, hidden=False, bandwidth=HT40)</function>

Set or get the WiFi network processor configuration.

Arguments are:

- `mode` can be either <constant>WLAN.STA</constant>, <constant>WLAN.AP</constant> or <constant>WLAN.STA_AP</constant>.
- `ssid` is a string with the SSID name. Only needed when mode is <constant>WLAN.AP</constant>.
- `auth` is a tuple with (sec, key). Security can be `None`, <constant>WLAN.WEP</constant>, <constant>WLAN.WPA</constant> or <constant>WLAN.WPA2</constant>. The key is a string with the network password. If `sec` is <constant>WLAN.WEP</constant> the key must be a string representing hexadecimal values (e.g. `ABC1DE45BF`). Only needed when mode is <constant>WLAN.AP</constant>.
- `channel` a number in the range 1-11. Only needed when mode is <constant>WLAN.AP</constant>.
- `antenna` selects between the internal and the external antenna. Can be either
<constant>WLAN.INT_ANT</constant>, <constant>WLAN.EXT_ANT</constant>.
With our development boards it defaults to using the internal antenna, but in
the case of an OEM module, the antenna pin (`P12`) is not used, so it’s free to be
used for other things.
- `power_save` enables or disables power save functions in STA mode.
- `hidden` only valid in <constant>WLAN.AP</constant> mode to create an access point with a hidden SSID when set to `True`.

- ``bandwidth`` is the Bandwidth to use, either 20MHz or 40 MHz , use `HT20` or `HT40`

For example, you can do:

```python
# create and configure as an access point
wlan.init(mode=WLAN.AP, ssid='wipy-wlan', auth=(WLAN.WPA2,'www.wipy.io'), channel=7, antenna=WLAN.INT_ANT)
```

or:

```python
# configure as an station
wlan.init(mode=WLAN.STA)
```

#####<function>wlan.deinit()</function>

Disables the WiFi radio.

#####<function>wlan.connect(ssid, * , auth=None, bssid=None, timeout=None, ca_certs=None, keyfile=None, certfile=None, identity=None, hostname=None)</function>

Connect to a wifi access point using the given SSID, and other security parameters.

- `auth` is a tuple with `(sec, key)`. Security can be `None`, <constant>WLAN.WEP</constant>, <constant>WLAN.WPA</constant>, <constant>WLAN.WPA2</constant> or <constant>WLAN.WPA2_ENT</constant>. The key is a string with the network password. If `sec` is <constant>WLAN.WEP</constant> the key must be a string representing hexadecimal values (e.g. `ABC1DE45BF`). If `sec` is <constant>WLAN.WPA2_ENT</constant> then the `auth` tuple can have either 3 elements: `(sec, username, password)`, or just 1: `(sec,)`. When passing the 3 element tuple, the` keyfile` and `certifle` arguments must not be given.
- `bssid` is the MAC address of the AP to connect to. Useful when there are several APs with the same SSID.
- `timeout` is the maximum time in milliseconds to wait for the connection to succeed.
- `ca_certs` is the path to the CA certificate. This argument is not mandatory.
`keyfile` is the path to the client key. Only used if `username` and `password` are not part of the `auth` tuple.
- `certfile` is the path to the client certificate. Only used if `username` and `password` are not part of the `auth` tuple.
- `identity` is only used in case of <constant>WLAN.WPA2_ENT</constant> security.

- ``hostname`` is the name of the host connecting to the AP. Max length of name string is 32 Bytes

#####<function>wlan.scan([ssid=NULL, bssid=NULL, channel=0, show_hidden=False, type=WLAN.SCAN\_ACTIVE, scantime=120ms])</function>

Performs a network scan and returns a list of named tuples with (ssid, bssid, sec, channel, rssi). When no config args passed scan will be performed with default configurations.

Note: For Fast scan mode ssid/bssid and channel should be 

- ``ssid`` : If the SSID is not NULL, it is only the AP with the same SSID that can be scanned.

- ``bssid`` : If the BSSID is not NULL, it is only the AP with the same BSSID that can be scanned. The bssid is given as 6 Hexadecimal bytes literals (i.e b'\xff\xff\xff\xff\xff\xff')

- ``channel`` : If “channel” is 0, there will be an all-channel scan; otherwise, there will be a specific-channel scan.

- ``show_hidden`` : If “show_hidden” is 0, the scan ignores the AP with a hidden SSID; otherwise, the scan considers the hidden AP a normal one.

- ``type`` : If “type” is `WLAN.SCAN_ACTIVE`, the scan is “active”; otherwise, it is a “passive” one.

	- Active Scan is performed by sending a probe request. The default scan is an active scan

	- Passive Scan sends no probe request. Just switch to the specific channel and wait for a beacon. 

- ``scantime`` : 

	This field is used to control how long the scan dwells on each channel.
 	For passive scans, scantime=[int] designates the dwell time for each channel.
 
	For active scans, dwell times for each channel are listed below. 
	scantime is given as a tuple for min and max times (min,max)

	{% hint style='danger' %}
	
	min=0, max=0: scan dwells on each channel for 120 ms.
	
	min>0, max=0: scan dwells on each channel for 120 ms.
	
	min=0, max>0: scan dwells on each channel for max ms.
	
	min>0, max>0: The minimum time the scan dwells on each channel is min ms. If no AP is found during this time frame, the scan switches to the next channel. Otherwise, the scan dwells on the channel for max ms.If you want to improve the performance of the the scan, you can try to modify these two parameters.
	
	{% endhint %}


#####<function>wlan.disconnect()</function>

Disconnect from the WiFi access point.

#####<function>wlan.isconnected()</function>

In case of STA mode, returns `True` if connected to a WiFi access point and has a valid IP address. In AP mode returns `True` when a station is connected, `False` otherwise.

#####<function>wlan.ifconfig(id=0, config=['dhcp' or configtuple])</function>

When `id` is 0, the configuration will be get/set on the Station interface. When `id` is 1 the configuration will be done for the AP interface.

With no parameters given returns a 4-tuple of `(ip, subnet_mask, gateway, DNS_server)`.

If `dhcp` is passed as a parameter then the DHCP client is enabled and the IP params are negotiated with the AP.

If the 4-tuple config is given then a static IP is configured. For instance:

```python
wlan.ifconfig(config=('192.168.0.4', '255.255.255.0', '192.168.0.1', '8.8.8.8'))
```

#####<function>wlan.mode([mode])</function>

Get or set the WLAN mode.

#####<function>wlan.ssid([ssid])</function>

Get or set the SSID (Set SSID of AP).

In case if mode = `WLAN.STA` this method can get the ssid of AP the board is connected to.

In case of mode = `WLAN.AP` this method can get the ssid of the board's own AP.

In case of mode = `WLAN.STA_AP` this method can get the ssid of board's own AP plus the AP the STA is connected to in form of a tuple:

 _\<SSID of connected AP, SSID of own AP\>_

#####<function>wlan.auth([auth])</function>

Get or set the authentication type when in AP mode.

#####<function>wlan.channel([channel])</function>

Get or set the channel (only applicable in AP mode).

#####<function>wlan.antenna([antenna])</function>

Get or set the antenna type (external or internal).

#####<function>wlan.mac()</function>

Get a 6-byte long `bytes` object with the WiFI MAC address.

#####<function>wlan.bandwidth()</function>

Set the bandwidth of the wifi, either 20 MHz or 40 MHz can be configured, use constants `HT20` or `HT40`

#####<function>wlan.hostname()</function>

Set the Host name of the device connecting to the AP in case of Wifi `mode=WLAN.STA`, in case of `mode=WLAN.AP` this is the name of the host hosting the AP. Max length of name string is 32 Bytes

#####<function>wlan.ap\_sta\_list()</function>

Gets an info list of all stations connected to the board's AP.

Info returned is a list of tuples containning ([mac address of connected STA], [average rssi value], [Wlan protocol enabled by STA]).

Protocol types are either : `WLAN.PHY_11_B`, `WLAN.PHY_11_G`, `WLAN.PHY_11_N` or `WLAN.PHY_LOW_RATE`

### Constants

<constant>WLAN.STA</constant> <constant>WLAN.AP</constant> <constant>WLAN.STA_AP</constant>

WLAN mode

<constant>WLAN.WEP</constant> <constant>WLAN.WPA</constant> <constant>WLAN.WPA2</constant> <constant>WLAN.WPA2_ENT</constant>

WLAN network security

<constant>WLAN.INT_ANT</constant> <constant>WLAN.EXT_ANT</constant>

Antenna type

<constant>WLAN.HT20</constant> <constant>WLAN.HT40</constant>

WLAN Bandwidth

<constant>WLAN.PHY\_11\_B</constant> <constant>WLAN.PHY\_11\_G</constant> <constant>WLAN.PHY\_11\_N</constant> <constant>WLAN.PHY\_LOW\_RATE</constant>

WLAN protocol

<constant>WLAN.SCAN\_ACTIVE</constant> <constant>WLAN.SCAN\_PASSIVE</constant>

Scan Type
