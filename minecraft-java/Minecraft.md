# Minecraft Java Edition on the Xavier NX

In this small tutorial, you will learn how to get the latest version of Minecraft Java Edition installed on your Jetson Xavier NX. To achieve this, we will make use of the open source Minecraft Launcher called MultiMC and we will use LWJGL libraries ported to the ARM64 platform by [JJTech0130](https://github.com/JJTech0130). This tutorial is based on the [instructions provided by Nicholas Bering](https://nicholasbering.ca/raspberry-pi/2020/10/18/minecraft-64bit-rpi4/) to get Minecraft to run on a Raspberry Pi 4.

### Setting up the building environment:
First, install the necessary dependencies on your Xavier NX:
* For Ubuntu 18.04:
  - <code> sudo apt install build-essential cmake qt5-default qtbase5-dev zlib1g-dev openjdk-8-jdk openjdk-8-jdk-headless git </code>
  - Please note that Ubuntu 18.04 only offers up to Java 11 while Minecraft >= version 1.17 requires Java 16
* For Ubuntu 20.04:
  - <code> sudo apt install build-essential cmake qt5-default qtbase5-dev zlib1g-dev openjdk-16-jdk openjdk-16-jdk-headless git </code>

### Cloning and setting up MultiMC:
  Please navigate to your wanted file location, open a Terminal on there and run the following:
* Compilation:
  - <code> mkdir MultiMC && cd MultiMC </code>
  - <code> git clone --recursive https://github.com/MultiMC/MultiMC5.git src </code>
  - <code> mkdir build && mkdir install && cd build </code>
  - <code> cmake -DCMAKE_INSTALL_PREFIX=../install -DMultiMC_META_URL:STRING="https://raw.githubusercontent.com/Grima04/meta-multimc/master/" ../src </code>
  - <code> make -j6 </code>
  - 
* Installation:
  - <code> make install </code>
  - 
* Troubleshooting:
  If you are getting an error with this syntax (with x and y = some version number)
  - <code> error: Source option x is no longer supported. Use y or later. </code>
  - <code> error: Target option x is no longer supported. Use y or later. </code> \
  you need to change the target and source version numbers since your cmake version probably no longer supports the older ones. To fix this (at least in my case),    you need to edit the following files of MultiMC:
  - <code> MultiMC/build/libraries/launcher/CMakeFiles/NewLaunch.dir/build.make </code>
  - and <code> MultiMC/build/libraries/javacheck/CMakeFiles/JavaCheck.dir/build.make </code> \
  - Open both files with a text editor like nano, look for the keywords <code> -target </code> and <code> -source </code> and change the following version number to the one required. For example: if your source and target version 6 is no longer supported and version 7 is the minimum version required, change <code> -target 1.6 </code> to <code> -target 1.7 </code>. The same applies for the source: <code> -source 1.6 </code> becomes <code> -source 1.7 </code>.
  - Finally, re-run <code> make -j6 </code> and <code> make install </code> and it should compile just fine.

* Running:
  Now, that everything is installed, you need to cd to MultiMC/install and run <code> ./MultiMC </code> \
  MultiMC will now open and give you instructions on how to proceed.
  When you get to the screen where you need to select the Java path, please choose the highest available Java version on your system and set the maximum allocated memory for the JVM to 4096 MB and the minimum allocated memory to 2048 MB like shown here:
  ![Screenshot from 2021-07-27 00-33-34](https://user-images.githubusercontent.com/22496380/127175508-6f58b578-e796-4dc1-ba0c-6db2662d88d4.png) \
Now you are ready to log in with your account and you can start downloading and installing a Minecraft version by creating a new instance. \
If everything went as planned, you should now have Minecraft Java Edition running on your Xavier NX:
![Screenshot from 2021-07-26 22-55-20](https://user-images.githubusercontent.com/22496380/127176373-cc266110-4491-4687-b35e-a6a1f8fbfc1f.png)

* Performance tweaks:
  When running Minecraft Java, you might notice that the game suffers from a lot of FPS drops and heavy stutters, especially when flying around with an elytra or in creative mode. \
  To fix this, I would recommend you to install the Sodium Renderer mod (requires Fabric and the Fabric API) to get a smooth gameplay experience. Furthermore, I would recommend you to use the increased CPU speed tweak to further reduce stutters and sudden FPS drops. \
  After doing that, I get a solid and locked 60 FPS @ 1080p with fancy graphics, smooth rendering set to minimum, 12 chunks render distance and VSYNC enabled.
  Without VSYNC, I am getting around 80 to 130 FPS:
  - With VSYNC enabled:
  ![Screenshot from 2021-07-27 15-37-21](https://user-images.githubusercontent.com/22496380/127177904-2f7b4faf-5fba-44de-96fd-7bbb68dc50ff.png)
  - With VSYNC disabled:
  ![Screenshot from 2021-07-27 15-43-21](https://user-images.githubusercontent.com/22496380/127178035-f9e8f2b7-8a50-4c92-9421-7867530abbce.png)
  
  If you want to test shaders, you can use OptiFabric with OptiFine. I don't recommend to do this though, since you will not get above 30 FPS @ 1080p, even with Sildurs Shaders and your Xavier NX will get quite hot when doing this. Maybe in the future, it will be possible to some degree, using the more performant Iris Shader Mod (when Iris gets support for shader options, to lower the settings).

* Final words:
I hope that I could help you with this tutorial on how to get Minecraft Java to run on your Xavier NX. \ 
This definitely shows that the Xavier can be used for more than just AI computing and that it is a great platform for some casual gaming as well (as long as the game is ARM64 compatible or able to be emulated of course).
