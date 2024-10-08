# iot-sandbox-flows
Node-RED flows for an Internet-of-Things sandbox

This repo is intended to be cloned from within a Node-RED installation that has the projects feature enabled.

With these Node-RED flows installed, you can create an environment that makes it easy for people to collaboratively 
interconnect inputs and outputs of diverse devices, leveraging the power of Pareto Anywhere and the Internet of Things.

It's expecially useful in an educational environment where you want students to be able to play with interaction possibilities 
without having to worry too much about the nuts and bolts of implementation.


# Installation

## Set up a Raspberry Pi or similar computer with Node-RED and Pareto Anywhere

Using Raspberry Pi Imager, create an image of:

__Raspbian Lite, with ssh enabled__


(Download Raspberry Pi Imageer and check out more thorough instructions for use at
this link: https://www.raspberrypi.com/software/)

Once you've logged into your Pi, run:

`ifconfig`

Note your IP address, you'll need it later :) 

then run these commands to install nodejs:

```
sudo apt update
sudo apt -y upgrade
sudo apt install -y git ca-certificates curl gnupg
curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key | sudo gpg --dearmor -o /usr/share/keyrings/nodesource.gpg
NODE_MAJOR=20
echo "deb [signed-by=/usr/share/keyrings/nodesource.gpg] https://deb.nodesource.com/node_$NODE_MAJOR.x nodistro main" | sudo tee /etc/apt/sources.list.d/nodesource.list
sudo apt -y update
sudo apt -y install nodejs build-essential
```

Then go install Pareto Anywhere, using these instructions:
https://reelyactive.github.io/diy/pareto-anywhere-pi/  

The above link is the most up-to-date installation instructions, 
but here are all the commands distilled below:

```
mkdir ~/reelyActive
cd ~/reelyActive
git clone --recurse-submodules https://github.com/reelyactive/pareto-anywhere.git
cd pareto-anywhere
npm install
sudo cp units/pareto-anywhere-pi.service /lib/systemd/system
sudo systemctl enable pareto-anywhere-pi.service
sudo systemctl start pareto-anywhere-pi.service

cd ~/reelyActive
git clone https://github.com/reelyactive/barnowl-hci.git
cd barnowl-hci
npm install
sudo setcap cap_net_raw+eip $(eval readlink -f `which node`)
sudo cp units/barnowl-hci-forwarder-pi.service /lib/systemd/system
sudo systemctl enable barnowl-hci-forwarder-pi.service
sudo systemctl start barnowl-hci-forwarder-pi.service
```

Now to install Node-RED
```
bash <(curl -sL https://raw.githubusercontent.com/node-red/linux-installers/master/deb/update-nodejs-and-nodered)
```
say YES to pi-specific nodes

This installation is insteractive, with some questions to answer. 
Here's what that process should look like for you:

```
=====================================
This tool will help you create a Node-RED settings file.

✔ Settings file · /home/pi/.node-red/settings.js

User Security
=============
✔ Do you want to setup user security? · No

Projects
========
The Projects feature allows you to version control your flow using a local git repository.

✔ Do you want to enable the Projects feature? · Yes
✔ What project workflow do you want to use? · manual - you must manually commit changes

Editor settings
===============
? Select a theme for the editor. To use any theme other than "default", you will need to install @node-red-contrib-themes/theme-collection in your Node-RED user directory. …
✔ Select a theme for the editor. To use any theme other than "default", you will need to install @node-red-contrib-themes/theme-collection in your Node-RED user directory. · default

✔ Select the text editor component to use in the Node-RED Editor · monaco (default)

Node settings
=============
✔ Allow Function nodes to load external modules? (functionExternalModules) · Yes
Node-RED Settings File initialisation
=====================================
This tool will help you create a Node-RED settings file.

✔ Settings file · /home/pi/.node-red/settings.js

User Security
=============
✔ Do you want to setup user security? · No

Projects
========
The Projects feature allows you to version control your flow using a local git repository.

✔ Do you want to enable the Projects feature? · Yes
✔ What project workflow do you want to use? · manual - you must manually commit changes

Editor settings
===============
? Select a theme for the editor. To use any theme other than "default", you will need to install @node-red-contrib-themes/theme-collection in your Node-RED user directory. …
✔ Select a theme for the editor. To use any theme other than "default", you will need to install @node-red-contrib-themes/theme-collection in your Node-RED user directory. · default

✔ Select the text editor component to use in the Node-RED Editor · monaco (default)

Node settings
=============
✔ Allow Function nodes to load external modules? (functionExternalModules) · Yes
```

Once that's done, run the commands:
```
sudo systemctl enable nodered.service
cd ~/.node-red
sudo nano settings.js
edit:
    credentialSecret: false,

sudo systemctl restart nodered.service
```

Now you should be able to go to http://[THE RPI IP ADDRESS]:1880 and see Node-RED running, yay!


### Cloning GitHub repo

Note: These instructions will allow you to clone the iot-sandbox-flows repo, but not to commit back to it.
If you are a member of this project and want to commit back to the repo, or if you're curious about how to work with Git Repos from within Node-RED more broadly, better instructions can be found (here)

Go to: http://[THE RPI IP ADDRESS]:1880 from a computer on the same network as your Pi.
From the menu on the upper right, select Projects->New

![f7b03f4b4ed1f89baf2eb3ea19e99f84.png](f7b03f4b4ed1f89baf2eb3ea19e99f84.png)

in the box that appears, select "Clone Repository" and enter this repository's URL in the field "Git repository URL"

![3568855a5679f829cf8ce95ac6ddd229.png](3568855a5679f829cf8ce95ac6ddd229.png)
[The repository URL might have changed by the time you read this :) ]

