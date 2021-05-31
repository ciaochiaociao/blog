---
title: Linux Package
---

```bash
# Add GPG key
sudo apt-key add
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
sudo apt-add-repository

sudo apt-key fingerprint 0EBFCD88  # Check
apt-cache madison <package-name>  # list all versions of this package available
apt list -a <package-name> # list all versions of this package available


# apt-get upgrade when available but if you want to pin at a certain version:
echo "mongodb-org hold" | sudo dpkg --set-selections
echo "mongodb-org-server hold" | sudo dpkg --set-selections
echo "mongodb-org-shell hold" | sudo dpkg --set-selections
echo "mongodb-org-mongos hold" | sudo dpkg --set-selections
echo "mongodb-org-tools hold" | sudo dpkg --set-selections
```

