# Setup fresh Solus install 

Steps:
1. Download Solus iso from https://getsol.us/download/

   Choose Budgie version preferably (or whatever)

2. Burn iso to USB stick 

   using Rufus https://rufus.ie/ (Windows) 

   or Gnome MultiWriter (Linux)

3. Install Solus from USB stick. 

   Choose LVM if you need encryption.

4. Update Solus install (second icon on the bottom bar)

5. Open Terminal and install git using command:

   `sudo eopkg install git`

6. Recommended:  set your git details:

    `git config --global user.name "Your Name"`

    `git config --global user.email "you@your-domain.com"`

   To enable colors in the output, which can be very helpful:

    `git config --global color.ui auto`

   Generate SSH keys: use instructions from here:

    https://help.github.com/en/github/authenticating-to-github/connecting-to-github-with-ssh

    be sure to replace calls to apt-get (Ubuntu package manager) with eopkg (Solus package manager)

   Initialise repository:

    `cd ~; mkdir git; cd git; mkdir solus_setup; cd solus_setup; git init` 

7. Pull this repository locally

   `git clone https://github.com/acceleriser/solus_setup solus_setup`
   
8. Run the setup script setup-solus.sh 

   Go to script's folder. Make it executable if not (right click then properties).

   Open Terminal and run:

    `./setup-solus.sh`

   The script is an ansible playbook with a bash script meant to easily set up Solus workstation.

