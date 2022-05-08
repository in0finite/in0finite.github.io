---
layout: post
title: Running Unity Editor on Linux dedicated servers
---

Unity supports running on dedicated Linux servers, but there are some gotchas on how to install Unity, activate license, install modules, and make it run properly.

Running Unity on cloud servers can be useful for many cases:
	- using it for your CI/CD pipeline, to build application and run tests
	- performing some long-running CPU-heavy tasks, such as generating navigation mesh for all of your levels

In this specific case, I used Unity 2020.3.26 (free license) with Linux server hosted on Azure. But in general, it should work with any Unity version and any hosting provider.

## UnityHub installation

First, we need to install UnityHub (steps taken from [official documentation](https://docs.unity3d.com/hub/manual/InstallHub.html)):

```bash
sudo sh -c 'echo "deb https://hub.unity3d.com/linux/repos/deb stable main" > /etc/apt/sources.list.d/unityhub.list'

wget -qO - https://hub.unity3d.com/linux/keys/public | sudo apt-key add -

sudo apt update

sudo apt install unityhub
```

Before running UnityHub, we need to install some dependencies:

```bash
sudo apt install libgbm-dev libasound2 libgconf-2-4
```

UnityHub requires X server in order to run, so we will use virtual X server environment to run it. This can be done using a program called `xvfb`.

Install `xvfb`:
```bash
sudo apt install xvfb
```

To verify that UnityHub is working properly, run:
```bash
xvfb-run unityhub --headless help
```

If it displays usage information, then you are good to go.

## Install Unity Editor using UnityHub

Find the changeset for the Unity version you want to install. Changeset is located on the release notes page, eg `https://unity3d.com/unity/whats-new/2020.3.26`.

Install Editor:
```bash
xvfb-run unityhub --headless install --version 2020.3.26f1 --changeset 7298b473bc1a
```

Unity Editor should now be installed at `~/Unity/Hub/Editor/2020.3.26f1/`.

## Activate license

In order to use Editor, you need to activate license. If you are using free license, you can't simply activate it by providing username and password on the command line. Instead, you have to use different approach described [here](https://docs.unity3d.com/Manual/ManualActivationGuide.html).

First step is to generate a license activation file (.alf):
```bash
xvfb-run Unity/Hub/Editor/2020.3.26f1/Editor/Unity -quit -batchmode -nographics -createManualActivationFile
```

Next, you need to request a Unity license file (.ulf) using your generated .alf file, as described in [documentation](https://docs.unity3d.com/Manual/ManualActivationGuide.html).

Once you obtain .ulf file, you are ready to activate license:
```bash
xvfb-run Unity/Hub/Editor/2020.3.26f1/Editor/Unity -quit -batchmode -nographics -manualLicenseFile yourLicenseFile.ulf
```

Now you should be able to run Unity Editor, eg to build game for Linux:
```bash
xvfb-run Unity/Hub/Editor/2020.3.26f1/Editor/Unity -quit -batchmode -nographics -logFile - -projectPath path/to/your/project -buildTarget Linux64 -buildLinux64Player "Build.x86_64"
```

## Install modules

By default, Editor will only have Linux build module installed. If you want to build the game for other platforms, you will need to install their build modules.

For example, to add build module for Windows (Mono):
```bash
xvfb-run unityhub --headless install-modules --version 2020.3.26f1 --module windows-mono
```

To see all available build modules, check out official [documentation](https://docs.unity3d.com/hub/manual/HubCLI.html#install-modules).

## Adding Android support

To install Android build module:
```bash
xvfb-run unityhub --headless install-modules --version 2020.3.26f1 --module android android-sdk-ndk-tools android-open-jdk
```

If, while building for Android, you get this error:
```
UnityException: JDK not found
Java Development Kit (JDK) directory is not set or invalid. Please, fix it in Preferences -> External Tools
```
, you need to allow execution of all binaries in Android folder:
```bash
chmod --recursive +x ~/Unity/Hub/Editor/2020.3.26f1/Editor/Data/PlaybackEngines/AndroidPlayer/
```

If your project is targeting Android API level which is not installed by default (eg 28), you can install it this way:
```bash
export JAVA_HOME="$HOME/Unity/Hub/Editor/2020
.3.26f1/Editor/Data/PlaybackEngines/AndroidPlayer/OpenJDK"
~/Unity/Hub/Editor/2020.3.26f1/Editor/Data/PlaybackEngines/AndroidPlayer/SDK/tools/bin/sdkmanager "platforms;android-28"
```

