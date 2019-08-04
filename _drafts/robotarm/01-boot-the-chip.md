* Blink a LED

The first thing to do to blink a LED from the CHIP computer is to be able to use the CHIP computer itself. Once taken out of its protective bag, you just need to connect it through USB to any computer:




To connect to the CHIP, plug it and type the following commands in the command line of your system (I'm using mac, I guess they will work also for linux, but Windows users will need to find analogous commands):

    ls /dev/tty.*     # Shows all available connections
    screen /dev/tty.xxxxx 9600    # Connects to the chip
    # use the following user/pass = root/chip
    export TERM=ansi # So you can see anything with the following command, although this is not perfect
    nmtui # To try and connect to wifi
   
   
I had an interesting problem with the wifi: I used pretty wild characters in the SSID, and the chip botched them when they were displayed, and I couldn't write them by hand. So this is what I did to solve it:

    nmcli device wifi # shows all networks
    echo `nmcli device wifi | grep "{something to get only the one you like}" | awk '{print $1 " " $2}'` > SSID.txt
    nmcli device wifi connect "`cat SSID.txt`" password "{your password}"

Done! Now I get internet and I can update my chip:

    apt update
    apt dist-upgrade
    apt install ssh

After these commands, you can connect through your local network to the CHIP:

    ssh root@chip.local
   
   
It would be good to change your ssh password by the way:

    passwd
    
You can also do some basic config, such as installing useful packages:

    apt install git emacs

Or fixing locales (mine didn't work properly):

    apt-install --reinstall locales # As beforehand, I had no locale-gen
    locale-gen en_US.UTF-8
    echo "LC_ALL=en_US.UTF-8" >> /etc/default/locale
    echo "LANG=en_US.UTF-8" >> /etc/default/locale
    echo "LANGUAGE=en_US.UTF-8" >> /etc/default/locale
    sudo reboot

That's all! Now let's go and blink a LED.
