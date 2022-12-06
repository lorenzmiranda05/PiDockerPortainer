### **Pi Docker Portainer**
#### Video by Will of SpaceRex YouTube Channel

[Youtube Video Link][How to install Docker (and Portainer) on a RaspberryPi and run millions of apps on your RaspberryPi!]

---

<br  />

**Raspberry Pi Desktop Setup**

1. Download Raspberry Pi Imager installer from this [link][Raspberry Pi Imager Download].

1. Install Raspberry Pi Imager.
1. Plug-in the SD Card to the machine.
1. Open Raspberry Pi Imager.
1. Select RASPBERRY PI OS Lite (32-BIT) for the Operating System.
1. Select the SD Card for Storage.
1. Click the gear button to open the Advanced options for image customization.
1. Set the following:
    * Set hostname
    * Enable SSH
        * Use password authentication
    * Set username and password
        * Username
        * Password        
    * Configure wireless LAN
        * SSID
        * Password
    * Set locale settings
        * Time zone
        * Keyboard layout

1. Click the save button.
1. Click the write button.
1. Wait for the SD Card imaging to end.
1. Eject the SD Card from the machine.
1. Insert the SD Card to the Raspberry Pi.
1. Turn on the Raspberry Pi.
1. Close Raspberry Pi Imager.

##### Reference: [Raspberry Pi Documentation][Raspberry Pi Documentation]
---

<br  />

**Raspberry Pi Static IP Address Setup**

1. Open Windows PowerShell.

1. Run the command to connect to the Raspberry Pi using SSH.
    ```bash
    ssh pi@rapsberrypi.local
    # ssh [username]@[hostname].local
    ```
1. Enter the password.
1. Run the command to determine the router's ip address. See the address under the Gateway column with UG Flags.
    ```bash
    netstat -nr
    ```
1. Run the command to open /etc/dhcpcd.conf in nano for editing.
    ```bash
    nano /etc/dhcpcd.conf
    ```
1. Add the following lines at the bottom of the file.
    ```bash
    interface wlan0
    static ip_address=192.168.1.73/24
    static routers=192.168.1.1
    static domain_name_servers=192.168.1.1
    
    #interface INTERFACE
    #static ip_address=YOURSTATICIP/24
    #static routers=YOURGATEWAYIP
    #static domain_name_servers=YOURGATEWAYIP
    ```
1. Save the file
    1. Press ctrl+x.

    1. Press y.
    1. Press enter.
1. Run the command to restart the Raspberry Pi and apply the new static IP.
    ```bash
    sudo reboot
    ```

##### Reference: [Get a static IP-address][Get a static IP-address] by Raspberry Pi Guide
---

<br  />

**Docker and Portainer Setup**

1. Open Windows PowerShell.

1. Run the command to connect to the Raspberry Pi using SSH.
    ```bash
    ssh pi@rapsberrypi.local
    # ssh [username]@[hostname].local
    ```
1. Enter the password.
1. Run the commands to update the operating system:
    ```bash
    sudo apt update
    sudo apt upgrade -y
    ```

    **If the error "Cannot initiate the connection to mirror.rise.ph:80 (43.226.6.79). - connect (101: Network is unreachable)" was emcountered, see Troubleshooting section below before performing this step again.**

1. Run the command to begin docker installation:
    ```bash
    curl -sSL https://get.docker.com | sh
    ```

    **If the error "E: Sub-process /usr/bin/dpkg returned an error code (1)" was encountered, see Troubleshooting section below before performing the next step.**

1. Run the command to add the user into the docker group.
    ```bash
    sudo usermod -aG docker $USER
    ```

1. Run the command to test the docker setup.
    ```bash
    docker
    ```

1. Run the command to pull portainer-ce image from docker image repository, docker hub.
    ```bash
    docker pull portainer/portainer-ce:linux-arm
    ```

1. Run the command to run portainer-ce in docker.
    ```bash
    docker run -d -p 9000:9000 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:linux-arm
    # docker run -d --> run a new docker container

    # -p --> which ports to use
    # 9000:9000 --> internal:external ports

    # --name=portainer --> gives a name for the docker GUI

    # --restart=always --> if anything goes down or reboot, perform automatic restart

    # -v /var/rund/docker.sock --> environmental variable name
    # /var/run/docker.sock --> value of /var/rund/docker.sock variable that will allow portainer to control other containers in docker

    # -v portainer_data --> environmental variable name
    # /data --> the value of portainer_data variable is the root/data folder

    # portainer/portainer-ce:linux-arm --> image to run in docker
    ```

