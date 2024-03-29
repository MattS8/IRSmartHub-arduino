# IRSmartHub
A Wifi connected IR receiver / blaster. The hardware consists of a simple IR LED setup, ESP8266 board, and IR receiver. Utilizes a Firebase backend for a "serverless" implementation. 

## Feature List
- Dynamically learn SSID/Password via SoftAP setup from any wifi-connected device 
- Send programmable IR signals via Android App **(WIP)**
- Learn IR signals and send to Android App **(WIP)**
- Handle sending repeat signals (simulating "holding down" a remote button) **(WIP)**

## IRFunctions
The currently planned functions are:
- record IR signal
- send single IR signal
- send repeat signal

### Record IR Signal
Listens for complete IR signals recorded from `IRrecv` ([see IRremoteESP8266](https://github.com/markszabo/IRremoteESP8266/blob/master/src/IRrecv.h)). This function ignores any recognized "repeat" commands. If no signal is recorded after `IR_READ_TIMEOUT` seconds ([see ArduinoIRFunctions](https://github.com/MattS8/IRSmartHub-arduino/blob/master/ArduinoIRFunctions.h)), a timeout error is sent. If the signal is too large, an overflow error is sent. Assuming none of the previous conditions are met, the recorded raw data is sent ([see ArduinoFirebaseFunctions](https://github.com/MattS8/IRSmartHub-arduino/blob/master/ArduinoIRFunctions.h))

### Send IR Signal
Current implementation sends straight raw data at a fixed frequency. Most likely, future implementations will have protocol-specific behavior. That all depends with how well sending raw data suites our need. Certain devices require sending a signal multiple times. This might require the `sendSignal` function to behave differently based on the type of IR signal sent. In addition, repeat functions may vary on a device-by-device basis. Idk, might need to check that.

## Firebase Functions
Currently planned functions are:
- set hub name
- send recorded signal
- send error message

### Set Hub Name
This function is meant to be called automatically when first being set up by a user. There's a strong possibility this feature will be removed from the hub's functionality and just be a user feature.

### Send Recorded Signal
Sets the "result" object in FirebaseDB, sending the raw data for the IR signal. If there is a problem sending the signal, an error message is sent with an unknown error code. The sent JSON object structure is as follows:

    {
     "code": <RES_SEND_SIG>, 
     "timestamp": "<timestamp value>", 
     "rawData": "<{4999, 9993, 4999, 4333, ... }>",
     "rawLen": "<size of rawData array>"
     }

### Send Error
Several error messages can be set based on issues sending responses, failing to get IR input, etc. This is done by setting the "result" object in FirebaseDB. The "code" value will be one of several error codes. The sent JSON object structure is as follows:

    {
     "code": <error_code>,
     "timestamp": <timestamp value> 
    }
