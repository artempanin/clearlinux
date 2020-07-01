## My Personal Clear Linux setup guide

#### Cleanup (carefully check what you removing!)
    
    sudo swupd bundle-remove evolution
    sudo swupd bundle-remove geary
    
#### Configure swupd
    
    sudo swupd mirror --max-parallel-downloads=20
    
#### Additional network tools
    
    sudo swupd bundle-add wget network-basic clr-network-troubleshooter
    
#### Install "Dash to panel" 
    
    cd ~/Downloads && git clone https://github.com/home-sweet-gnome/dash-to-panel.git
    cd dash-to-panel && make install
    
Installation finished, navigate to gnome tweaks and enable it at "extensions"

#### Install brave
    
    sudo su -
    swupd bundle-add package-utils
    pip install python-dateutil
    dnf config-manager --add-repo https://brave-browser-rpm-release.s3.brave.com/x86_64/
    rpm --import https://brave-browser-rpm-release.s3.brave.com/brave-core.asc
    dnf download brave-browser
    rpm -U --nodeps brave-browser*.rpm
    rm -f brave-*.rpm
    sed -i 's\/usr/bin/brave-browser-stable\env FONTCONFIG_PATH=/usr/share/defaults/fonts /usr/bin/brave-browser-stable\g' /usr/share/applications/brave-browser.desktop
    exit

#### Virtualbox 6.1.10 Install

    sudo swupd bundle-add kernel-native-dkms
    cd ~/Downloads && wget https://download.virtualbox.org/virtualbox/6.1.10/VirtualBox-6.1.10-138449-Linux_amd64.run
    chmod +x VirtualBox-6.1.10-138449-Linux_amd64.run
    sudo ./VirtualBox-6.1.10-138449-Linux_amd64.run
***Optional! In my case without this steps my laptop is not booting anymore***

Find your boot partition by running (it will be the smallest one and named "EFI System")

    sudo fdisk -l
Creating mount point for boot partition. In my case boot partition is **/dev/nvme0n1p1**
    
    sudo mkdir /media/boot && sudo mount /dev/nvme0n1p1 /media/boot/
No idea why, but this worked for me
   
    sudo cp /media/boot/EFI/BOOT/BOOTX64.EFI /media/boot/EFI/org.clearlinux/mmx64.efi

Now, add your user to vboxusers group , otherwise USB pass-through will not work

    sudo usermod -a -G vboxusers $USER

Signing virtualbox modules

    cd ~/Documents && openssl req -new -x509 -newkey rsa:2048 -keyout MOK.priv -outform DER -out MOK.der -nodes -days 36500 -subj "/CN=VirtualBox/"
    sudo /usr/lib/modules/`uname -r`/build/scripts/sign-file sha256 ./MOK.priv ./MOK.der $(modinfo -n vboxdrv)
    sudo mokutil --import MOK.der

***Reboot!***
   
    sudo reboot

After reboot finish setup

    sudo /sbin/vboxconfig

Now you can use Virtualbox as usual

#### Telegram
    flatpak install flathub org.telegram.desktop

#### Simplenote installation with AppImage
    mkdir ~/.apps 
    cd ~/.apps/ && wget https://github.com/Automattic/simplenote-electron/releases/download/v1.19.0/Simplenote-linux-1.19.0-x86_64.AppImage
    chmod +x Simplenote-linux-1.19.0-x86_64.AppImage
    sudo wget https://raw.githubusercontent.com/Automattic/simplenote-electron/develop/resources/images/icon_256x256.png -O/usr/share/icons/hicolor/256x256/apps/simplenote.png
    echo -e "[Desktop Entry]\nType=Application\nName=SimpleNote\nIcon=simplenote\nExec=.apps/Simplenote-linux-1.19.0-x86_64.AppImage\nTerminal=false\nCategories=Office;" > ~/.local/share/applications/simplenote.desktop
    update-desktop-database ~/.local/share/applications/

