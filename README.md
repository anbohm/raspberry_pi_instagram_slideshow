# Raspberry Pi Instagram Slideshow

I'm a volunteer at the [Idea Fab Labs](https://santacruz.ideafablabs.com/) maker/hacker/artspace here in Santa Cruz, 
and I was asked to set up a Raspberry Pi for our weekly open house and for IFL booths at events, so that all you 
had to do was plug it into a large monitor and it would start running a slideshow of 
[Idea Fab Labs' Instagram feed](https://www.instagram.com/ideafablabs/) of photos of projects, facilities, and events.

I wrote the slideshow program in Python using the Tkinter GUI interface, and I've tested it with Raspberry Pi 2 Model B 
computers running Raspbian Wheezy with the LXDE GUI desktop, Raspbian Jessie with the Pixel GUI desktop, and Raspbian 
Stretch with the Pixel GUI desktop.

If you'd like to do something similar with your own Instagram feed, the instructions below will walk you through 
getting the slideshow set up on your Raspberry Pi.

Because (at least as of September 2017) the Instagram API in [Sandbox Mode](https://www.instagram.com/developer/sandbox/) 
only gets the 20 most recent photos from an Instagram account, and in the interest of both reducing bandwidth and being able to run the slideshow even when your internet connection is down, once an hour the program checks Instagram to see if any new photos have been posted to the account, and if so, downloads them to its `instagram_photos` directory. (If you like you can also copy other jpg files to the directory and they will also be included in the slideshow -- for that matter I used the [InstaG Downloader Chrome Extension](https://chrome.google.com/webstore/detail/instag-downloader/jnkdcmgmnegofdddphijckfagibepdlb?hl=en) to download all the photos from the Idea Fab Labs Instagram feed that were older than the 20 most recent, so they would be in the `instagram_photos` directory too.)

You can configure whether the slideshow displays photos in random order, the order they are in the `instagram_photos` 
directory, or sorted lexicographic order (the default is random), and/or configure how long the slideshow displays each 
photo before moving on to the next (the default is 15 seconds), by pressing `Esc` to exit fullscreen mode, and then 
selecting `Preferences ...` from the `Edit` pull-down menu. After making configuration changes, click `OK`, and then 
return to fullscreen display by either pressing `Esc` again or selecting `Enter Fullscreen` from the `View` pulldown menu.

Note: I've now also written a 
[Raspberry Pi Instagram Slide and Video Show](https://github.com/tachyonlabs/raspberry_pi_slide_and_video_show) version 
that downloads and shows Instagram videos in addition to photos, so check that out as well.

## Getting the slideshow set up on your Raspberry Pi
Follow these instructions to get the slideshow set up and working on your Raspberry Pi:

1. **Set your Raspberry Pi to autologin into the GUI desktop if it doesn't already**

    The slideshow runs in the Raspberry Pi GUI desktop, so follow these instructions to set your Raspberry Pi to 
    autologin to the GUI if it isn't already doing it: 
    https://raspberrypi.stackexchange.com/questions/7261/how-to-set-my-raspberry-pi-to-boot-into-the-gui

2. **Configure your Raspberry Pi to connect to your Wifi if you haven't done so already**

    To download Instagram photos (or for that matter to follow the instructions link in the previous step) your 
    Raspberry Pi needs to be connected to the Internet. If you haven't already done so, do the following to select your 
    Wifi network and enter the password:

    * For Raspbian Wheezy and the LXDE GUI desktop, in the Raspberry Pi GUI Menu select `Preferences` and then `WIFI Configuration`.

    * For Raspbian Jessie or Raspbian Stretch, with the Pixel GUI desktop, click the wifi icon at the top right of the 
    taskbar.

3. **Do a general update of your Raspberry Pi software if you haven't done so recently**

    To install updates to your Raspberry Pi, in a terminal window, run
    ```
    sudo apt-get update
    sudo apt-get upgrade
    sudo reboot
    ```

4. **Python 3 or Python 2?**

    The Raspberry Pi Instagram Slideshow Python code will work with either Python 3 or Python 2, but as 
    described in the steps below, how you install the required Python libraries and how you run the program 
    will vary a little depending on which version of Python you want to use. Basically, for Python 2 you will be 
    running `python` and `pip`, and for Python 3 `python3` and `pip3`.
    
5. **Install python-dev and python-setuptools if you haven't done so already**

    The slideshow is written in Python, and before you can install Python libraries for talking with the Instagram API 
    and displaying photos you will need to install python-dev and python-setuptools if you haven't already done so. 
    
    Enter the following into a terminal window if you want to use Python 2:
    ```
    sudo apt-get install python-dev python-setuptools
    ```
    Or if you want to use Python 3:
    ```
    sudo apt-get install python3-dev python3-setuptools
    ```
6. **Install pip (the Python package manager) if you haven't done so already**

    Type the following into a terminal window if you want to use Python 2:
    ```
    sudo apt-get install python-pip
    ```
    Or if you want to use Python 3:
    ```
    sudo apt-get install python3-pip
    ```
7. **Install the Python requests library**

    The slideshow uses the Python requests library to talk to the Instagram API -- install it by entering the following 
    into a terminal window if you want to use Python 2:
    ```
    sudo pip install requests
    ```
    Or if you want to use Python 3:
    ```
    sudo pip3 install requests
    ```
8. **Install a library for handling JPEG images**

    Instagram photos are downloaded as JPEG images, so type the following into a terminal window:
    ```
    sudo apt-get install libjpeg8-dev
    ```

9. **Install the Pillow Python Imaging Library**

    The slideshow uses the Pillow library to display the Instagram photos, so install Pillow by entering the following 
    into a terminal window if you want to use Python 2:
    ```
    sudo pip install Pillow
    sudo apt-get install python-imaging-tk
    ```
    Or if you want to use Python 3:
    ```
    sudo pip3 install Pillow
    sudo pip3 install pillow --upgrade
    sudo apt-get install python-imaging-tk
    ```
    You may get error messages about missing libraries for other image formats, but right now we're only concerned that 
    the JPEG library in step 8 was installed.

10. **Create a directory for the slideshow, and copy the files from this repo into it**

    Create a directory `raspberry_pi_instagram_slideshow` in the `/home/pi/` directory, and copy the files `instagram_slideshow.py`, `tkSimpleDialog.py`, and `instagram_slideshow.bat` from this repo into it. (Or if you prefer you can use a different slideshow directory name and/or location, adjusting its name/location in subsequent steps accordingly.)

11. **Edit `instagram_slideshow.bat` to specify Python 2 or Python 3.**

    Use a text editor to edit `instagram_slideshow.bat` so that depending on whether you're using Python 2 or 3, the 
    line for the Python version you're using will be uncommented, and the line for the other version will be commented 
    out.

12. **Make `instagram_slideshow.bat` executable.**

    Make the file `instagram_slideshow.bat` executable by entering the following into a terminal window:
    ```
    chmod 755 /home/pi/raspberry_pi_instagram_slideshow/instagram_slideshow.bat
    ```

13. **Get the Instagram access token for the account you want to use with the slideshow, and enter it into the 
slideshow code**

    There are other ways to download Instagram photos than their API, but I used their API. When I was using the 
    Instagram API for another project last year, all you needed to do to download photos from an account was to 
    register as a developer and get a client id, but now you have to go through a lot more steps to generate an access 
    token for the account you want to access. To get the access token I followed the instructions on this 
    [How to get Instagram API access token and fix your broken feed](https://github.com/adrianengine/jquery-spectragram/wiki/How-to-get-Instagram-API-access-token-and-fix-your-broken-feed) 
    page, and as I do some Django development I used the Django development server (as 
    `python manage.py runserver 0.0.0.0:8000` on my Windows system) in their "your favorite MAMP, LAMP, Node whatever 
    you use to create a local server" step.

    When you get the access token you need, use a text editor or IDE to substitute it for "put your access token here" 
    in the line
    ```
    self.INSTAGRAM_ACCESS_TOKEN = "put your access token here"
    ```
    in `instagram_slideshow.py`.

14. **Set the Raspberry Pi to run the slideshow automatically after the GUI loads**

    The issue here is that you want the program to run not only after the Raspberry Pi boots, but after the GUI loads.

    What worked for me was following the instructions in one of the posts in the 
    [How to launch programs on LXDE startup](https://www.raspberrypi.org/forums/viewtopic.php?f=27&t=11256) topic in 
    the raspberypi.org forums, doing

    * For Raspbian Wheezy with the LXDE GUI desktop: `sudo nano /etc/xdg/lxsession/LXDE-pi/autostart`

    * For Raspbian Jessie or Raspbian Stretch, with the Pixel GUI desktop: 
    `sudo nano /home/pi/.config/lxsession/LXDE-pi/autostart`

    to edit the autostart file, adding the line
    ```
    @/home/pi/raspberry_pi_instagram_slideshow/instagram_slideshow.bat
    ```
    to the end of the file, and saving it.

15. **Disable the Raspberry Pi screensaver**

    It's great to be have the Raspberry Pi set up so that all you have to do is plug it in and the slideshow will 
    start, without needing to have a keyboard and/or mouse connected, but pretty sad if the slideshow only lasts for 
    ten or fifteen minutes until the screensaver blanks the screen. :-(

    * What worked for me with Raspbian Wheezy and the LXDE GUI desktop was following the "2 – Disabling the blank 
    screen forever" instuctions in this 
    [How to Disable the Blank Screen on Raspberry Pi (Raspbian)](http://www.geeks3d.com/hacklab/20160108/how-to-disable-the-blank-screen-on-raspberry-pi-raspbian/) 
    HackLab post.

    * What worked for me with Raspbian Jessie with the Pixel GUI desktop, was adding the line 
    `xserver-command=X -s 0 dpms` to `/etc/lightdm/lightdm.conf` as described in this 
    [Raspberry Pi Stack Exchange comment](https://raspberrypi.stackexchange.com/questions/752/how-do-i-prevent-the-screen-from-going-blank/51687#51687).

    * What worked for me with Raspbian Stretch with the Pixel GUI desktop, was following the instructions in 
    [https://www.raspberrypi.org/documentation/configuration/screensaver.md](https://www.raspberrypi.org/documentation/configuration/screensaver.md)
    to install the X Windows screensaver application by typing
    `sudo apt-get install xscreensaver`
    into a terminal window, rebooting the Raspberry Pi after the installation finishes, bringing up the Screensaver 
    Preferences dialog by selecting Preferences/Screensaver in the Raspberry Pi GUI menu, and changing "Mode" to 
    "Disable Screen Saver".

## Running the slideshow on your Raspberry Pi

Once you've followed all the above steps, reboot your Raspberry Pi, and after the GUI desktop starts the slideshow 
should start running automatically.

The first time you run the slideshow, it will create an `instagram_photos` subdirectory, and you will see messages 
about photos being downloaded in a terminal window before it starts displaying the photos.

If instead of showing messages about downloading photos, it immediately blanks the screen, this means that it was not 
able to download any photos to display due to not having a connection to the Internet (if there's no Internet 
connection the slideshow will just display stored photos, but to get stored photos in the first place you need to be 
connected to the Internet) or there being a problem with your Instagram access token. To see what's going on, use 
`Alt-Tab` to switch to the terminal window and see the status messages.

If at any point you want to close the slideshow, here are two ways to do it:

* Do an `Alt-Tab` to switch to the terminal window, and then do a `Ctrl-C` to close the slideshow but leave the 
terminal window open, or just close the terminal window to close both of them.

* Press `Esc` to get out of fullscreen mode, and then you can either (1) select `Exit` from the `File` pull-down menu, 
(2) click the `X` close button at the top-right of the slidehow window, or (3) do a `Ctrl-C` from the terminal window 
or close the terminal window.
