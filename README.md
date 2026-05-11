# **Tutorial for Installing Google Play** **on DFSK E5 / Fengon 600**

**Before we get started with the actual installation of the Google Play Store in the**
**car…**


…I recommend first installing a small but very useful app called **Panels** . With Panels, you can add
additional software navigation buttons such as “Back”, “Home”, or “Recent Apps” – these are missing in
the vehicle’s standard user interface.


Additionally, Panels allows you to create a shortcut to the Google Play Store. This is especially helpful
because after installation, the Google Play icon will initially not be visible.


The Panels app remains constantly invisible, but can be quickly accessed with a swipe gesture from the
(default – can be changed) right edge of the screen.


All apps you install later via the Play Store will automatically appear in the vehicle’s app menu.


**Panels can be installed most quickly as follows:**


1. Prepare a USB stick formatted in FAT32.


2. Copy the Panels.apk file to the stick.


3. Plug the USB stick into the front USB port of the vehicle.


4. Open the Phone app in the vehicle (make sure your smartphone is connected via Bluetooth).


5. Enter the following code there: * **#12040416#***


6. A list of all installed apps on the vehicle should now appear.


7. Find and open “ES File Manager.”


8. Select the USB stick (usually labeled as “udisk”) and install the Panels.apk file by tapping on it.


9. After installation, return to the Phone app and enter the same code again: * **#12040416#***


10. In the app list, launch Panels to configure it as you wish.


There you can add software buttons like Home, Back (and later a shortcut to the Google Play Store).


**Note:**


Panels always runs invisibly in the background and can be accessed at any time by swiping from the
(default) right edge of the screen.


To install Google Play on the DFSK E5/600, a diaerent approach is required than simply installing it from
a USB stick. For Google Play to function properly, it (along with the necessary Google apps included in
the ZIP archive) must be installed as a system application with the correct permissions.

