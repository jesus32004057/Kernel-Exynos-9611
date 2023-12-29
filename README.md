<h4 align="center">A custom kernel for the Exynos9611 devices.</h4>

<p align="center">
  <a href="#key-features">Key Features</a> •
  <a href="#how-to-build">How To Build</a> •
  <a href="#how-to-flash">How To Flash</a> •
  <a href="#credits">Credits</a>
</p>

## Key Features AxynGrass Kernel

* Thanks to @roynatech for his amazing work on the exynos 9611 and allowing me to use his base 

##                                                                     GPU

*Gpu Add multiplier for timeout limits Adds TICK_MULTIPLIER which is multiplied into various timeout values.This can be set such that slower systems (such as emulation) can be
allowed to run longer
 
*Gpu Add adaptive power policy
Adds a new power policy 'adaptive' based off 'coarse_demand'.
The main difference is that while coarse_demand has a static
power off hysteresis (i.e. the amount of time after the GPU
goes idle that the platform idle callback is made), adaptive
scales this up and down depending on the workload.

*Add GPU sustainable frequency
Avoid excessive thermal throttling on stock DVFS during gaming. This frequency, has been tested, giving the best thermal stability

*DFVS Gpu_dvfs_clock_lock
This remove GPU max freq lock.
No more drop max GPU freq to 377MHz.

##                                                                     OTHERS 

*CRC disable and CRC configurable

*Optimized Console FrameBuffer

*Optimized SLUB memory allocator

*Improve sqrt speed

*Lower the non-hugetlbpage pageblock size to reduce scheduling delays

*Added [wireguard](https://www.wireguard.com/) driver, an open-source VPN driver in-kernel
 
*Added [KernelSU](https://kernelsu.org/)

 ##                                                                     CPU 

*Optimize CPU select_idle_core()
Currently we loop through all threads of a core to evaluate if the core is idle or not. This is unnecessary. If a thread of a core is not idle, skip evaluating other threads of a core.

*Cpu earch a task from the tail of the queue As a first step this patch makes cfs_tasks list as MRU one.
It means, that when a next task is picked to run on physical
CPU

*Disable SLUB per-CPU partial caches This causes load spikes when the per-CPU partial caches are filled and need to be drained, which is bad for maintaining low latency.

*Remove CPUFreq times driver:
Ignoring its colorful memory-leak-filled past, the CPUFreq times driver is absolutely horrible for several reasons. Firstly, it allocs and reallocs memory directly from the scheduler tick with IRQs disabled, which is not only bad for latency, but also forbidden on RT. Secondly, the statistics just aren't that useful because they're calculated using nonsense: a process is assumed to have run at the same CPU freq for the entire jiffy (and that freq is just the current freq at the time of the tick), the power consumed for the entire jiffy is attributed to whatever unlucky process happens to be running at the time of the scheduler tick, the UID owning the process is blamed for other CPUs in the cluster not being idle (with no basis for it), and there's assumed to be a strong correlation between a CPU's freq and the power consumed by the process (when there isn't, at the very least because all CPUs in a cluster share the same clock source, which means a high load on any CPU in a cluster will cause all CPUs in the cluster to run at a higher freq).

*Optimize cpu energy calculation algorithm For more effective energy aware placement, take schedutil tipping point
into account for needed capacity and power

*Always try to find energy efficient CPU.

*Add CPU Boost driver Port Razer phone, Since this driver enforces the boost by hooking into standard cpufreq ADJUST notifiers, it has several advantages:
- More portable across kernel versions where the cpufreq internals might
  have been rewritten.
- Governor agnostic and hence works with multiple governors like
  conservative, ondemand, interactive, etc.
- Does not affect the sampling period/logic of existing governors.
- Can have the boost period adjusted independent of governor sampling period.
  
*Don't hog the CPU and zone lock in rmqueue_bulk()  With this change, rmqueue_bulk() no longer appears as a serious offender in the preemptoff tracer, and system latency is noticeably improved.

 ##   CUSTOM DTB
Custom Device tree blob

*Tweak rate limits on the big cores

*Shorten down rate limit on the little cores, This should allow the little cores to scale a bit better between tasks (especially since a lot of them happen on the little cores for efficiency) by having its scale down limits shorter than the big cores.

*DTS Exynos: Re-adjust variable frequency boosting (freqvar-boost) (Backport) Now that upstream patches and improvements to the scheduler have been applied, it's also important to address the variable frequency scaling the kernel does. Also known as 'freqvar-tune', it adjusts the CPU frequency by inflating the reported utilization to both schedutil (upscale_ratio_table) and schedtune (boost_table), making the CPU boost to higher frequencies. This can be treated as some sort of "wiggle room" just in case a task suddenly adds a significant amount of load.
Based on device testing with the current boosting values, there is aggressive high-frequency scaling on the little cores as well as measly frequency scaling on the big cores. Not only does this cause performance issues, it's also troublesome for battery life.

*Freqvar-tune Adapt to new changes to EMS Adapt the platform's 'freqvar-tuner' tables based on the new changes to the Exynos Mobile Scheduler on this tree

*Tweak 'ontime migration' tunables

*Re-adjust FRT ratios

*Only set one CPU in the default IRQ affinity mask
 
*Don't allow IRQ affinity masks to have more than one CPU

*Tune MIF utilization

*Add UC 728Mhz big frecuencia

*BACKPORT Sched ems: freqvar-tune: Import from the Exynos 1280 Import a newer version of freqvar-tune used by Samsung on the Exynos 1280. There's also freqvar-tune for the CPUs' idle states but (a) it's actually incomplete and is unused and (b) used on platforms with seemingly more advanced idle (which the 9611 doesn't have),Pulled from the Galaxy A53 Android 13 sources and adapted to current kernel source state.

*Disable modem debugging

*Reserved memory reduced Stock 555Mb to 533Mb

## How To Build

You will need ubuntu, git, around 8GB RAM and bla-bla-bla...

```bash
# Install dependencies
$ sudo apt install -y bash git make libssl-dev curl bc pkg-config m4 libtool automake autoconf

# Clone this repository
$ git clone https://github.com/Gojikovi/kernel_samsung_universal9611

# Go into the repository
$ cd kernel_samsung_universal9611

# Install toolchain
$ ./download_toolchain.sh 

# If you want to compile the kernel not for A51 then change the 23 line in build_kernel.sh to f41, m21, m31, m31s
# Build the kernel
$ ./build_kernel.sh aosp ksu # (for AOSP)
$ ./build_kernel.sh oneui ksu # (for OneUI)
```

After build the image of the kernel will be in out/arch/arm64/boot/Image

## How To Flash

After a successful build, you can see the Kernel.zip archive.
This is your kernel. Just flash it via TWRP or adb sideload

## Credits

- [jesus3200405](https://github.com/jesus32004057)
- [roynatech2544](https://github.com/roynatech2544)
- [Samsung Open Source](https://opensource.samsung.com/)
- [Android Open Source Project](https://source.android.com/)
- [The Linux Kernel](https://www.kernel.org/)


