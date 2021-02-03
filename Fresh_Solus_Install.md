# Setup fresh Solus install 

## Download, burn, install and update
1. Download Solus iso from https://getsol.us/download/. Choose Budgie version preferably.

1. Burn iso to a Solus Live USB stick using Rufus https://rufus.ie/ (Windows) or Gnome MultiWriter (Linux) or Balena Etcher (Mac).

1. Boot into the Solus Live USB stick and install Solus. Choose LVM if you need encryption.

1. Boot into the installed Solus and update the software.


## Make Solus suitable for getting stuff done

### Option 1: use script
#### Install Git

1. Open Terminal and install git using command:\
   `sudo eopkg install git`

1. Recommended:  set your git details:\
    `git config --global user.name "Your Name"`\
    `git config --global user.email "you@your-domain.com"`

1. To enable colors in the output, which can be very helpful:\
    `git config --global color.ui auto`

1. Generate SSH keys: use instructions from here:  
   https://help.github.com/en/github/authenticating-to-github/connecting-to-github-with-ssh  
   take care be sure to replace calls to apt-get (Ubuntu package manager) with eopkg (Solus package manager)

1. Initialise repository:\
    `cd ~; mkdir git; cd git;`

#### Pull the setup script

1. Pull this repository locally:\
   `git clone https://github.com/neurogamer/tools_of_the_trade`\
   or this script:\
   `wget https://gist.github.com/MrDrMcCoy/c23f1d215b8725ac3e1193686b7ebbfc`
   
1. Run the setup script downloaded above:\
   Go to script's folder. Make the .sh or .bash script executable (right click > properties).\
   Open Terminal and run:\
    `./setup-solus.sh`\
    `./setup-solus.bash`\
   Any of these scripts will automatically set up Solus workstation.

### Option 2: directly install software customised to your needs

#### remove sudo reauthentication timeout
```
sudo sed -i.bak -e '$a\' -e 'Defaults timestamp_timeout=-1' -e '/Defaults timestamp_timeout=.*/d' /etc/sudoers
```

#### set better ssh defaults
```
sudo sed -i.bak -e '$a\' -e 'StrictHostKeyChecking=no' -e '/StrictHostKeyChecking=.*/d' /etc/ssh/ssh_config
sudo sed -i.bak -e '$a\' -e 'UserKnownHostsFile=\/dev\/null' -e '/UserKnownHostsFile=.*/d' /etc/ssh/ssh_config
sudo sed -i.bak -e '$a\' -e 'GlobalKnownHostsFile=\/dev\/null' -e '/GlobalKnownHostsFile=.*/d' /etc/ssh/ssh_config
sudo sed -i.bak -e '$a\' -e 'Compression=yes' -e '/Compression=.*/d' /etc/ssh/ssh_config
```

#### Remove useless default apps
`sudo eopkg remove -y gnome-mpv hexchat hythmbox thunderbird`

#### Install packages from standard repo (adjust list as needed)
```
sudo eopkg install -y audacity calibre cifs-utils cmus conky curl davfs2 dbeaver deadbeef docker evolution feh fuse gimp htop inkscape iotop keepass lutris lxappearance lynx mc mpd mtr neofetch pavucontrol powertop ranger remmina scribus shellcheck sshfs-fuse steam terminator tlp umbrello unrar variety vim virt-manager virtualbox{,-current} vlc vscode wine zsh
```

#### Compile 3rd-party packages

##### msfonts
```
sudo eopkg bi --ignore-safety -y https://raw.githubusercontent.com/getsolus/3rd-party/master/desktop/font/mscorefonts/pspec.xml
sudo eopkg it mscorefonts*.eopkg;sudo rm mscorefonts*.eopkg
```

##### teamviewer
```
sudo eopkg bi --ignore-safety https://raw.githubusercontent.com/getsolus/3rd-party/master/network/util/teamviewer/pspec.xml
sudo eopkg it teamviewer*.eopkg;sudo rm teamviewer*.eopkg
sudo systemctl start teamviewerd.service
```

##### chrome
```
sudo eopkg bi --ignore-safety https://raw.githubusercontent.com/getsolus/3rd-party/master/network/web/browser/google-chrome-stable/pspec.xml
sudo eopkg it google-chrome-*.eopkg;sudo rm google-chrome-*.eopkg
```

##### Mendeley Desktop
```
sudo eopkg bi --ignore-safety https://raw.githubusercontent.com/getsolus/3rd-party/master/office/mendeleydesktop/pspec.xml
sudo eopkg it mendeleydesktop*.eopkg;sudo rm mendeleydesktop*.eopkg
```

##### PyCharm
```
sudo eopkg bi --ignore-safety https://raw.githubusercontent.com/getsolus/3rd-party/master/programming/pycharm/pspec.xml
sudo eopkg it pycharm*.eopkg;sudo rm pycharm*.eopkg
```

##### EnPass
```
sudo eopkg bi --ignore-safety https://raw.githubusercontent.com/getsolus/3rd-party/master/security/enpass/pspec.xml
sudo eopkg it enpass*.eopkg;sudo rm enpass*.eopkg
```

##### Set up Visual Studio Code extensions
```
code-oss --install-extension Tyriar.sort-lines
code-oss --install-extension ms-python.python
code-oss --install-extension redhat.vscode-yaml
code-oss --install-extension shardulm94.trailing-spaces
code-oss --install-extension shd101wyy.markdown-preview-enhanced
code-oss --install-extension timonwong.shellcheck
code-oss --install-extension wholroyd.jinja
code-oss --install-extension yzhang.markdown-all-in-one
```

##### Add user to groups
```
for group in docker fuse davfs2 input libvirt vboxusers; do
  sudo gpasswd -a $USER $group
done
```

