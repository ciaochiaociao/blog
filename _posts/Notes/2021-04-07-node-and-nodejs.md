---
title: Node and NodeJS
---

```
# NodeJS, Node, NPM, NVM
## install nodejs (node), npm 
### (Distro-Stable Version for Ubuntu) in ubuntu 16.04
sudo apt-get install nodejs
sudo apt-get install npm

## Using Ubuntu (official)
curl -fsSL https://deb.nodesource.com/setup_15.x | sudo -E bash -
sudo apt-get install -y nodejs

## Using conda
conda install -c conda-forge nodejs

#note: Because of a conflict with another package, the executable from the Ubuntu repositories is called nodejs instead of node. Keep this in mind as you are running software.

### Using a PPA
curl -sL https://deb.nodesource.com/setup_8.x -o nodesource_setup.sh
sudo bash nodesource_setup.sh
sudo apt-get install nodejs

#for npm to compile code from source
sudo apt-get install build-essential

## Using nvm (Node.js version manager) (at the level of an independent directory within your home directory)
sudo apt-get install build-essential libssl-dev
curl -sL https://raw.githubusercontent.com/creationix/nvm/v0.33.8/install.sh -o install_nvm.sh
bash install_nvm.sh
nvm ls-remote
nvm ls
nvm install 8.9.4
nvm use 8.9.4
npm install ...  #to the Node.js project’s ./node_modules directory
npm install -g express  #globally ~/.nvm/node_version/lib/node_modules/package_name (can run in cli)
npm link express (can run global one in local sphere)
```

