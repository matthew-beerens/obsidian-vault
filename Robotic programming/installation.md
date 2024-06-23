
# Installing ROS (Noetic) on Ubuntu 20.04 ('Focal'):
# Ideally, the following lines (i.e., those starting with '>') should be executed line-by-line rather than all at once

# The lines below are for the installation of ROS

    $ sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
    $ sudo apt install curl # if you haven't already installed curl
    $ curl -s https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc | sudo apt-key add -
    $ sudo apt-get update
    $  sudo apt install ros-noetic-desktop-full

# you must source the environment

    $  source /opt/ros/noetic/setup.bash

# You may write the command directly on the ".bashrc"

    $ echo "source /opt/ros/noetic/setup.bash" >> ~/.bashrc
    $ source ~/.bashrc

# install dependencies for building packages

    $ sudo apt install python3-rosdep python3-rosinstall python3-rosinstall-generator python3-wstool build-essential

# install and initialize "rosdep"

    $ sudo apt install python3-rosdep
    $ rosdep update

# to test the ROS installation enter "roscore" and if successful, last line should print "started core service [/rosout]"

    $ roscore


