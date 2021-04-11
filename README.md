# Not yet cleaned Automic
Automic is an Android Internal made for the Game [Growtopia](https://www.growtopiagame.com/). Autofarming in this Game can be very lucrative which why this one in particuliar got some hype, next to Exafarm and a few private Bots. I wanted to pay playingo for a clean build (PC Version) before his first Android leak. But in 2021 it's so hard to contact someone over the Internet. Also, I don't think he ever wanted to make money with it.

The original project was made by [iProgramInCpp](https://github.com/iProgramMC) & [Playingo](https://github.com/playingoDEERUX). Which of course turned out to be an Account Stealer for relaunching Growalts. Keep that in mind and don't praise them for that.

This project makes use of letting the game load their own library. I have never seen Dear Imgui getting used for Android Mod Menus before, which why I was interested in stuff like Exafarm and Automic the whole time. Guess, because of them, we will see menus like this pop up for not only Growtopia pretty soon, but also other mobile phone games. Credits to that.

## Prerequisites
Since both original Devs use Windows. Other Operating Systems might not be supported.
- [Java Runtime](https://java.com/en/download/)
- [Android NDK](https://dl.google.com/android/repository/android-ndk-r16b-windows-x86_64.zip)
- [Android SDK Platform-Tools](https://developer.android.com/studio/releases/platform-tools)
- [7-Zip](https://www.7-zip.org/)

- Optional: [Visual Studio IDE](https://visualstudio.microsoft.com/downloads/). You can open the whole ``jni`` Folder with it for easier Code/Class navigation. Also it can let you jump to declarations of functions by right-clicking one. And uses of one. Good for reversing their "Botnet" + Stealer.
- Optional: [Bluestacks Android Emulator](https://www.bluestacks.com/). This Emulator supports adb and thus you can run the ``buildall.bat`` that starts ``install.bat`` without any problems. Turned out to be pretty good at running it.

After having all of that installed. Restart your PC for the better. Now you will need to edit the batch files from the Automic Project Folder, since your Paths might not be the same.

### **buildall.bat:**

Change ``C:\Program Files\7-Zip`` to where you have installed **7-Zip**. This is the default path. If you haven't changed the directory in the Setup, you can leave it like that. Change ``D:\android-ndk-r16b`` to where you have extracted **Android NDK** to.
```batch
@ECHO OFF
SET PATH=%PATH%;C:\Program Files\7-Zip;D:\android-ndk-r16b;
CALL COMPILE.BAT
CALL PACKAGEAPK.BAT
CALL INSTALL.BAT
```

### **compile.bat:**

Change ``D:\android-ndk-r16b`` to where you have extracted **Android NDK** to. Everything else points to folders in the same directory the script got run. Leave them as they are.
```batch
D:\android-ndk-r16b\ndk-build
NDK_PROJECT_PATH=\
NDK_APPLICATION_MK=\jni\Application.mk
cmd /k
```

### **install.bat**


Same here. Edit all paths. Even though I don't know why it has to be included here. Optional: Remove the ``REM`` in front of ``adb uninstall com.rtsoft.growtopia``. This will uncomment that line. And will cause Growtopia to get uninstalled every build. Which is quite useful when you have builds that just won't work or you find small things that you have to edit. It won't be able to install/update the android package when you have one with the same name already installed. So choose for yourself if you want it to happen automatically, or you want to uninstall it yourself every single small update.
```batch
@ECHO OFF
SET PATH=%PATH%;C:\Program Files\7-Zip;D:\android-ndk-r16b;D:\platform-tools
ECHO Installing on default phone...
REM adb uninstall com.rtsoft.growtopia
adb install growtopia.apk

ECHO Done, spawning logcat...
adb logcat -s "INfERNAL"
```

### **PackageApk.bat**

This is something I couldn't manage to work. It won't place the library into Growtopia's APK Lib Folder on it's own. You also will probably have to replace ``libblue.so`` with ``libinfernal.so``. I don't know, they had a private version or this source is super old.

Also create a new Folder in the Source Folder (where all those batches are) called ``gt``. In this Folder you put the original ``growtopia.apk``, since it gets deleted, don't download it to this folder, but always copy-paste it into that folder. Or edit the batch script so it does a copy of it on it's own. You will probably need the one for 3.59, not sure what version the sigs are there for.

```batch
@ECHO OFF

SET PATH=%PATH%;C:\Program Files\7-Zip;D:\android-ndk-r16b;D:\platform-tools
ECHO Copying library...
REM Copy the so file from libs/armeabi-v7a to gt's lib dir
COPY libs\armeabi-v7a\libinfernal.so gt\lib\armeabi-v7a\libinfernal.so

ECHO Packaging the library...
REM delete the original apk
DEL growtopia.apk

REM package the apk itself
CD gt
7Z a -tzip growtopia.apk *
CD ..
MOVE gt\growtopia.apk growtopia.apk

ECHO Signing the apk...
REM sign the apk
java -jar apksigner.jar sign  --key apkeasytool.pk8 --cert apkeasytool.pem  --v4-signing-enabled false growtopia.apk
```

## Build
Run ``buildall.bat`` once you are done with your changes to the source. Since it will close the command prompt that opens with it automatically once it's done. You should open ``cmd`` as an Admin and run that batch file through it.

I am working on cleaning the source and making it skid-ready. Also I will include my current solution for the ``PackageApk.bat`` problem once it's buildable on it's own and stealer-free. The Function ``isLibraryLoaded`` wasn't present in the source. So you had to decide if you want to comment out the whole sleeping loop. Or if you recreate the function. Since I thought it's not there without any reason, I decided myself to recreate that function. The library would build fine.

But I was told iProgramMcVirgin at some point announced next to the leak that you had to remove a huge part of ``eNetClient2.cpp``. Currently reversing what is supposed to be wrong there. As by removing all that 'SendPacket' Stuff, you break the Multibotting feature as well as the Autofarmer itself. Maybe it's a try by him to stop skids from using it and rebranding it under some "Project [...]" or "[...]ware".

Quote [@iProgramMC](https://github.com/iProgramMC):
> @everyone Please enjoy. To remove stealer, you will need to remove everything inside enetclient2.cpp, as well as remove the lines that error out once you do that. Enjoy!!! Also, don't release this as your own project without asking permission
