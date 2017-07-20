# OpenDXL-ATD-Forcepoint
This integration is focusing on the automated threat response with McAfee ATD, OpenDXL and Forcepoint Firewalls (SMC).
McAfee Advanced Threat Defense (ATD) will produce local threat intelligence that will be pushed via DXL. An OpenDXL wrapper will 
subscribe and parse IP indicators ATD produced and will automatically update Firewall rules and push new configuration to selected Firewalls.

![51_atd_forcepoint](https://cloud.githubusercontent.com/assets/25227268/25074447/0f2ba064-22fb-11e7-9b65-6b5159374aa7.PNG)

## Component Description

**McAfee Advanced Threat Defense (ATD)** is a malware analytics solution combining signatures and behavioral analysis techniques to rapidly 
identify malicious content and provides local threat intelligence. ATD exports IOC data in STIX format in several ways including the DXL.
https://www.mcafee.com/in/products/advanced-threat-defense.aspx

**Forcepoint Next Generation Firewalls**  combines fast, flexible networking with industry-leading security to connect and protect 
people and the data they use throughout diverse, evolving enterprise networks. https://www.forcepoint.com/product/network-security/forcepoint-ngfw

## Prerequisites
McAfee ATD solution (tested with ATD 3.8)

Download the [Latest Release](https://github.com/mohl1/OpenDXL-ATD-Forcepoint/releases)
* Extract the release file

OpenDXL Python installation
1. Python SDK Installation ([Link](https://opendxl.github.io/opendxl-client-python/pydoc/installation.html))
    Install the required dependencies with the requirements.txt file:
    ```sh
    $ pip install -r requirements.txt
    ```
    This will install the dxlclient, and requests modules.
2. Certificate Files Creation ([Link](https://opendxl.github.io/opendxl-client-python/pydoc/certcreation.html))
3. ePO Certificate Authority (CA) Import ([Link](https://opendxl.github.io/opendxl-client-python/pydoc/epocaimport.html))
4. ePO Broker Certificates Export ([Link](https://opendxl.github.io/opendxl-client-python/pydoc/epobrokercertsexport.html))

Forcepoint SMC (tested with SMC 6.1)

## Configuration
McAfee ATD receives files from multiple sensors like Endpoints, Web Gateways, Network IPS or via Rest API. 
ATD will perform malware analytics and produce local threat intelligence. After an analysis every indicator of comprise will be published 
via the Data Exchange Layer (topic: /mcafee/event/atd/file/report). 

### atd_subscriber.py
The atd_subscriber.py receives DXL messages from ATD, filters out discovered IP's and loads smc.py.

Change the CONFIG_FILE path in the atd_subscriber.py file.

`CONFIG_FILE = "/path/to/config/file"`

### Forcepoint SMC
[Forcepoint SMC API Reference Guide](https://www.websense.com/content/support/library/ngfw/v61/rfrnce/ngfw_610_rg_smc-api_a_en-us.pdf)

Before Firewall Rules can be updated via API it is neccessary to enable the SMC API and to create an API Client element.

### smc.py
The smc.py receives only the discovered malicious IP's and will use API's to update Firewall rules / IP lists.

Change the url and auth variables.

`url = "http://smcurl:8082/6.1/"`

`auth = "authkey"`

The script will:

1. create a new api session 
2. login
3. check if the IP list exists already and create it if it doesn't
4. get the element and find out content URI
5. download the content for the IP list and add new ip
6. upload content for the IP list
7. push rule to a selected Firewall

Don't forget to create a new Firewall rule related to the IP list.

![52_atd_forcepoint](https://cloud.githubusercontent.com/assets/25227268/25074567/bcc32aa0-22fe-11e7-8359-75a4c93e8634.PNG)

## Run the OpenDXL wrapper
> python atd_subscriber.py

or

> nohup python atd_subscriber.py &

## Summary
With this use case, ATD produces local intelligence that is immediatly updating cyber defense countermeassures like the 
Forcepoint Next Generation Firewalls with malicious IP's.