**Why is this necessary?**
So that Google Play can reliably perform background updates of installed apps and handle new app
installations.
Since we need to move it into the **system/priv-app/** folder of the vehicle’s system, this must be done via
**ADB** .

**We need:**

- Installed **ADB Tools (Android Debug Bridge (ADB)** is used to establish a connection between a
computer and an Android device in a development environment)
**[https://dl.google.com/android/repository/platform-tools-latest-windows.zip](https://dl.google.com/android/repository/platform-tools-latest-windows.zip)**

- A set of APK files for Google Play – specific to the Android version in our car
(already located in the same folder as this guide)

- WLAN connection between the car and the computer

We will now go step by step

**Step 1: Installing the ADB protocol on the computer**
_(If you already have ADB Tools installed on your computer and have used them before, you can skip to_
_**Step 2**_ _)_

Download the ADB ZIP file from the link above and extract its contents – preferably directly to the **C:**
drive.
The ZIP file contains a folder named platform-tools, so after extracting, the folder path should look like
this: **C:\platform-tools**

Next, add the path of this folder to the system’s PATH variables.
This allows you to access ADB from any folder using the Command Prompt (CMD), which will be useful
later.

**The fastest way to do this:**
Press the key combination **Windows + R**, type **sysdm.cpl** in the input field, and click OK.
A window will appear. Click on the third tab **(Advanced)**, then at the bottom click on **Environment**
**Variables** .


A new window will open. Select the option **“Path”** and click the **“Edit”** button.


In the next window, click on **“Browse”** and select the folder where you extracted ADB. This should be
located at the following path: **C:\platform-tools** .


Now confirm each open window by clicking “OK”.
After that, I recommend restarting the computer so the PATH change takes effect.


**Step 2: Connecting the computer to the car via ADB and installing the Google Play**
**Store files**

Download the ZIP file that contains the four required APK installation files for the Google Play Store and
extract it into a new folder named “DFSK E5 Play Store” on your desktop.

The path should then look something like this: **C:\Users\Your-Username\Desktop\DFSK E5 Play Store**


Press the key combination **SHIFT + right-mouse-click** inside the folder and select the option **“Open**
**command window here”.**

The command prompt (CMD) should then open:


Now it’s time to connect the car to your home Wi-Fi network and find the car’s IP address in your router’s
settings.

This IP address usually follows the format **192.168.xxx.xxx** (or something else, depending on the router).
Remember this address, as we’ll need it shortly to connect the computer to the car via ADB.

First, let’s test whether ADB was successfully added to the PATH variables.

Enter the following command in the command prompt (CMD):
adb version

If the output looks something like this:

Then ADB is **successfully installed!**
If an error message appears or there is no output, Step 1 was not completed correctly.
In that case, check whether the path to platform-tools was properly added to the environment variables
(PATH).

Now we will connect to the car using its IP address.
Enter the following command in the command prompt (CMD):
adb connect IP-ADDRESS:5555

**Example:**
If your IP address is **192.168.30.20**, then the command would be:
adb connect 192.168.30.20:5555

If the connection is successful, the output should look something like this:
connected to 192.168.30.20:5555

If an error message appears, check the following:

- Is the car connected to the Wi-Fi network?

- Is the correct IP address being used?

If the connection is successful, we proceed with the next command.
Enter the following in the command prompt (CMD):
adb root

This command starts ADB with **root privileges**, which is required to modify system files.
If the output says “restarting adbd as root,” the command was executed successfully.

Now enter the next command:
adb remount

This command allows system files to be overwritten by mounting the /system directory in read-write
mode.
If the output says “remount succeeded,” the operation was successful.

Now we will finally transfer the four required Google Play Store APK files into the **system/priv-app/**
folder of the car.

Enter each of the following commands one by one into the command prompt (CMD) and wait for each to
complete successfully before proceeding to the next!


Now enter the following command to transfer the frst fle:
adb push GoogleLoginService.apk /system/priv-app/

Once this step is complete, proceed with the next fle:
adb push GoogleServicesFramework.apk /system/priv-app/

3rd File:
adb push GmsCore.apk /system/priv-app/

And fnally, the last fle:
adb push Phonesky.apk /system/priv-app/

Now that all files have been successfully transferred, we need to **correct the permissions** .
Enter the following command individually for each file.

**Note:** After executing each of these commands, there may be no feedback in the command prompt
(CMD).
If no error message appears and the command runs without output, the action was successful!

1. File:
adb shell chmod 644 /system/priv-app/GoogleLoginService.apk
2. File:
adb shell chmod 644 /system/priv-app/GoogleServicesFramework.apk
3. File:
adb shell chmod 644 /system/priv-app/GmsCore.apk
4. File:
adb shell chmod 644 /system/priv-app/Phonesky.apk

**What does this command do?**
It sets the permissions to 644 (rw-r–r–), which means:
The owner can read and write.
Other users (system processes) can only read.

To finish, we set the file to the correct owner (root) by entering the following command:

1. File:
adb shell chown root:root /system/priv-app/GoogleLoginService.apk
2. File:
adb shell chown root:root /system/priv-app/GoogleServicesFramework.apk
3. File:
adb shell chown root:root /system/priv-app/GmsCore.apk
4. File:
adb shell chown root:root /system/priv-app/Phonesky.apk

**What does this command do?**
It sets the owner to “root”
It sets the group permissions to “root”

**Why is this important?**
So that the system recognizes the file as an oaicial system app and can execute it properly.


After completing all the previous steps, enter the following command to restart the car’s system and
save the changes made:
adb reboot

This will restart the vehicle’s system so that the installed Google Play services can load correctly.

After the car has restarted, reconnect it to your Wi-Fi network and (again) run the following command to
re-establish the ADB connection:
adb connect IP-ADDRESS:5555

If the message “already connected” appears, the connection is still active and everything is fine.
If not, the connection will be re-established, and you should receive a confirmation (as shown above).


**Step 3: Registering the GSF ID to certify the device for the Play Store**

Since our car comes from China (where the Google Play Store is typically not used), we need to register
the device with Google so that we can sign in to the Play Store using our Google account.

**Why is this necessary?**
Because the car does not have oaicial Google certification, we need to manually register it by retrieving
a unique GSF ID (Google Services Framework ID) from the system and submitting it to Google.

The car has a pre-installed application that allows us to retrieve this unique GSF ID.

To launch this application, enter the following command in the command prompt (CMD):
adb shell am start -n com.bw.engineermode/.gms.GmsRegisterActivity

This command starts the hidden app on the car, which looks like the following image:


Since I have already completed the registration process and already have my GSF ID, in your case it will
not be shown to you immediately!

**What do you need to do?**
1. Tap the “Open Play Store” button in the top right corner of the app.
2. The Google Play Store should open on the car.
3. A message will appear stating that the device is **not certified** .

**Don’t worry!** This message is completely normal because the device has not been registered yet. Now
let’s continue with the registration.

Now reopen the command prompt (CMD) on your computer and enter the following command:
adb shell am start -n com.bw.engineermode/.gms.GmsRegisterActivity

This command restarts the app that displays the GSF ID.

Since you previously opened the Play Store, the unique GSF ID of the vehicle should now be visible.
Write down this number, as we will need it shortly to register the device with Google.


Now open a web browser on your computer and go to the following page:
**[https://www.google.com/android/uncertifed/?pli=1](https://www.google.com/android/uncertified/?pli=1)**


Sign in with your Google account (if you aren’t already logged in), then enter the previously obtained GSF
ID into the designated field.
Click on **“Register”** afterward.

With this step, the registration process is complete – now it’s just a matter of waiting:
Give it some time for the registration to be processed on Google’s servers (this may take a few hours,
sometimes even 1 to 3 days).

You can now add the Google Play Store icon to the Panels app, which we mentioned at the very
beginning in the introduction.

After the waiting period has passed (try again after a few hours), launch the Google Play Store and try to
sign in with your Google account.

If the message still says the device is not certified, it means the registration hasn’t gone through yet – in
that case, just be patient a little longer.
Repeat the sign-in attempt periodically until you eventually land on the main screen of the Play Store.

From that point on, you can install any apps you like, such as:
a navigation app of your choice, YouTube Music, YouTube, Spotify, internet radio streaming apps, and
more.

**But (there’s always a “but”) – caution is advised:**
Since the infotainment system in our car is based on rather average hardware
(SoC / CPU: AutoChips AC8257, Quad-core ARM Cortex-A53 @ 2.0 GHz, RAM: 4 GB LPDDR3, Android 9),
it’s roughly equivalent to a budget entry-level Android smartphone.


**This means:**
You should **avoid installing too many (unnecessary) apps**, as this can overload the system in the
background — resulting in slower response, sluggish operation, or interface delays.

**Stick to the essentials:** a navigation app of your choice, YouTube/Spotify, internet radio — and other
lightweight applications.

**Tip!:**
Optionally, you can apply a small optimization in the Developer Options —
**Attention:** This refers to the Android system settings (green icon), not the car’s settings menu!

These Android settings can be accessed either through the Panels app
or using the previously mentioned secret code ***#12040416#***, which opens the full app list.

**Here’s how:**
1. Open the “Settings” app (green icon).
2. Go to “About this phone.”
3. Tap the build number 7–8 times quickly until you see the message that Developer Options have been
enabled.
4. Go back and open the new “Developer Options” menu.
5. There you’ll find the following settings:

**• Window animation scale**

**• Transition animation scale**

**• Animator duration scale**


You can set these values to 0.5x or turn them oa completely.
This will result in faster and more responsive system performance.

Have fun!

Kristijan Barisic


