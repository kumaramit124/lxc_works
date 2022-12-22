# lxc_works
LXC container creation and launch Setup
Prerequsites - Ubuntu-20.04 Recommended, below steps are verified on ubuntu 20.04

# 1. Install lxc and create first container on x86.
https://linuxcontainers.org/lxc/getting-started/
- sudo apt-get install lxc
Create the ~/.config/lxc directory if it doesn't exist
- mkdir -p ~/.config/lxc
Copy /etc/lxc/default.conf to ~/.config/lxc/default.conf
- cp /etc/lxc/default.conf ~/.config/lxc/default.conf
Append the following two lines to it:
lxc.idmap = u 0 100000 65536
lxc.idmap = g 0 100000 65536
Now, create your first container with:
- sudo lxc-create -t download -n my-container
Distributions, Versions, and Architectures to choose from the list -
- ubuntu, bionic and amd64
(Note: if there is an error to get the templet run below cmd-
- sudo lxc-create -t download -n my_lxc -- --keyserver hkp://p80.pool.sks-keyservers.net:80
OR
- sudo lxc-create -n test -t download -- --no-validate)
Once created start the container
- sudo lxc-start -n my-container -d
Check the container info using
- sudo lxc-info -n my-container
- sudo lxc-ls -f
- sudo lxc-attach -n my-container
- sudo lxc-stop -n my-container
- sudo lxc-destroy -n my-container


# 2. Install all the lxc development dependent packages.
- sudo apt-get install lxc-dev
- sudo apt-get install libpopt-dev
- sudo apt-get install liblxc-dev
- sudo apt-get install libconfig-dev

# 3. Clone launchService from

https://github.netgear.com/seal/launchService
Install Doxygen
- sudo apt-get install -y doxygen
- sudo apt-get install -y graphviz


# 4. Build the launchService(Recommended to build it as super user- "sudo su")

- cd launchService
- mkdir build
- cd build
- cmake ../ ---> if cmake not found. Use command sudo apt install cmake to install make
- make
- sudo make install
Check the container present in below path
- /var/lib/lxc/
To see the container list.
- sudo lxc-ls
To check the container current status
- sudo lxc-info -n example
an example is the container name.
To launch the build container run
- sudo launchService example
(Note: This will not work as example container created using busybox, so follow the below steps 5 and 6)


# 5. Add your own container under the launchService.

Go to launchService folder
- cd launchService
From Step 1 lxc created container needs to be copied under launchService for a new container name "testContainer"
- cp -rf /var/lib/lxc/my-container testContainer
This my-container having the config and rootfs.
Now copy the launch.conf and poststartup.sh from example to testContainer.
Edit the config file under the testContainer.
- lxc.rootfs.path -> replace my-container with your container name, e.g. testContainer
- lxc.uts.name -> replace my-container with your container name, e.g. testContainer
- lxc.net.0.hwaddr - this shows the container address, as of now change the last digits
Edit launch.conf file
- name = "Your container name" e.g."testContainer"
- forward = "/your container name" e.g. "/testContainer"

Edit CMakeLists.txt file to create new container entry, add below line
install( DIRECTORY Your container name e.g. testContainer
DESTINATION /var/lib/lxc
USE_SOURCE_PERMISSIONS )

Follow the build steps from point 4.


# 6. After build the new container from the lanucher, run the container with "launchService"

- sudo launchService testContainer
- sudo lxc-ls
- sudo lxc-info -n testContainer
