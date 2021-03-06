# Troubleshooting

The page details how to fix common problems that can occur when using pyControl.  If you encounter a problem that is not covered on this page, please contact the [google group](https://groups.google.com/forum/#!forum/pycontrol).

### Can't open GUI

To open the GUI you need to run the file *pyControl_GUI.py* using Python.  One way to do this is to set the file association for *.py* files to Python 3 so that you can run the file by double clicking it.  Alternatively you can open a command prompt, change directory to the folder containing *pyControl_GUI.py* and run the file with the command `python pyControl_GUI.py`.

If the GUI does not open this is probably because you do not have the required [dependencies](../index.md#dependencies) installed, you should see an error message indicating which dependency could not be loaded. 

### Can't connect to acquisition board.

If the GUI status says 'Connection Failed' when you try to connect to the board, reset the board using the *Reset* button on the breakout board or micropython microcontroller, then try connecting again.

### Can' transfer files to pyboard

If you get a message saying *Error: Unable to transfer file.* this usually means that the filesystem on the pyboard has got corrupted. To fix this problem, reset the filesystem by following the instructions [here](https://docs.micropython.org/en/latest/pyboard/tutorial/reset.html#factory-reset-the-filesystem).  

The instructions say to use the *Reset* and *USR* buttons and LEDs on the micropython board for the file system reset.  Depending on how the pyControl breakout board is mounted it may be hard to access these.  However the *Reset* and *USR* buttons on the front of pyControl breakout board 1.2 have identical functionality to those on the pyboard, and indicator LEDs 3 and 4 from the left on the breakout board have the same behaviour as the orange and green LEDs on the pyboard, so the filesystem can be reset with access only to the front of the breakout board.  

After the filesystem reset has finished, reset the board again using the *Reset* button, then connect to the board with the GUI.  The filesystem reset removes both the pyControl framework and any hardware definition from the pyboard so you will have to reload them using the board config menu.  

To reduce the likelihood of filesystem corruption, it is strongly recomended to disable the pyboards filesystem from acting as a USB flash drive before loading the framework.  This can be done using the *'Disable USB flash drive'* option in the board config menu.

### Board acting funny / reliability problems

If a board is acting strangely, e.g. giving error messages like *PyboardError('could not exec command')* or *pyControl Framework:Import error* try the following steps to fix the problem:

- Disconnect from the board using the GUI and reset it using the *Reset* button either on the pyboard or on the pyControl breakout board.
#
-  If resetting the board does not solve the problem, it is possible the pyboard's filesystem has got corrupted.  Follow the instructions above under *Can' transfer files to pyboard* to reset the pyboard filesystem.

These two steps will solve the great majority of board misbehaviour.  If you continue to have reliabilty problems:

- Make sure you have disabled the pyboard from acting as a USB flash drive using the 'Disable USB flash drive' option in the board config menu. This stops the computers from trying to access or modify the boards file system which can otherwise cause reliability issues.
#
- On windows, use the micropython USB drivers rather than the generic windows USB serial device drivers. To check which drivers you are using, look in the windows device manager under *Ports (COM & LPT)*, if your pyboard shows up as a *USB Serial Device* then you are using the generic windows driver, if it shows up as a *Pyboard USB Comm port* you are using the micropython USB drivers. For information on installing the microputhon USB drivers see [micropython windows setup](http://micropython.org/resources/Micro-Python-Windows-setup.pdf) and the micropython [docs](http://docs.micropython.org/en/latest/pyboard/pyboard/tutorial/repl.html).  The micropython drivers are unsigned so to install them on Windows 10, follow the instructions [here](https://www.maketecheasier.com/install-unsigned-drivers-windows10/) under *Install Unsigned Drivers from Advanced Boot Menu*.  You should only need to do this the first time you install the drivers on a computer.  Note, the micropython USB driver is stored on the pyboards flash drive, so if you have disabled the pyboard from acting as a flash drive using the GUI's board config menu, you will need to re-enable it in order to access the driver file from windows. 

### Unable to setup state machine

If you get a message saying *Error: Unable to setup state machine* when you upload a task, this usually means that there is a problem with the task definition file which gives an error when it is imported.  The error message will be followed by a traceback saying the line in the task file where the error occured and what the error was.  Task definition files are renamed *task_file.py* when they are transferred to the pyboard, so the traceback will refer to errors in the task definition file using this name.

### Error during framework run

If you get a message saying *Error during framework run* while the framework is running, this usually indicates there is a problem with the task file that does not prevent the file being imported but only occurs while the task is running.  This is often due to errors in state behaviour functions which only occur when the function is called.  The error message will include a traceback indicating the line number in the task file where the error occured and what the error was.

### Upgrading Micropython

Micropython boards (pyboards) need to be running a fairly recent version of the Micropython to work with pyControl.  When you connect to a pyboard with the GUI, the micropython version installed on the board is displayed.  If the micropython version is <1.9 update micropython by doing the following (windows):

1.  Download and install the software [DfuSe demo](https://www.st.com/en/development-tools/stsw-stm32080.html).
2.  Download the latest numbered release of the firmware (e.g. v1.10) from the micropython [download](http://micropython.org/download) page.  Note, there are two different version of the pyboard microcontroller, *PYBv1.0* and *PYBv1.1*, which require different versions of the firmware.  The board version will be printed on the microcontroller, make sure to download the matching version of the firmware.  There are various different versions of the firmware for each board with names like *standard* and *double FP* - download the *standard* version.
3.  Open the pyControl GUI and connect to your board.  Open the *Config* menu and select the option *Device firmware update (DFU) mode*.  The pyControl GUI will put the board in DFU mode and disconnect from it.
4.  Open the program *DfuSe demo*, in the *Available DFU Devices* drop down menu it should say *STM Device in DFU Mode*, indicating it has found the board and it is in DFU mode.  In the *Upgrade or Verify Action* box, press the button *Choose*, then select the micropython firmware file you downloaded.
5.  Press *Upgrade* and then *Yes* in the dialog box.  *DfuSe demo* will upload the new firmware to the board. When you see a message saying *Upgrade successful*, quit *DfuSe demo*.
6.  Press the reset button on the pyboard to exit DFU mode.  Connect to the board using the pyControl GUI and the micropython version should be updated.

For information about updating micropython on Linux/Mac see [here](https://github.com/micropython/micropython/wiki/Pyboard-Firmware-Update). 