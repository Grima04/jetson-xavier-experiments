# Unlocking the full potential of the CPU of the Xavier NX

In this small tutorial, you will learn how to unlock the full potential of the CPU of your Jetson Xavier NX without needing to modify the kernel by making it possible for the CPU to reach the maximum stock clocks when all 6 cores are enabled.

### Some background information:
By default, the Jetson Xavier NX comes with 6 power modes with TDP values ranging between 10 W and 15 W and between 2 to 6 enabled CPU cores. However, when I took a closer look at the 15 W 6 core mode, I noticed that the CPU never reached its maximum frequency, even when the load was purely CPU based without stressing the GPU. Sure enough, when looking at the frequency tables located at <code> /sys/devices/system/cpu/cpu0/cpufreq/scaling_available_frequencies </code> , the maximum frequency should be 1907200 or around 1.9 GHz, however the tegrastats only returned a maximum frequency of 1420800 or around 1.42 GHz when the CPU was under full stress. After digging a bit deeper, I found the config file for the power profiles located at <code> /etc/nvpmodel/nvpmodel_t194_p3668.conf </code> . When going through the data, I found the 6 core 15 W profile:
![Screenshot from 2021-07-28 12-38-29](https://user-images.githubusercontent.com/22496380/127308944-0092c9da-84f9-4820-9eb8-c53628d34a63.png)

The lines starting with *CPU_DENVER* are the key to solve the problem. As we can see, the maximum CPU frequency is set to 1420800 or 1.42 GHz instead of 1.9 GHz which matches my observations when running a CPU intensive application and tegrastats.

### Creating a custom power profile:
Now, let us get to the interesting part: how to unlock the full CPU potential by creating a custom profile. \
To get started, open the config file with <code> sudo nano /etc/nvpmodel/nvpmodel_t194_p3668.conf </code> and search for the *MODE_15W_6CORE* profile. Afterwards, copy the whole profile info, go to the bottom of the file and append the profile at the end of the 10 W Desktop profile (but above the mandatory section line). \
Now, change the line *POWER_MODEL ID=2 NAME=MODE_15W_6CORE* to *POWER_MODEL ID=6 NAME=MODE_CPU_MAX*. Finally, change the lines *CPU_DENVER_0 MAX_FREQ 1420800* , *CPU_DENVER_1 MAX_FREQ 1420800* and *CPU_DENVER_2 MAX_FREQ 1420800* to *CPU_DENVER_0 MAX_FREQ 1907200* , *CPU_DENVER_1 MAX_FREQ 1907200* and *CPU_DENVER_2 MAX_FREQ 1907200*. Furthermore, to make sure that the GPU won't use too much power, change its frequency to 510 MHz instead of 1.1 GHz by editing the *GPU MAX_FREQ* line \
Please make sure that your custom profile looks like this:
![Screenshot from 2021-07-28 12-53-30](https://user-images.githubusercontent.com/22496380/127310908-c504bd63-5880-4918-8527-377d675fba32.png)

Now you can save the changes and reboot the system. If everything went as planned, you should now have an additional power mode called MODE CPU MAX. \
For CPU bound applications or games, this custom profile can improve the performance by a significant amount. In my experience, Minecraft Java ran much smoother after this change and CPU benchmarks got a significantly higher score. \
For the benchmarking, I used the box64 x86_64 emulator, Wine 64 Bit and Cinebench R15.
* Using the default power profile:
![Screenshot from 2021-07-23 15-47-50](https://user-images.githubusercontent.com/22496380/127311716-99bc133f-a9c3-4362-8fd7-ad721b8ab42a.png)

* Using the custom profile:
![Screenshot from 2021-07-26 16-48-12](https://user-images.githubusercontent.com/22496380/127311683-9b0af577-e379-481b-86ca-ea421d4ddfa6.png)