Enter your github credential as appropriate.

Click "Clone Repository"

Now you should see several flow tabs in your Node-RED app!

# Now that it's Installed

Great, the sandbox is installed, now what?

The sandbox is made up of a few flows, each of which is contained in a tabbed window in the app

Each flow is full of comments explaining what it's for an how to use it, so here's just a summary.

But basically, the idea is that:

SOME of the tabs are for more technical people to do the complicated work of capturing data sent via various communication prototocals, and mapping them to convenient, easy-to-understand, "friendly-named" inputs and outputs. These are the "bridge tabs." Techie folks copy existing templates in these tabs, modifying them to match the identifiers of their specific devices, creating their own friendly-named inputs and outputs

OTHER tabs are for people to play with those named inputs and outputs, connecting them willy-nilly to just see what happens, to quickly create different kinds of interactions, fail, learn, and try again. The main play tab is the "IoT Playground: Message Switchboard," but folks should be encouraged to make their own personal playground tabs into which they can copy inputs and output to play on their own.

## IoT Playground: Message Switchboard
This is the fun zone. Most of your users time will be spent in this tab.

On the lefthand side is the friendly-named inputs from all your defined devices that have outputs.

On the righthand side is the friendly-named outputs TO all your defined devices that have inputs

Fun stuff happens when you connect outputs from devices to inputs from other devices.

Learning happens when you use other node objects to modify the outputs to make even more insteresting things happen in the outputs.

Encourage your users to try stuff out!

FUN TIP: Users should feel free to create NEW tabs, an copy the inputs/outputs they care about to them, so they can play in a somewhat cleaner space. But also there's nothing wrong with makeing a mess!

## BLE In Bridge with Pareto Anywhere
This tab leverages the power of Pareto Anywhere, to access the data coming out of BLE devices and convert them to friendly-named inputs for the Switchboard.

You'll notice that the sample flows in this tab connect to a node named "saveToCaputredBLE." What this does it record every device that you're actively doing something with. Another Pareto node captures ALL the devices pareto sees. This empowers a page in the UI to show a display of all the devices you currently ARE and ARE NOT doing anything with, along with all the properties that are available to use. This makes it easy to find new devices and add them to the BLE In table with friendly names.

## MicroBit Bridge
This tab creates a bridge with Micro:bits devices (https://microbit.org/), which are popular microcontrollers for teaching interactive programming.

For this tab to work, you need to have a dedicated micro:bit device attached via USB cable to the computer running Node-RED.

TBD: For full setup instructions, see  [MicroBitsBridgeInstall.md](MicroBitsBridgeInstall.md)

## OSC Bridge
This tab is where you map OSC-based inputs and outputs to named inputs and outputs that can be used in the Switchboard.

## BLE Out Bridge
This tab is for connecting named outputs to SEND data to BLE devices, via EspruinoHub, an application that maps mqtt messages to BLE messages. 

Due to how BLE software accesses the BLE hardware on RPIs, EspruinoHub needs to run on a separate Raspberry Pi (or maybe you could use an additional Bluetooth dongle?). Instructions on how to install that are at [BLEOutBridgeInstall.md](BLEOutBridgeInstall.md).

## UI Generator
Turn inputs into a cool dashboard, and keep track of all the devices you've got in the system
Named inputs from the switchboard can be copied to this tab, then attached to UI elements that can be viewed at 
http://[THE RPI IP ADDRESS]:1880/ui/

## Graveyard
I use this tab as a scratchpad, and a place to put little string of nodes that I don't want to delete, but aren't using either.



