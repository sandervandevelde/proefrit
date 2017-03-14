# Proefrit
Files and information on how to get your Hello World on NB-IoT


Installation quick guide
===
### Hello World! on the NB-IoT Trial network of T-Mobile NL


![](https://github.com/tmnl-iot/proefrit/blob/master/images/iot_logo.png)


![](https://github.com/tmnl-iot/proefrit/blob/master/images/logo%20tmnl.png)

Document properties

Creation date: 9-2-2017

Last update: 21-2-2-17

Author: Eric Barten, Thees Brons


### Contents

- Introduction to NB-IoT       
- Overview of the installation steps     
- Module commands       
- Security certificates for using the API        


### Introduction to NB-IoT

The picture below provides a high level overview of the network set-up of the T-Mobile IoT network:

![](https://github.com/tmnl-iot/proefrit/blob/master/images/iot_network_overview2.png)

It is important to understand that data transfer to and from the device does not happen directly. In between is the T-Mobile IoT platform, which provides an Application Programming Interface (API) towards end-user applications.
This means that in order to send data end-end, devices need to be registered on this IoT platform, and the type of application data needs to be defined.
This is different from a traditional M2M set-up, where the device receives an (internal) IP address from the network and a direct connection can be established.



### Overview of the installation steps

This installation manual describes the steps necessary to connect to the trial IoT network of T-Mobile. As a prerequisite, it is assumed that the test set-up consists of the following elements:

- Laptop/desktop with programmable board plus NB-IoT module connected to com port
- The test SIM-card from T-Mobile installed
- Located in area with NB-IoT coverage (please contact T-Mobile for the latest coverage information or visit the Proefrit section on forum.things.nl ).


>This quick guide only focuses on the software side of setting up an NB-IoT connection. Please review the documentation from your module manufacturer on how to connect the module to your programmable board (the pin layout of most modules is the same as current market models working on GPRS or 3G).



__High level steps for sending your Hello World:__

- Retrieve the IMEI number of your device
- Set up Postman or another API program with the correct environment [variables](https://github.com/tmnl-iot/proefrit/blob/master/postman/iot_portal.postman_environment.json) (certificates, app ID en app key)
- POST Login to API to retrieve access token
- POST Register device to create new device with IMEI and a name
- PUT Set device info to set manufacturer, devicetype and model of your device (__should be exactly the same as the manufacturer, devicetype and model in your device template in Ocean Connect__).
- Use the appropriate AT commands to establish a network attach 
- Use AT+NMGS=<length>,<data> to send your Hello World!
- GET Get device data history to retrieve your Hello World! via the API



### Detailed steps

1. Use your com software to connect to the NEUL module (for example Qcom for Quectel modules).

2. Use the AT+CGSN=1 command to retrieve your IMEI device information.

3. Use the appropriate AT commands to establish a network attach with the T-Mobile IoT network.

>Specific information on module / modem set-up can be found in Chapter &quot;[Module commands](#module-commands)&quot;

4. Log in with your Ocean Connect credentials (provided by email) on [https://160.44.193.219:8843/#/login](https://160.44.193.219:8843/#/login)

5. On your dashboard you will see two main menus: App management and Device Management.

6. Go to App Management &gt; Applications and click on the default app that was created on your account (app name is nbiot\_yourcompany).

7. Copy the different IP addresses that are needed in your set-up (see screenshot, needed in next steps):

- Application Docking IP-address (not necessary, only when running own Ocean Connect instance)
- Devices Docking IP-address (use this IP address in your AT commands)
- North API IP-address ( [https://160.44.193.23:8743](https://160.44.193.23:8743) )

![](https://github.com/tmnl-iot/proefrit/blob/master/images/ocean_connect_screenshot1.png)

8. Go to App Management > Applications > yourAppId > App Resource

![](https://github.com/tmnl-iot/proefrit/blob/master/images/ocean_connect_screenshot2.png)

9. You will see a service and device template which is already created for you, this will only transport RAW data from your device to the API.

>If you want to create your own device and service templates (for example to transfer different data types) use the upload button to upload a zip file containing your service and device template (this zip file needs to contain the files devicetype-capability.json and servicetype-capability.json both in the appropriate folder). This will define which kind of devices you will use and which kind of data they will transmit. Please make sure that the filename is in the right format: devicetype\_manufacturer\_model.zip and that you use exactly the same names in your Set Device Info API call!


>Specific details on how to create your own device and service templates can be found in Huawei IoT Platform Interworking Guide for NB-IoT.doc (available in the repository)

If your devices are able to make a network attach and send the raw data, the final steps are to retrieve this data via the North API.

In this quick guide we will show how this can be done by using the program Postman (download on [https://www.getpostman.com/](https://www.getpostman.com/) , please use the program and not the Chrome plugin), which can be used to test and simulate your application talking to our API.

On Github you can download our [Postman](https://github.com/tmnl-iot/proefrit/tree/master/postman) collection and environment variables, with the example code.

Postman can also convert this collection into for example Python or PHP scripts.

### Setting up Postman

Please follow the steps below to use Postman:

1. Use the file cert.zip containing the security certificates to connect to the Ocean Connect API. For use within Postman, you need to split the certificate into a \*.crt and \*.key file. Instructions on how to do this can be found in the chapter &quot;[Security certificates](#security-certificates-for-using-the-api)&quot;.

2. Import the TMNL postman collection [NB-IOT TMNL.postman\_collection.json](https://github.com/tmnl-iot/proefrit/blob/master/postman/NB-IOT%20TMNL.postman_collection.json)

![](https://github.com/tmnl-iot/proefrit/blob/master/images/postman_screenshot1.png)

3. Now select manage environments &gt; import &gt; select the environment json file

![](https://github.com/tmnl-iot/proefrit/blob/master/images/postman_screenshot2.png)

4. Click on IOT portal to edit the variables, enter your appID and Secret (send in email)

![](https://github.com/tmnl-iot/proefrit/blob/master/images/postman_screenshot3.png)

5. You should now be able to use the POST Login, to get an access token from the API. This access token you can enter into the environment variables (our Postman collection has a script to do this automatically).

6. With the access token entered, you should be able to use:

- Get Devices – in case you already have devices registered

- Register device – to register the IMEI (please enter the IMEI in the fields VerifyCode and NodeID)

- SetDeviceInfo – to set additional info of your device, like its name, manufacturer and model. __Please make sure to use exactly the same names of manufacturer, devicetype and model as in the device template on Ocean Connect!__ 

In the current trial set-up, you need to switch off SSL certificates check in the general Postman settings.

7. To send data from the device, use the AT+NMGS=+NMGS=&lt;length&gt;,&lt;data&gt;

- &lt;length&gt; Decimal length of message.

- &lt;data&gt; Data to be transmitted in hexstring format.

And collect the data via GET device data history


### Module commands

All AT commands are based on the Neul chipset. The complete Neul documentation can be found on Github.

To check hardware and firmware version:

```
Connect USB to Serial;

Use 9600 Baud, 8 bits , 1 stop, Parity: None

Reset;                   Nuel Ok
Switch On;               AT+CFUN=1
Manufacturer;            AT+CGMI
Model;                   AT+CGMM
Firmware;                AT+CGMR
IMEI;                    AT+CGSN=1
IMSI;                    AT+CIMI
```

Note: Please verify the firmware revision number anything equal or higher than SP11 will not work on the trial network.

To establish network attach:
```
Connect USB to Serial;

9600 Baud, 8 bits , 1 stop, Parity: None

Reset;          Nuel Ok
Switch On;      AT+CFUN=1
Set IP;         AT+NCDP=172.16.0.36
Set APN;        AT+CGDCONT=1,"IP", "parking.donas.nb-iot.com"
Attach;         AT+COPS=1,2,"12345"
Wait for IP;    AT+CGPADDR=1
```

Note: Newer Firmware versions (SP8) require a slightly different approach.

By default these modules have auto connect configured. The CDP must be set before connection to the network is performed. (From the manual; Set the module to minimum functionality (issuing AT+CFUN=0 command) before sending the NCDP command).

So there are two steps:

1). Turn off Autoconnect;

AT+NCONFIG=AUTOCONNECT,FALSE

2). Perform the connect sequence

```
Reset ;                 Nuel Ok
Set IP;                 AT+NCDP=172.16.0.36
Set APN;         AT+CGDCONT=1,"IP", "parking.donas.nb-iot.com"
Switch On;        AT+CFUN=1
Attach;                 AT+COPS=1,2,"12345"
Wait for IP;         AT+CGPADDR=1
```

Please be aware that these settings only apply for the T-Mobile trial network, the production network will have different APN and IP addresses.


### Security certificates for using the API

In your account info email the file cert.zip was attached. Use the file outgoing.CertwithKey.pkcs12

- Visit [https://www.sslshopper.com/ssl-converter.html](https://www.sslshopper.com/ssl-converter.html) en select the pkcs12 file.

- Choose current certificate type PKCS12

- Choose Type To Convert To for PEM

- Enter as PFX password the keystore password from the account set-up email

- Click convert certificate.
- A file is downloaded with the PEM extension.

- Make a copy of this file and give one copy the new extension .key and the other the extension .crt

- In Postman, click in the top right on Settings &gt; Certificates

- Choose Add certificate enter the following information and files:
the North API IP address and port number from the email
upload the CRT and KEY files
Passphrase is the truststore password from the email

Postman now has the right settings and certifcates in order to connect to the API.
