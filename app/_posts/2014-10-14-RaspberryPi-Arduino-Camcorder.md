#Connecting the infrared red receiver and rgb to the Arduino and then the Arduino to the RaspberryPi
##Shopping list
We use cheap hardware components in this project. You do not need to use exactly the same components, but if you get the exactly same ones, it might be easier to follow this tutorial:

1. Raspberry Pi (model B), you can get them pretty much anywhere
2. Raspberry Pi camera module - or any other USB camera that works with the Pi. We used the officail RPi camera module, because it is easy to connect, is cheap and operates efficeintly with Raspberry Pi, so we don't waste hardware resources processing the video stream inefficiently. We also got a case for the camera module, toprevent it from getting damaged. There are many places form which you can get the camera module, we got our case here: http://uk.rs-online.com/web/p/product/7846190/
3. Arduino Mini Pro 328 3.3V/8MHz - We use the Arduino to connect the IR remote and RGB LED. Because the Raspberry Pi already has a GPIO, whci we could use to drive the RGB LED and IR receiver. The GPIO on the Pi is not nearly as precise though as the GPIO on the Arduino and when driving the RGB LED from the Pi, the colours would flicker a little. We used the GPi.GPIO library though and might get slighlty better results using another library to do the PWM stuff, the insterested reader might want to have a look at: https://pythonhosted.org/RPIO/pwm_py.html. There is also a library available for the IR receiver to work with the Arduino. So rather than writing a new library for the Raspberry Pi to interface with the IR receiver, we decided to do it with the Arduino and then have the Arduino and Raspberry Pi communicate with each other over the serial line. If you are interested in writing an IR receiver library for the Raspberry Pi, a good starting point would be this tutorial, which exaplins how to hook up a radio 433MHz receiver to the Pi's GPIO: http://www.princetronics.com/how-to-read-433-mhz-codes-w-raspberry-pi-433-mhz-receiver/ If anyone knows a better way or an existing IR receiver library for the Raspberry Pi, please leave a comment below. There are many places where you can purchase Arduino Mini Pros, so I suggest you just google for it.
4. RGB LED, we use this to indicate the different states our Raspberry Pi Camcorder can be in, this is optional, as the camcorder would work without displaying the curent state it is in, but we find it very useful to get the feedback from the camera, to monitor it is operating as we want it. Of course, you could also use other devices to indicate the camcorders state, such as an LCD display, you could SSH into you Raspberry Pi and use the terminal to monitor the state, etc. The RGB LED we use can be purchased at: http://hobbycomponents.com/index.php/diy-arduino-3-color-rgb-smd-led-module-black.html
5. IR receiver and remote - This is used for controlling the Raspberry Pi. Again, this is optional and you could control the Pi in many ways, such as SSHing into it and issuing commands, attaching buttons straight to the GPIO or even using a USB keyboard, but using an InfraRed remote is the best suited option for our requirements. The IR remote and receiver we used can be purchased here: http://hobbycomponents.com/index.php/ir-receiver-module-wireless-remote-control-kit.html. If you are tight on budget, you culd also just buy any cheap IR receiver and use any IR remote you happen to have around. But then you need to program the *codes* your remote sends into the IR library yourself. If that's what you want to do, have a look here: http://playground.arduino.cc/Code/InfraredReceivers
6. USB battery to power the Raspberry Pi -  Again, this is optional, but a camcorder should be portable to make sense, therfore a wall charger can not be used to power the Pi. Make sure the USB battery you buy has enough capacity to power yur Pi for as long as you need to, remeber, with the camera running, your Pi will consume more current.
7. Wi-fi USB dongle - You only need this if you want to stream the video you are capturing over the network and don't want to use the ethernet cable. If you are happy connecting your Pi to the network via cable or you only want to record to files, but not stream them, then this is not needed.
8. Cables to connect the stuff, breadboard, soldering iron or however you decide to connect the components to the Arduino and Raspberry Pi.
9. You also shouldn't forget that you need something to flash the Arduino Mini Pro with or you can get (at a higher price) an Arduino Mini Pro with a FTDI on it. We use our Arduino Uno to flash the Mini Pro, you can find instuction on how to do this in the next section.
##Flashing the Arduino Mini Pro
If you already know how to flash your Arduino Mini Pro you can skip this section. This only applies to the Arduino Mini Pros which do not come with a FTDI on the board. We think this is the better option, as they are cheaper and smaller.

