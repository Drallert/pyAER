
# Index

1. [Establish connection](#Establish-connection)
    1. [Linux](#Linux)
    2. [Windows](#Windows)
2. [Scorbot use tutorial with Scorbot Server](#Scorbot-use-tutorial-with-Scorbot-Server)
    1. [Prepare the session](#Prepare-the-session)
        * [TL;DR](#TL;DR-Too-long-didn't-read)
    2. [Understanding references](#Understanding-references)
    3. [How to make the robot move](#How-to-make-the-robot-move)
    4. [Configure_SPID and cameras](#Configure_SPID-and-cameras)
        1. [The real important part](#The-real-important-part)
        2. [TL;DR](#TL;DR)
    5. [Coding](#Coding)
3. [Frequently Asked Questions](#FAQ)

# Establish connection

## Linux 

1. Create public/private keypair (for ssh connection)
    * ```ssh-keygen -f <key_name>```
        * Enter password if desired
    
    You will get two different files named <key_name> and <key_name>.pub in the directory you run the command in

2. Send an e-mail to epinerof@us.es specifying the following information:
    * Full name 
    * Name of your organization
    * Public key (<key_name>.pub file) attached
    * Preferred username (optional)

    Once you've done this step, you will have to wait to receive a confirmation e-mail from the server's administrator.

3. Upon receiving the confirmation e-mail, you will already have your user created (you will receive a username in case you didn't specify one in the e-mail) and ready to go. So, in order to establish the connection execute the following command:

    * ```ssh -X -i /path/to/private/key/<key_name> <username>@150.214.140.189```

    If everything went correctly, you should have access to a terminal like this in your screen:

    ![terminal](./terminal.jpg)
    

4. Check everything runs fine by executing the GUI of the service (please be patient when using the GUI because it can take a little while to respond):
    * ```cd pyAER```
    * ```python3 main.py```

    A window like this should open in your PC:

    ![gui](./gui.jpg)

    Check the bottom left checkbox that says "Open device", then look at the terminal where you have the active connection. If the message "*Device found and initialized successfully*" is displayed, you have successfully connected to the server and everything is ready for you to begin. 
    
    [Device not found troubleshooting](#When-I-check-the-"Open-device"-checkbox-a-message-box-that-says-"Device-not-found,-try-again-or-check-the-connection"-is-displayed)

## Windows
1. Create public/private keypair (for ssh connection)
    * Download and install PuTTY client ([official website](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html))

    * Run PuTTYgen utility (it is installed with PuTTY). A window like this should appear:
        
        ![puttygen](./puttygen.JPG)

    * Now click on the button that says "Generate". The program will need you to move your mouse around the blank area, so just do as it says. Once that is out of the way, you will have generated your keypair and the window should look like this:

        ![puttygen2](./puttygen2.JPG)

    * Now save both private and public keys by clicking on the buttons that say so. You may also enter a passphrase for your key, which is recommended but optional. You may now close PuTTYgen 

    * Once you've saved both keys, you'll have two files: <key_name>.pub and <key_name>.ppk. At this point, you've finished generating the private and public key required.

2. Send an e-mail to epinerof@us.es specifying the following information:
    * Full name 
    * Name of your organization
    * Public key (<key_name>.pub file) attached
    * Preferred username (optional)

    Once you've done this step, you will have to wait to receive a confirmation e-mail from the server's administrator. Upon receiving the confirmation e-mail, you will already have your user created (you will receive a username in case you didn't specify one in the e-mail) and ready to go. However, you will need to install the Xming software as well as configure PuTTY in order for you to be able to use the X11 utilities (otherwise you can't use the graphical interface).  

3. Install Xming software ([official website](https://sourceforge.net/projects/xming/))
    * During the installation process, you may select the option "Don't install an SSH client", as you have already installed PuTTY: ![xming_install](./xming_install.JPG)

        You can just use the default configuration for everything else.

4. Run Xming
    * Executing the software will make a small "X" icon appear in your tray bar:

        ![xming_icon](./xming_icon.jpg)
    
        This means Xming is being executed. 
5. Configure PuTTY connection
    * Open PuTTY and create a new session named however you want. This is recommended so that you only have to perform these steps once and after that the connection is as simple as double-clicking the session you've created.
    
    * Under "Host Name (or IP address)" type the following: ```<username>@150.214.140.189```

    * Uncollapse the "SSH" section under "Connection" in the tree view that's in the left side of the program. Now select "X11" and check the box that says "Enable X11 forwarding". In the input box that says "X display location" enter "localhost:0.0".

        ![X11_forward](./x11_forward.jpg) 
    
    * Now select the "Auth" section that is also under "Connection". The last option displayed lets you select a private key file. Click on "browse" and select the <key_name>.ppk file that you generated earlier.

        ![ppk_select](./ppk_select.jpg) 

    * Return to the session view by clicking on the "Session" section that is the first one in the tree view and save your session. 
    
6. Now all you have to do is double-click on your connection's name and you will be connected to the server with your username. A window like this should appear before you: 

    ![putty](./putty.jpg)


7. Last, but not least, you should check that the Xming X server is working correctly, as well as test your configuration by doing the following:
    
    * ```cd pyAER```
    * ```python3 main.py```

    A window similar to this one should appear (please be patient, from now on it can take a while to load):
    ![pyaer](./pyaer.jpg)

    You just have one more thing to check, and you can do that by checking the bottom left checkbox that says "Open device". If a message that says "*Device found and initialized successfully*" is displayed in the PuTTY console, then you're ready to begin.


# Scorbot use tutorial with Scorbot Server

*NOTE: It is important to note that this access is quite limited, so that you only may perform certain activities. However, if you need to use any command or utility that isn't available out of the box, contact epinerof@us.es with your requests.*

## Prepare the session

First of all, each joint of the robot has to be positioned in its home area (home in this robot isn't just one position but a range of them). So for us to work with it, before anything we do we have to command the robot to send all of its joints to their home.

In order to do that, there is a function called ```Search_Home``` that implements said process, starting from the 1st joint (a.k.a. the base) up to the 4th one. This will take a while, as each joint is sent to one of its limit positions and then progressively moves in the opposite direction searching for its home.

The ```Search_Home``` function is an all-in-one button in the GUI as well as in the code itself; however, there are also separate functions, called ```search_Home_JX``` (where ```X``` is the joint number), that are callable from the code that implement this functionality but for just one joint. Each of these functions also has its corresponding button in the GUI for convenience. 

So, to sum it all up, any time that anyone has to work with the robot, the first thing to do is to command it to search the home position for all of its joints, whether it is via the ```Search_Home``` button or function or via the ```search_Home_JX``` buttons or functions, and after that you have to use the ```Configure_Init``` button so that the home position is considered the initial one.

### **TL;DR** (Too long, didn't read)

Just click on the ```Search_Home``` button before anything else and let it finish, then push the ```Configure_Init``` button when initiating a new work session and you should be fine.

NOTE: ```search_Home_JX``` functions should be used in case one of the joints is suspected to have lost its home reference, so that you don't have to wait for all joints to search their home in case just one of them has gone astray.

## Understanding references

In order to make this section a little bit more understandable, let's take a look at the graphical interface first:

![gui](./gui.jpg)

As you can see, each motor has 6 different parameters and almost all of them are used to determine how it should move. However, if you're only interested in actually moving them and not in how they move you're lucky, because you're only going to need one of these parameters, and that would be the ```ref_MX``` one.

So what does exactly this "reference" mean? You can think of it as a way of telling each motor the position it should be in. For example, the reference "0" would be the motor's initial position (which should be the home position if you followed the steps under the "Prepare the session" section), then any positive reference would send the motor in one direction and any negative reference would send it in the opposite one.

And that's everything you need to know if you're only interested in moving the robot. As for how to map references to angles, we're providing you with some functions in the code to help you with that, as that kind of mapping is specific to each robot and to each motor within a given robot. That's right, a reference of say, 50, can put the first motor in an angle ```alpha``` but the second motor in another completely different angle ```beta```, so it's best if you just adhere to the mappings we offer you.

## How to make the robot move

So now that the concept of reference has been explained, we can move on to moving the robot. As it has been said, the only important parameter here would be the reference, so you can rest easy knowing that all of the other parameters won't have to be changed, at least from a user's point of view. 

Therefore, in order to move the robot you have to first do the actions specified in the "Prepare the session section". Then, everything you need to do is change a joint's reference (via the graphical interface or via code) and hit the ```Configure_SPID``` button (or call the ```Configure_SPID``` function in the code). As long as you've specified some different references, you should see some movement going on. 

## Configure_SPID and cameras

In recent updates, we've added some functionality in order to make available a couple of cameras in the laboratory, so that you can see (and maybe work with) the robot's movement from a visual perspective and not only from a coding perspective.

As of the writing of this, the cameras are available in the graphical interface via two buttons, one for the front camera and another one for the side camera. Please, keep in mind that, as everything is setup remotely, latency is expected when activating the cameras, as carrying video over the internet is quite a heavier data payload than just SSH commands.

### The real important part

This section is actually written because we have identified a problem with how the ```Configure_SPID``` function is implemented within the GUI, and this problem affects the cameras' setup. 

Basically, when you hit the ```Configure_SPID``` button, a bunch of commands are being sent to the robot, and the GUI is frozen during that time. It is actually not that long that the graphical interface is frozen, but it is enough to make the cameras' feed miss continuity, in other words, when you use the ```Configure_SPID``` button to command movement, cameras freeze for a bit and when they unfreeze the robot has probably finished moving, meaning that we've missed its movement entirely.

However, we've found a workaround for this situation: we've implemented a "light" function for each joint that moves that specific joint separately from the rest so that the only information that is sent is that concerning the movement of that joint, which turns out to be fast enough not to freeze the cameras.

However, for this to work you need to press the ```Configure_SPID``` button at least once during the session. We heavily encourage you to do it right after the session preparement process, so that you can forget about it for the rest of the time and only use the light functions to move the robot.

### TL;DR

In order to move the robot and be able to see it moving in the cameras, you have to hit ```Configure_SPID``` at least once after the initial setup (described [here](#Prepare-the-session)) and then use the ```Send JX ref``` buttons (within the GUI) or functions (within the code).

If you don't do this and program the movement with the ```Configure_SPID``` function or button, you will freeze the cameras and probably won't be able to see the movement.

## Coding

So, the graphical interface is nice and all, however, we understand that it is not suitable to be used when the robot is intended to be moved in a programatically way, for example, with a Spiking Neural Network, as we don't expect the network to handle the robot's movement the same way we do.

Therefore, the same functions and variables that are bound to the buttons in the graphical interface are also accessible within the code, what means that all you have to do in order to use them is import the main module and use it just like any other Python module.

A small demo script can be found [here](./demo.py). 

# FAQ 


## When I check the "Open device" checkbox a message box that says "Device not found, try again or check the connection" is displayed

![faq1](./faq1.JPG)

There is nothing wrong with this, it normally means that the robot is not powered. This shouldn't happen during a scheduled work session, so it should typically occur when you're trying to check whether the GUI works for you or not. 

## What are the parameters that are not "ref_MX" for?

These parameters are used to control the SPID (Spiking PID) of each joint, and they are already configured for this robot. You shouldn't be touching these unless you really know what you're doing. For more information, please refer to this paper: [A Neuro-Inspired Spike-Based PID Motor Controller for Multi-Motor Robots with Low Cost FPGAs](https://www.mdpi.com/1424-8220/12/4/3831/htm)