1. Run the command to list all running containers in docker.
    ```bash
    docker ps
    ```

1. Open http://rapsberrypi.local:9000/ in a web browser to open portainer.

1. Under New Portainer installation, provide the following details.
    * Username

    * Password

    * Confirm Password

1. (Optional) Tick or untick checkbox of "Allow collection of anonymous statistics..."

1. Click Create user button.

1. Under Environment Wizard, select Get Started using the local environment.

<br  />

**Troubleshooting**

1. "Cannot initiate the connection to mirror.rise.ph:80 (43.226.6.79). - connect (101: Network is unreachable)"
    1. Run the command to define a specific mirror to use
        ```bash
        sudo nano /etc/apt/sources.list
        ```

    1. Replace the mirror director with the closest mirror to your area.
        * From
            ```bash
            deb http://raspbian.raspberrypi.org/raspbian/ bullseye main contrib non-free rpi
            # Uncomment line below then 'apt-get update' to enable 'apt-get source'
            #deb-src http://raspbian.raspberrypi.org/raspbian/ bullseye main contrib non-free rpi
            ```
        * To
            ```bash
            #deb http://raspbian.raspberrypi.org/raspbian/ bullseye main contrib non-free rpi
            deb http://mirror.pregi.net/raspbian/raspbian/ bullseye main contrib non-free rpi
            # Uncomment line below then 'apt-get update' to enable 'apt-get source'
            #deb-src http://raspbian.raspberrypi.org/raspbian/ bullseye main contrib non-free rpi
            ```
    1. Save the file.
        1. Press ctrl+x.

        1. Press y.
        1. Press enter.

1. "E: Sub-process /usr/bin/dpkg returned an error code (1)"
    1. Run the command to move the dpkg info file into another file
        ```bash
        sudo mv /var/lib/dpkg/info /var/lib/dpkg/info_silent
        ```

    1. Run the command to create a new directory info in dpkg folder
        ```bash
        sudo mkdir /var/lib/dpkg/info
        ```

    1. Run the command to download package information from all configured sources
        ```bash
        sudo apt-get update
        ```

    1. Run the command to check the dependencies of the packages you want and install any that are needed
        ```bash
        sudo apt-get -f install
        ```

    1. Run the command to move all the files of the info directory into info_silent directory
        ```bash
        sudo mv /var/lib/dpkg/info/* /var/lib/dpkg/info_silent
        ```

    1. Run the command to remove the info directory
        ```bash
        sudo rm -rf /var/lib/dpkg/info
        ```

    1. Run the command to move all the file of the info_silent directory into the info directory
        ```bash
        sudo mv /var/lib/dpkg/info_silent /var/lib/dpkg/info
        ```

    1. Run the command to update the list of available packages and their versions
        ```bash
        sudo apt-get update
        ```

    1. Run the command to installs newer versions of the packages in the machine
        ```bash
        sudo apt-get upgrade
        ```

    1. Run the command to check the version of docker
        ```bash
        docker --version
        ```

    1. Run the command to view the docker documentation
        ```bash
        docker
        ```

##### References:
* [Cannot connect to mirrordirector.raspbian.org][Cannot connect to mirrordirector.raspbian.org]
* [Raspbian Mirrors][Raspbian Mirrors]
* [Sub-process /usr/bin/dpkg returned an error code (1)][Sub-process /usr/bin/dpkg returned an error code (1)]

---

<br  />

<!-- Reusable and Invisible URL Definitions  -->
[How to install Docker (and Portainer) on a RaspberryPi and run millions of apps on your RaspberryPi!]: https://www.youtube.com/watch?v=O7G3oatg5DA
[Raspberry Pi Imager Download]: https://www.raspberrypi.com/software/
[Get a static IP-address]: https://raspberrypi-guide.github.io/networking/set-up-static-ip-address#get-a-static-ip-address
[Raspberry Pi Documentation]: https://www.raspberrypi.com/documentation/
[Cannot connect to mirrordirector.raspbian.org]: https://raspberrypi.stackexchange.com/questions/27479/cannot-connect-to-mirrordirector-raspbian-org
[Raspbian Mirrors]: http://www.raspbian.org/RaspbianMirrors
[Sub-process /usr/bin/dpkg returned an error code (1)]: https://databasefaqs.com/sub-process-usr-bin-dpkg-returned-an-error-code-1/#:~:text=The%20%E2%80%9Csub%2Dprocess%20%2Fusr,process%20or%20a%20corrupted%20database.