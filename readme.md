### Dependencies:

	ROS Melodic (Ubuntu 18.04 LTS release)
	hector_gazebo_plugins (GPS + IMU sensors for quadrotor)
	gazebo_ros
	ardupilot_gazebo
	ArduPilot

### Getting Digital Elevation Models into Gazebo:
	http://gazebosim.org/tutorials/?tut=dem
	
### Manipulating .tif files:
	gdalwarp to resize/adjust resolution/other stuff
	gdalinfo to get properties
	qgis to display
	
	changing resolution: 
		gdalwarp -tr .00138885 .00138885 n34_w087_1arc_v3.tif n34_low_res4.tif
		fractional is (1/3601)*5
	cropping data to get just huntsville: 
		gdalwarp -t_srs EPSG:4326 -te -86.67 34.815 -86.533 34.683 n34_w087_1arc_v3.tif n34_huntsville4.tif

	center point of huntsville (hopefully) = 34.755 N 86.6035 W, elevation: ~208m
	
### Setting up ArduPilot:

	git clone https://github.com/Ardupilot/ardupilot
	cd ardupilot
	git submodule update --init --recursive
	
	Tools/environment_install/install-prereqs-ubuntu.sh -y
	. ~/.profile
	./waf copter
	
	sudo apt-get install ros-melodic-mavros ros-melodic-mavros-extras
	wget https://raw.githubusercontent.com/mavlink/mavros/master/mavros/scripts/install_geographiclib_datasets.sh
	chmod a+x install_geographiclib_datasets.sh
	./install_geographiclib_datasets.sh
	
	git clone https://github.com/khancyr/ardupilot_gazebo
	cd ardupilot_gazebo
	mkdir build
	cd build
	cmake ..
	make -j4
	sudo make install
	
	# optional - hector gazebo plugins
	sudo apt install ros-melodic-hector-gazebo-plugins
	
	add to ~/.bashrc (may/may not be necessary):
	
		export PATH=$PATH:$HOME/.local/bin
		export GAZEBO_RESOURCE_PATH=/usr/share/gazebo-9:~/ardupilot_gazebo:${GAZEBO_RESOURCE_PATH}
	
### setting home location:

	add desired location to following file: ardupilot/Tools/autotest/locations.txt
	or to this file: $HOME/.config/ardupilot/locations.txt
	
	then launch SimVehicle.py with -L <LOCATION_NAME> flag	

###  launching ardupilot example sitl:


#### (works for dev. vm)	
	wes@ubuntu1804:~/ardupilot/ArduCopter$ ../Tools/autotest/sim_vehicle.py -f gazebo-iris --console --map
	wes@ubuntu1804:~/ardupilot_gazebo$ roslaunch mavros apm.launch fcu_url:=udp://127.0.0.1:14551@14555
	wes@ubuntu1804:~/ardupilot_gazebo$ gazebo --verbose worlds/iris_arducopter_runway.world
