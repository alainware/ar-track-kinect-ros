# AR Track Kinect
This repository shows a basic implementation of the ROS ar_track_alvar library on Ubuntu 18.04 LTS using ROS Melodic and Microsoft Kinect sensor (Xbox 360).

## Project Structure

1. [ROS Melodic Installation](#ROS-Melodic-Installation)
2. [Creating and Configuring a ROS Environment](#Creating-and-Configuring-a-ROS-Environment)
3. [Installing AR Tag Tracking library](#Installing-AR-Tag-Tracking-library)
4. [Running Launch Files](#Running-Launch-Files)

## ROS Melodic Installation

* [Ubuntu Configuration](#Ubuntu-Configuration)
* [ROS Installation](#ROS-Installation)
* [ROS Dependencies](#ROS-Dependencies)

### Ubuntu Configuration

Add the **ROS repository** to your Ubuntu/Debian **sources.list**:

```console
sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
```

Add the keys for accessing it:

```console
sudo apt-key adv --keyserver 'hkp://keyserver.ubuntu.com:80' --recv-key C1CF6E31E6BADE8868B172B4F42ED6FBAB17C654
```

At this point you should have an output like this:

```console
Executing: /tmp/apt-key-gpghome.WudTyznLyJ/gpg.1.sh --keyserver hkp://keyserver.ubuntu.com:80 --recv-key C1CF6E31E6BADE8868B172B4F42ED6FBAB17C654
gpg: key F42ED6FBAB17C654: public key "Open Robotics <info@osrfoundation.org>" imported
gpg: Total number processed: 1
gpg:               imported: 1
```

Update the packages list:

```console
sudo apt update
```

At this time, the output should be like the following:

```console
Hit:1 http://eu-west-1.ec2.archive.ubuntu.com/ubuntu bionic InRelease
Hit:2 http://eu-west-1.ec2.archive.ubuntu.com/ubuntu bionic-updates InRelease
Hit:3 http://eu-west-1.ec2.archive.ubuntu.com/ubuntu bionic-backports InRelease
Get:4 http://security.ubuntu.com/ubuntu bionic-security InRelease [88.7 kB]     
Get:5 http://packages.ros.org/ros/ubuntu bionic InRelease [4669 B]                              
Get:6 http://packages.ros.org/ros/ubuntu bionic/main amd64 Packages [569 kB]
Fetched 662 kB in 2s (406 kB/s)   
Reading package lists... Done
Building dependency tree       
Reading state information... Done
3 packages can be upgraded. Run 'apt list --upgradable' to see them.
```

### ROS Installation

There are many options when it comes to ROS Installation, i.e., **~~ROS Base~~**, **~~ROS Desktop~~** and **ROS Desktop Full**. In this case we are going to choose the <mark>**ROS Full Desktop**</mark> option.

**ROS Desktop Full** installs everything of ROS Desktop option plus **2D/3D simulators** and **2D/3D perception**.

Install ROS Desktop Full:

```console
sudo apt install ros-melodic-desktop-full
```

After running the command, you should have an output like this:

```console
0 upgraded, 485 newly installed, 0 to remove and 3 not upgraded.
Need to get 234 MB of archives.
After this operation, 1132 MB of additional disk space will be used.
Do you want to continue? [Y/n]

Setting up ros-melodic-nodelet-topic-tools (1.9.16-0bionic.20190601.015001) ...
Setting up ros-melodic-nodelet-core (1.9.16-0bionic.20190601.015433) ...
Setting up ros-melodic-roswtf (1.14.3-0bionic.20190601.014658) ...
Setting up ros-melodic-ros-comm (1.14.3-0bionic.20190601.015500) ...
Setting up ros-melodic-ros-core (1.4.1-0bionic.20190601.015718) ...
Setting up ros-melodic-ros-base (1.4.1-0bionic.20190808.193524) ...
Processing triggers for libgdk-pixbuf2.0-0:amd64 (2.36.11-2) ...
Processing triggers for libc-bin (2.27-3ubuntu1) ...

user@computer:~$
```
**Important! - Troubleshooting**

Note that due to the differences on the Linux distributions that support ROS Melodic, you could have some problems with the next section such as the following error:

```console
rosdep: command not found
```

If that's the case, dont worry, just run this few commands:

```console
sudo apt-get install python-pip
sudo pip install -U rosdep
``` 

or use the recommended method on Ubuntu:

```console
sudo apt-get install python-rosdep
```

### ROS Dependencies

ROS has a client which is in charge of managing commands and dependencies, and it is called ``rosdep``.

Initialize ``rosdep``:

```console
sudo rosdep init
```

The output would be the following:

```console
Wrote /etc/ros/rosdep/sources.list.d/20-default.list
Recommended: please run
```

Update ``rosdep``:

```console
rosdep update
```

The output for running the previous command:

```console
reading in sources list data from /etc/ros/rosdep/sources.list.d
Hit https://raw.githubusercontent.com/ros/rosdistro/master/rosdep/osx-homebrew.yaml
Hit https://raw.githubusercontent.com/ros/rosdistro/master/rosdep/base.yaml
Hit https://raw.githubusercontent.com/ros/rosdistro/master/rosdep/python.yaml
Hit https://raw.githubusercontent.com/ros/rosdistro/master/rosdep/ruby.yaml
Hit https://raw.githubusercontent.com/ros/rosdistro/master/releases/fuerte.yaml
Query rosdistro index https://raw.githubusercontent.com/ros/rosdistro/master/index-v4.yaml
Skip end-of-life distro "ardent"
Skip end-of-life distro "bouncy"
Add distro "crystal"
Add distro "dashing"
Add distro "eloquent"
Skip end-of-life distro "groovy"
Skip end-of-life distro "hydro"
Skip end-of-life distro "indigo"
Skip end-of-life distro "jade"
Add distro "kinetic"
Skip end-of-life distro "lunar"
Add distro "melodic"
Add distro "noetic"
updated cache in /home/ubuntu/.ros/rosdep/sources.cache
```

## Creating and Configuring a ROS Environment

We have ROS and the dependencies manager installed. Let’s configure our environment. This is a very important step, once we have it done, working with ROS will be smooth.

ROS is installed at ``/opt/ros/<distro>`` (in this case ``/opt/ros/melodic``). In order to have ROS commands available, it’s needed to source the shell file inside of the installation folder. This is done like the following:

```console
source /opt/ros/melodic/setup.bash
```

But.. considering we want to have it available in every terminal we open, we use to have a “shortcut”, which is adding this command to the file "``/home/<user>/.bashrc``". The ``.bashrc`` file is called every time a new terminal is opened, therefore, we won’t need to ``source`` ROS setup, since we have the instruction in this file. In order to add the command to the file, you can edit it manually using an editor of your preference or just execute the command below:

```console
echo "source /opt/ros/melodic/setup.bash" >> ~/.bashrc
```

Now create a ROS Workspace:

```console
mkdir -p ~/catkin_ws/src
cd ~/catkin_ws/
catkin_make
```

Additionally, if you look in your current directory you should now have a 'build' and 'devel' folder. Inside the 'devel' folder you can see that there are now several setup.*sh files. Sourcing any of these files will overlay this workspace on top of your environment. To understand more about this see the general catkin documentation: [catkin](http://wiki.ros.org/catkin). Before continuing source your new setup.*sh file: 

```console
source devel/setup.bash
```

To make sure your workspace is properly overlayed by the setup script, make sure ROS_PACKAGE_PATH environment variable includes the directory you're in.

```console
echo $ROS_PACKAGE_PATH
```

The output would be the following:

```console
/home/youruser/catkin_ws/src:/opt/ros/melodic/share
```

## Installing AR Tag Tracking library

Install the library:

```console
sudo apt-get install ros-melodic-ar-track-alvar
```

Install the Kinect driver:

```console
sudo apt-get install ros-melodic-freenect-launch
```

Now that we have the package, let’s make our own AR Tag! To do that, you will either want to make a folder to keep all of your tags, or navigate to an existing one you want to output the images to. Once there, you will run:

```console
rosrun ar_track_alvar createMarker 0
```

You should see a new file named ``MarkerData_0.png`` in your current directory. You can easily open the image from your terminal by running:

```console
eog MarkerData_0.png
```

You should get an image that looks like the following pop up on your screen. It should also be noted that AR Tags have different patterns based off of the number you have requested. A zero will always look like a zero, a one will always look like a one and so on. This is really useful if you tag an object for a robot to interact with with a specific number. So you can reliably, and repeatably select the object again.

<center><img src="https://realitybytesdotblog.files.wordpress.com/2017/06/ar_0.jpg" alt="zero-ar-tag"></center>

</br>

Another note on printing. You can select different sizes to create an AR Tag. SO if  you wanted one really big one or several small one depending on your application, you can have that! It’s just important to note what size you printed it, because that’ll be important later. The default size that these tags are printed at are 9 units x 9 units. So for example. If you wanted to output a 3 tag that was 5cmx5cm, the syntax would be:

```console
rosrun ar_track_alvar createMarker -s 5 7
```

**Important!**

Due to differences in how you can format your printer/setup an image print or if you take a bunch of images and put them on a single page, it can cause some changes in the actual size of the tag. I would suggest that you still measure the printed tag just to be absolutely sure of the size of the tag, because that will be important to get an estimate of the tag’s position with respect to the camera. A tiny tag that isn’t known to be tiny to the software could think it’s really far away.

Clone the [repository](https://github.com/alainware/ar-track-kinect-ros) into the ``catkin_ws`` workspace:

```console
git clone https://github.com/alainware/ar-track-kinect-ros
```

## Running Launch Files

For the Xbox 360 Kinect, you will need a package called [freenect](http://wiki.ros.org/freenect_launch) (the one we installed [previously](#Installing-AR-Tag-Tracking-library)) to launch the hardware as a ROS Node. To test it you will plug in your respective camera/sensor, then launch their respective driver node.

Navigate to the folder where you cloned the repository. You should be able to see a folder named ``launch``. Navigate into the ``launch`` folder and run the following commands in <mark>**two separate terminals**</mark>...

Terminal 1:
```console
roslaunch freenect_launch freenect.launch
```
Terminal 2:
```console
roslaunch ar_track_kinect.launch marker_size:=5
```

Open rviz with the following command:

```console
rviz
```

Finally, load the ``view_markers_kinect.rviz`` configuration file into rviz (this file is located in the root of the folder of the cloned repository).

**You're done! :)**


## Reference Links
* [How to Install ROS on Ubuntu 18.04](https://www.theconstructsim.com/how-to-install-ros-on-ubuntu/)
* [Tracking AR tags in ROS](https://varunagrawal.github.io/2017/10/23/ar-track/)
* [Installing and Configuring Your ROS Environment](http://wiki.ros.org/ROS/Tutorials/InstallingandConfiguringROSEnvironment)
* [Detecting and Tracking AR Tags](https://realitybytes.blog/2017/06/02/detecting-and-tracking-ar-tags/)
* [ar_track_alvar - ROS Wiki](http://wiki.ros.org/ar_track_alvar)