### AWS IoT Deep Dive with Device Shadows

### temperature-control.js
temperature-control.js is an interactive simulation which demonstrates
how Thing Shadows can be used to easily synchronize applications 
and internet-connected devices.  

temperature-control.js runs in two
separate terminal windows and is configured via command-line options;
in the following example, all certificates are located in the ~/certs
directory and have the default names as specified in the 
[Certificate Configuration section](#certificates).  The process running
with '--test-mode=2' simulates an internet-connected temperature control 
device, and the process running with '--test-mode=1' simulates a mobile
application which is monitoring/controlling it.  The processes may be
run on different hosts if desired.

#### _Installing Dependencies_
temperature-control.js
uses the [blessed.js](https://github.com/chjj/blessed) and [blessed-contrib.js](https://github.com/yaronn/blessed-contrib) libraries to provide an 
interactive terminal interface; it looks best on an 80x25 terminal with a
black background and white or green text and requires UTF-8 character
encoding.  You'll need to install these libraries in the examples/temperature-control
directory as follows:

```sh
cd temperature-control
npm install
```

#### _Running the Simulation - Terminal Window 1_
```sh
node temperature-control.js -f ~/certs --test-mode=1 -H <PREFIX>.iot.<REGION>.amazonaws.com
```
![temperature-control.js, 'mobile application' mode](https://s3.amazonaws.com/aws-iot-device-sdk-js-supplemental/images/temperature-control-mobile-app-mode.png)

#### _Running the Simulation - Terminal Window 2_
```sh
node temperature-control.js -f ~/certs --test-mode=2 -H <PREFIX>.iot.<REGION>.amazonaws.com
```
![temperature-control.js, 'device' mode](https://s3.amazonaws.com/aws-iot-device-sdk-js-supplemental/images/temperature-control-device-mode.png)

#### _Using the simulation_
The simulated temperature control device has two controls; _Setpoint_ and
_Status_.  _Status_ controls whether or not the device is active, and
_Setpoint_ controls the interior temperature the device will attempt to 
achieve.  In addition, the device reports the current interior and exterior
temperatures as well as its operating state (_heating_, _cooling_, or
_stopped_).

Two Thing Shadows are used to connect the simulated device and mobile
application; one contains the controls and the other contains the 
measured temperatures and operating state.  Both processes can update the
controls, but only the device can update the measured temperatures and
the operating state.

Controlling the simulation is done using the <kbd>up</kbd>, 
<kbd>down</kbd>, <kbd>left</kbd>, <kbd>right</kbd>, and 
<kbd>Enter</kbd> keys as follows:

* <kbd>up</kbd> increase the Setpoint
* <kbd>down</kbd> decrease the Setpoint
* <kbd>left</kbd> move left on the menu bar
* <kbd>right</kbd> move right on the menu bar
* <kbd>Enter</kbd> select the current menu option

##### Operating State

The operating state of the device is indicated by the color of the
Interior temperature field as follows:

* Red: _heating_
* Cyan: _cooling_
* White: _stopped_

The following example shows the temperature control simulation in 'device' mode
while the operating state is 'heating'.

![temperature-control.js, 'device' mode, 'heating' operating state](https://s3.amazonaws.com/aws-iot-device-sdk-js-supplemental/images/temperature-control-device-mode-heating.png)

##### Log

The log window displays events of interest, e.g. network connectivity,
_Status_ toggles, re-synchronization with the Thing Shadow, etc...

##### Menu Options

* Mode: Toggle the device _Status_.  _Status_ can be controlled from both
the simulated device and the mobile application.
* Network: Toggle the network connectivity of the device or mobile 
application; this can be used to observe how both sides re-synchronize 
when connectivity is restored.

In this example, the mobile application is disconnected from the network.  Although it has
requested that the Setpoint be lowered to 58 degrees, the command can't be sent to
the device as there is no network connectivity, so the operating state still shows as
'stopped'.  When the mobile application is reconnected to the network, it will attempt
to update the Thing Shadow for the device's controls; if no control changes have been
made on the device side during the disconnection period, the device will synchronize to
the mobile application's requested state; otherwise, the mobile application will re-
synchronize to the device's current state.

![temperature-control.js, 'mobile application' mode, network disconnected](https://s3.amazonaws.com/aws-iot-device-sdk-js-supplemental/images/temperature-control-mobile-app-mode-network-disconnected.png)

##### Exiting the Simulation

The simulation can be exited at any time by pressing <kbd>q</kbd>, 
<kbd>Ctrl</kbd>+<kbd>c</kbd>, or by selecting 'exit' on the menu bar.