#### Protonmail Desktop Client installation with AppImage
    mkdir ~/.apps
    cd ~/.apps/ && wget https://github.com/protonmail-desktop/application/releases/download/1.0.4/protonmail-desktop-unofficial-1.0.4-x86_64.AppImage
    chmod +x protonmail-desktop-unofficial*
    sudo wget https://raw.githubusercontent.com/protonmail-desktop/application/master/src/main/images/Icon.png -O/usr/share/icons/hicolor/256x256/apps/protonmail.png
    echo -e "[Desktop Entry]\nType=Application\nName=ProtonMail\nIcon=protonmail\nExec=.apps/protonmail-desktop-unofficial-1.0.4-x86_64.AppImage\nTerminal=false\nCategories=Network;" > ~/.local/share/applications/protonmail.desktop
    update-desktop-database ~/.local/share/applications/

#### OnlyOffice Desktop Editiors
    flatpak install flathub org.onlyoffice.desktopeditors

#### Audacious music player
    sudo swupd bundle-add audacious

#### Pycharm Professional Python IDE
    flatpak install flathub com.jetbrains.PyCharm-Professional

#### Wine (for winscp and other windows software)
    sudo swupd bundle-add wine

#### Winscp (sorry, but love this one)
    cd ~/Downloads/ && wget https://winscp.net/download/WinSCP-5.17.6-Setup.exe
This will install Winscp , if you running Wine first time accept & install all required libs

    wine WinSCP-5.17.6-Setup.exe 

#### Flameshot screenshot utility
    sudo swupd bundle-add qt-basic-dev
    cd ~/Downloads/ && git clone https://github.com/lupoDharkael/flameshot && cd flameshot
    sed -i '1i#include "qpainterpath.h"' src/third-party/Qt-Color-Widgets/src/color_wheel.cpp 
    sed -i '1i#include "qpainterpath.h"' src/tools/arrow/arrowtool.cpp
    qmake && sudo make install
    cd ~/Downloads && rm -rf flameshot
Installation finished, navigate to *Gnome Settings -> Keyboard Shortcuts (scroll to the bottom) -> +*

Name: Any 
Command: flameshot gui
Shortcut: bind something 

#### Sqlitebrowser

    cd ~/Downloads && git clone https://github.com/sqlitebrowser/sqlitebrowser.git && cd sqlitebrowser
    cmake . && make -j4
    sudo make install

#### Nextcloud
    flatpak install flathub org.nextcloud.Nextcloud

#### Sublime text
    flatpak install flathub com.sublimetext.three

#### VLC
    sudo swupd bundle-add vlc

##### Freshpaper (BING wallpapers daily, love it!)
    sudo pip install freshpaper
Just put a "freshpaper" in terminal , and wallpaper will be changed to most recent one

#### Vmware Workstation 15.5.6
    sudo swupd bundle-add kernel-native-dkms
    cd ~/Downloads/ && wget https://download3.vmware.com/software/wkst/file/VMware-Workstation-Full-15.5.6-16341506.x86_64.bundle
    chmod +x VMware-Workstation-Full-15*
Now you need to run bundle. It will ask you about folder location , answer is /etc/init.d

    sudo ./VMware-Workstation-Full-15.5.6-16341506.x86_64.bundle
Now we need to fix kernel modules , just follow the steps

    cd ~/Downloads && wget https://github.com/mkubecek/vmware-host-modules/archive/workstation-15.5.6.tar.gz
    tar -xzf workstation-15.5.6.tar.gz && cd vmware-host-modules-workstation-15.5.6/
    tar -cf vmmon.tar vmmon-only && tar -cf vmnet.tar vmnet-only
    sudo cp -v vmmon.tar vmnet.tar /usr/lib/vmware/modules/source/
    sudo vmware-modconfig --console --install-all
Now juts run Vmware Workstation and finish installation.

Only one problem that i am not solved yet, that after restart you need to run Vmware modules manually.

    sudo /etc/init.d/vmware start
    
#### EasySSH
    flatpak install flathub com.github.muriloventuroso.easyssh
