# pubScribe
Light weight middleware layer to multiple underlying databases and publish / subscribe software

Copyright(C) 2021, BrucesHobbies,
All Rights Reserved

# PubScribe(TM) Project Overview
pubScribe provides a very light weight middleware layer to applications to free them of the specific database interface function calls and allow the ultimate end users the freedom to choose their database and/or publish/subscribe middleware.

It was developed in response to requests of users wanting a different underlying database or publish / subscribe layer. The intent is to be agnostic to the particular underlying software layers to enable a larger user base for the application layer software.

Features:
- Data logging (typically a time series of records)
  - Comma Separated Variable (CSV)
  - MQTT (publish / subscribe message queue)
    - OpenHab
    - Home Assistant
    - Domoticz
  - influxDB
  - SQL (multiple variations to be added)
  - Future options
    - IFTT
    - PubNub
    - Twilio
    - Cellular
    - APRS

- Send status and alert messages via email or SMS to a cell phone which is useful or monitoring home/vehicle/out-buildings
  - Email
  - SMS text message to your cell phone

PubScribe looks at the data type being published and provides the appropriate conversion from dictionary, list, or string to the native types supported by the underlying database or publish / subscribe software.

# Software Installation
## Step 1: Setup the Raspberry Pi Operating System.
Here are the instructions to install the Raspberry Pi Operating System.
[Raspberry Software Install Procedure](https://www.raspberrypi.org/software/operating-systems/)

Before continuing make sure your operating system has been updated with the latest updates.

    sudo apt-get update
    sudo apt-get full-upgrade
    sudo reboot now

## Step 2: Download database or publish/subscribe software such as MQTT

For example for MQTT:

    sudo apt-get install mosquitto             # local broker
    sudo apt-get install mosquitto-clients     # local publish-subscribe
    sudo systemctl enable mosquitto            # Enable the broker and allow it to auto-start after reboot
    sudo systemctl status mosquito             # Check status
    sudo pip3 install paho-mqtt                # Python interface to MQTT

## Step 3: Download PubScribe software
There are 2 python files: pubScribe, and sendEmail. To get a copy of the source files type in the following git command assuming you have already installed git:

    git clone https://github.com/BrucesHobbies/pubScribe

## Step 3: Software Configuration
If you desire, you may edit pubScribe.py and change the user configuration section at the beginning of the file.

    #
    # USER CONFIGURATION SECTION
    # Select one or more options to enable
    #
    CSV_FILE_ENABLED  = 1
    EMAIL_SMS_ENABLED = 1
    MQTT_ENABLED      = 0
    INFLUX_DB_ENABLED = 0

To connect

    connectPubScribe()

To add a record

    def pubRecord(dest, topic, data, hdr="")

Where dest is one or more of the following: MQTT, CSV_FILE, EMAIL_SMS, INFLUX_DB

topic is a string in the format of 'topic/subtopic', etc.
For alerts use 'topic/subtopic/alert'
For status use 'topic/subtopic/status'

Here are some examples:

    topic = "Sensor"
    dictVar = {"Current": 6.4,"Power": 3.2,"Energy": 4.5,"PF": 0.95}
    pubRecord(CSV_FILE, topic, dictVar)

    topic = "Sensor/Alert"
    listVar = [ 6.4, 3.2, 4.5, 0.95]
    pubRecord(CSV_FILE, topic, listVar,"Col1,Col2,Col3,Col4")

    fmtStr = "{:.1f},{:.2f},{:.3f},{:.4f}"
    addTopicFmtStr(topic, fmtStr)
    pubRecord(CSV_FILE, topic, listVar, "Col1,Col2,Col3,Col4")

    topic = "Sensor/topic"
    sVar = '6.4, 3.2, 4.5, 0.95'
    pubRecord(CSV_FILE, topic, sVar, "Col1,Col2,Col3,Col4")


To disconnect

    disconnectPubScribe()

## Step 4: Gmail Configuration (optional)
UPDATE: It has been reported that you don't actually need to lower the security settings for the RPi to access gmail. Gmail now allows you to set up a one-time password for low level security apps using gmail and still have TFA enabled. We still recommend a separate email account for your RPi for extra protection.
You can use Google Gmail to send status and alert emails. Others have also used Microsoft Live/Outlook/Hotmail, Yahoo, Comcast, ATT, Verizon, and other email servers. 
Currently, status and alert messages are sent by email which can also be sent as an SMS text to your cell phone.  Gmail works with Python on the Raspberry Pi if you set the Gmail security settings to low. As such, you can create a separate Gmail account to send messages from. Under your Gmail account settings you will need to make the following change to allow "Less secure app access".

![Figure 1: Gmail Less Secure App Access](https://github.com/BrucesHobbies/pubScribe/blob/main/figures/Figure1.png)

Then click on "Turn on access (not recommended)" by moving the slider to ON. Then click the back arrow.

![Figure 2: Enable Less Secure App Access](https://github.com/BrucesHobbies/pubScribe/blob/main/figures/Figure2.png)

# Running The Program From A Terminal Window 
When your first email is sent at program startup, Google will ask you to confirm that it is you. You will need to sign into the device email account that you created and go to the critical security email that Google sent you and confirm you originated the email before Google will allow emails to be sent from your Python program.

Once you have created an account, start the pubScribeâ„¢ program.   Type:

    python3 pubScribe.py

The first time the program starts up it will ask you for your email user id and password for the account that you just created to work with this program. The password will be saved to a file called cfgData.json.  Please be careful to not send that file to others. If you ever change your password you will need to delete emailCfg.json so that the program will ask you for your password again. 

    Enter sender's device email userid (sending_userid@gmail.com):
    sending_userid@gmail.com

    Enter password:
    password

Next the program will ask you for the recipient email address.  This can either be the same email address, your primary email address or your SMS cell number carrierâ€™s gateway.  To email an SMS to your cell phone construct the recipient email depending on your cell phone carrier:
Carrier|Format
-------|-------|
AT&T|number@txt.att.net|
Verizon|number@vtext.com|
Sprint PCS|number@messaging.sprintpcs.com|
T-Mobile|number@tmomail.net|
VirginMobile|number@vmobl.com|

    Enter recipient's email userid (receiving_userid@something.com):
    receiving_userid@something.com
 
# Future Options
Please feel free to fork and contribute or provide feedback on priorities and features

# Test Your Configuration and Email Setup
On the first time pubScribe starts, it will ask you for your email userid and password for the email account that you created to use for the alerts and status messages. Once you have entered a password the program will send an email message indicating the program has started. If not using email, you may skip this step by entering a null string.

# Feedback
Let us know what you think of this project and any suggestions for improvements. Feel free to contribute to this open source project.
