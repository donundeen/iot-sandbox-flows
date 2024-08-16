# iot-sandbox-flows
Node-RED flows for an Internet-of-Things sandbox

This repo is intended to be cloned from within a Node-RED installation that has the projects feature enabled.

With these Node-RED flows installed, you can create an environment that makes it easy for people to collaboratively interconnect inputs and outputs of diverse devices, leveraging the power of Paretoanywhere and the Internet of Things.

It's expecially useful in an educational environment where you want students to be able to play with interaction possibilities without having to worry too much about the nuts and bolts of implementation.


# Installation

## Set up a Raspberry Pi or similar computer with Node-RED and ParetoAnywhere

Using Raspberry Pi Imager, create an image of:
- Raspbian Lite, with ssh enabled
(more thorough instructions are at this link)

once you've logged into your Pi, run:

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

Then go install ParetoAnywhere, using these instructions:
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