To flash the Arduino Mini Pro with an Arduino Uno, make the following connections:

| Arduino UNO | Arduino Mini Pro (3.3V) |
|-------------|-------------------------|
| 3.3V        | VCC                     |
| GND         | GND                     |
| RX          | RX                      |
| TX          | TX                      |
| RESET       | RST                     |

<img src="img/">
##Making the connections
###Infrared-Arduino:
Signal			<->		Pin 11
Middle (V)		<->		VCC
GND (-)			<->		GND
###RGB-Arduino (Resistors should already be on cable ends..)
R				<->		2
G				<->		5
B				<->		6
GND (-)			<->		GND
###RaspberryPi-Arduino
3.3V			<->		VCC
GND				<->		GND
TX				<->		RX
RX				<->		TX
##Check if connection works
1. SSH into raspberry pi
2. run command: sudo minicom -b 9600 -o -D /dev/ttyAMA0  (install minicom first and disable login shell: http://blog.oscarliang.net/raspberry-pi-and-arduino-connected-serial-gpio/)
3. Now when the Arduino receives an IR signal, it will transmit it over serial to the Pi and it should show up in the minicom console (to quit hit CTRL+A and then press 'q')
##To make the python program
1. Install python-serial: ‘sudo apt-get install python-serial -y’
2. Install camera module: ‘sudo apt-get install python-picamera python3-picamera python-rpi.gpio -y’
3. Then show code for program and comment stuff...
4. Also show arduino program for serial communication, comment on RPi.GPIO, but say arduino is the better solution, refer to other raspberry pi PWM libs...
##Now we tested it's working, let's install ROS and stream data over network to PC for processing
1. You can follow tutorial at: https://github.com/fpasteau/raspicam_node, but basicaly the steps are (we assume you already installed the camera module):
    1. Install ROS with: sudo apt-get install ros-groovy-image-transport ros-groovy-image-transport-plugins ros-groovy-image-transport-plugins ‘ros-groovy-camera-info-manager -y’
	2. Clone the Github repository for the ARM side libraries for interfacing to Raspberry Pi GPU and run the following commands (not sure why we need to clone the Github repo, as it is not used later on directly, but I think raspicam_node uses it internally and needs it to be installed under /home/pi/userlan ??), anyways, run these commands:
	    - ‘git clone https://github.com/raspberrypi/userland.git /home/pi/userland’
        - ‘cd /home/pi’
        - ‘mkdir catkin_ws’
        - ‘source /opt/ros/groovy/setup.bash’
        - ‘export ROS_WORKSPACE=/home/pi/catkin_ws’
        - ‘cd /home/pi/catkin_ws’
		- ‘mkdir src’
		- ‘cd src’
		- ‘git clone https://github.com/fpasteau/raspicam_node.git raspicam’
		- ‘cd ..’
		- ‘catkin_make’
		- ‘source devel/setup.bash’
	3. Now we should have ROS and raspicam_node installed. To see if it works, run these three commands:
	    - ‘rosrun raspicam raspicam_node‘
		- ‘rosservice call /camera/start_capture‘
		- ‘rosrun image_view image_view image:=/camera/image _image_transport:=compressed‘
		- NOTE: each command is blocking, i.e. it won't return the prompt to you to execute other commands. I do not work directly on my Raspberry Pi, but rather SSH into it. So to run the three commands in parallel, we could either open three separate SSH connections, or , my prefered option, we could start a screen session and switch between the screens with keyboard shortcuts (see nestes bullet points below for a brief introduction to screen, if you haven't used it before). Alternatively, one could also run all commands in the background, i.e. appending an ampersand (&) to the end of the commands, but I don't like this option, as it  would not print the output from the programms to the terminal.
		    - First of all, make install screen if you haven't already: ‘sudo apt-get install screen -y‘
			- To start a new screen session, type: ‘screen‘ This is great, because even if we temporarily loose our SSH connection to the Raspberry Pi, screen will continue running in the background and we can *reattach* to the session, once we reconnected to the Raspberry Pi. If you don not run screen and you loose the connection, all other processes that you have started in the session (besides daemon processes) will stop.
			- OK, now we started the screen session, let's enter the first command: ‘rosrun raspicam raspicam_node’