What The MAC?
=============

It shouldn't be surprising that there are a lot of radio signals floating in the ether around us. What would happen if you capture some of the information in them? Could you use it to determine who is where and what they are doing?

We are trying to do this with What the MAC, a project that sets up a few WiFi listening devices at the WhereConf, collects data about what WiFi packets we collect, and stores it in Amazon S3. From there we hope to build some tools to manipulate and visualize this data.

Getting the Data
----------------

The data is on an S3 bucket, and is available to the public. You can download it with any S3 tool, or create EMR jobs that use the data as an input.

The data is in bunch of files here: s3://data.controlgroup.com/wtm/where2012/

The files are gzipped, so keep that in mind when you work with them. Enjoy!

The Data Format
---------------

The data is stored in files that are organized by year/month/day/hour. The time is in UTC. The files contain JSON data with one line representing a packet that an access point collected.

Here's an example of the data broken out into many lines:


```javascript
{
  "timestamp": 1332772975.117325,
  "metric": "SignalStrength",
  "value": -42,
  "tags": {
    "DAOUI": "unknown",
    "NodeName": "access_point_2",
    "DAHash": "18e2075d3fd4e865bc071ad258bfd590",
    "SAOUI": "Hon Hai Precision Ind. Co.,Ltd.",
    "BSSIDOUI": "Cisco Systems",
    "SAHash": "ee5fdea86ccbdf68b477fbc80bc9f0a4",
    "BSSIDHash": "f790ff24dd2c672436d86188fe530083"
  }
}
```

The timestamp is the UNIX epoch time and the value is the signal strength in dBm. In the tags block we keep information about the DA (destination address), SA (source address), and BSSID (the WiFi AP). We hash the addresses if we find them and we calculate the OUI if we can so you know who makes the device. 

The NodeName is the name of the access point that found the packet. We will be providing a map of where these devices are when we get closer to the conference.

A note about privacy
--------------------

Yeah this is a bit creepy. We tried to make it difficult to identify individual devices by salting and hashing the MAC addresses. We will provide a webservice to look up your MAC address in the data. 

Additionally, we are not decrypting any data. Most of this information appears to come from devices that are sending broadcast packets to find WiFi networks that they have joined in the past.
