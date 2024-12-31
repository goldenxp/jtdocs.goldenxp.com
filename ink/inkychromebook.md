---
title: Running Inky on a Chromebook
parent: Ink
description: How to get Inky up and running on Google Chromebooks
---

# Running Inky on a Chromebook
Chromebooks are an interesting device for writing. And with online tools such as Borogove and Encrier, it's possible to write Ink stories. But what if you wanted the [Inky](https://github.com/inkle/inky) experience? It's possible if you use the Inky Linux app.

## Step 1: Turn on Linux Dev Env
Activating Linux on Chromebook is prone to change but the current steps as of writing this are to go to Settings, search for `Linux development environment` and click "Turn on" on the first option.

![Snip of Linux Installer on a Chromebook]({{ site.baseurl }}/assets/img/Ink_ChromeOSInstallLinux.png)

Follow the wizard, wait a bit and a Terminal window should pop up.

## Step 2: Get Inky Linux
There's a few ways of accomplishing this. One method involves using Chrome to simply download the latest release from GitHub, then use Files' "Share with Linux" feature and access the files in Terminal in the `/mnt/chromeos` directory
But if we already have the Terminal up from the previous step, we can use `wget` or `curl` commands instead. Run `wget https://github.com/inkle/inky/releases/latest/download/Inky_linux.zip` and it will begin downloading the latest zip. 

Unzip should already be installed so unzip the archive with `unzip Inky_linux.zip -d .`
It should output a folder called Inky-linux-64. 

## Step 3: Running Inky
Enter the directory with `cd Inky-linux-64` and attempt to run Inky with `./Inky`
If all goes well, Inky should just run. But you might run into some errors depending on a variety of factors. 

#### Library Error
`/Inky: error while loading shared libraries: libnss3.so: cannot open shared object file: No such file or directory`

You might get the above error. If you do, run `sudo apt install libnss3` to install the missing libraries.

#### Permission Error
`-bash: ./Inky: Permission denied`

This will most likely happen if you used the browser to download the Inky zip and used Files to unzip and copy/move it to your Linux Files. Run `chmod a+x ./Inky` and rerun the program. You'll most likely see another error.

![Error dialog in Inky running on ChromeOS]({{ site.baseurl }}/assets/img/Ink_ChromeInkyError.png)

Once again, it's related to permissions. Basically inklecate is attempting to run in the background and failing. Run the following to allow its execution.
`chmod a+x ./resources/app.asar.unpacked/main-process/ink/inklecate_linux`

#### Unicode Error
`Process terminated. Couldn't find a valid ICU package installed on the system.`

![Error dialog in Inky running on ChromeOS]({{ site.baseurl }}/assets/img/Ink_ChromeInkyErrorICU.png)

Upon running Inky, you might see this error on the right side of the screen. You will get the same error when running inklecate in the Terminal too. This issue occurs in older versions of Inky but seems to be resolved. Using the latest version of Inky is recommended. It may be possible to downgrade the ICU (International Components for Unicode) library to ensure compatibility but this is tricky. It's better to get latest.

## Step 4: Avoiding the Terminal
Spinning up terminal just to run Inky seems like too many steps. Fortunately we can make a .desktop file that the ChromeOS Launcher can use.

Use the Chrome OS Text tool to make a new file called `Inky.desktop` in `~/.local/share/applications`. Note that you'll need to activate "Show hidden files" in the Files dialog and make new folders if they don't exist. In this file, add the following: 

```
[Desktop Entry]
Name=Inky
Comment=Inkle Tool
Exec=/home/replace-this-with-your-username/Inky-linux-x64/Inky
Icon=/home/replace-this-with-your-username/Inky-linux-x64/Icon1024.png
Type=Application
```

![Screenshot of Text editor in Chrome and save dialog with save path]({{ site.baseurl }}/assets/img/Ink_ChromeInkyDesktop.png)


The Icon1024.png can be found here: <https://github.com/inkle/inky/blob/master/resources/Icon1024.png>

You can toss this anywhere you want, just make sure to use that path as the Icon value in the `.desktop` file. Once you do this, the launcher will pick it up almost immediately and reveal a brand new icon.

![Small snippet of launcher icon for Inky]({{ site.baseurl }}/assets/img/Ink_ChromeInkyLauncher.png)

It will be in the Linux Apps folder. You can now launch Inky with it and even pin the application to the taskbar .. err .. shelf.

## Extra Credit
As mentioned earlier, you can use `mnt/chromeos` to access shared folders from the Chrome OS. Make an `ink` folder in My Files and share it with Linux. Now, any ink files in there can be access from `mnt/chromeos/MyFiles`.

![Screenshot showing the right-click menu ]({{ site.baseurl }}/assets/img/Ink_ChromeInkyDefault.png)

Now if you edit `Inky.desktop`, you can encourage ChromeOS to set Inky as the default app to open ink files. Just add `MimeType=text/plain` at the bottom and when you access a `.ink` file with the right-click menu you can choose which editor opens it. If you use "Change default" and set Inky as the default app, you can double-click the file directly to open it.

## Conclusion
Chromebooks can be very versatile and their ability to run Linux apps makes writing with Inky possible!