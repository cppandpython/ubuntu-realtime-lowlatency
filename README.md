# 🌟 ubuntu-realtime-lowlatency

# доделываю


<br><br>

## I USE THIS CONFIGURATION ON MY LAPTOP (LOQ-15IAX9)

<br><br>


## LINUX KERNEL

```bash
# 1


sudo mkdir -p /etc/apt/keyrings
wget -qO - https://dl.xanmod.org/archive.key | sudo gpg --dearmor -vo /etc/apt/keyrings/xanmod-archive-keyring.gpg




# 2


echo "deb [signed-by=/etc/apt/keyrings/xanmod-archive-keyring.gpg] http://deb.xanmod.org $(lsb_release -sc) main" | sudo tee /etc/apt/sources.list.d/xanmod-release.list




# 3


sudo apt update && sudo apt install linux-xanmod-lts-x64v3
```


<br><br>


## SYSCTL

```bash
# 1
# /etc/sysctl.d/realtime-lowlatency.conf


# --- CPU & Scheduler ---
# Use schedutil for adaptive frequency scaling with low latency
kernel.sched_latency_ns            = 8000000
kernel.sched_min_granularity_ns    = 2000000
kernel.sched_wakeup_granularity_ns = 6000000
kernel.sched_migration_cost_ns     = 5000000
kernel.sched_child_runs_first      = 0
kernel.sched_autogroup_enabled     = 0
kernel.sched_rt_runtime_us         = -1
kernel.sched_pelt_multiplier       = 1
kernel.sched_rt_period_us          = 1000000
kernel.sched_cfs_bandwidth_slice_us= 3000
kernel.sched_util_clamp_min        = 100
kernel.core_pattern                = /dev/null
kernel.printk                      = 3 3 3 3


# --- Security & Stealth ---
# Hardening the kernel against exploits and forensics
kernel.kexec_load_disabled         = 1
kernel.pid_max                     = 4194304
kernel.kptr_restrict               = 2
kernel.yama.ptrace_scope           = 1
kernel.dmesg_restrict              = 1
kernel.unprivileged_bpf_disabled   = 1
kernel.unprivileged_userns_clone   = 1
kernel.randomize_va_space          = 2
kernel.perf_event_paranoid         = 2
fs.suid_dumpable                   = 0
net.core.bpf_jit_harden            = 2


# --- Memory & Virtualization ---
vm.mmap_min_addr                   = 65536
vm.swappiness                      = 10
vm.max_map_count                   = 2097152
vm.vfs_cache_pressure              = 30


# --- Laptop & SSD Optimization ---
# Optimizing disk I/O for SSD life and battery power
vm.laptop_mode                     = 5
vm.dirty_ratio                     = 10
vm.dirty_background_ratio          = 5
vm.dirty_expire_centisecs          = 3000
vm.dirty_writeback_centisecs       = 3000


# --- Filesystem Protection ---
fs.protected_fifos                 = 1
fs.protected_hardlinks             = 1
fs.protected_regular               = 2
fs.protected_symlinks              = 1
fs.inotify.max_user_watches        = 1048576


# --- Networking (BBR + Low Latency) ---
# High-speed networking with minimum bufferbloat
net.core.default_qdisc             = fq_codel
net.ipv4.tcp_congestion_control    = bbr
net.core.netdev_max_backlog        = 10000
net.core.rmem_max                  = 16777216
net.core.wmem_max                  = 16777216
net.core.busy_poll                 = 50
net.core.busy_read                 = 50
net.ipv4.tcp_fastopen              = 3
net.ipv4.tcp_fin_timeout           = 15
net.ipv4.tcp_tw_reuse              = 1
net.ipv4.tcp_keepalive_time        = 600
net.ipv4.conf.all.rp_filter        = 2
net.ipv4.conf.default.rp_filter    = 2
net.ipv6.conf.all.use_tempaddr     = 2
net.ipv6.conf.default.use_tempaddr = 2




# 2


sudo sysctl --system
```


<br><br>


## GRUB

```bash
# 1
# /etc/default/grub


GRUB_DEFAULT=0
GRUB_TIMEOUT=3
GRUB_DISTRIBUTOR="Ubuntu realtime-lowlatency"
GRUB_CMDLINE_LINUX_DEFAULT="lockdown=integrity pti=on vsyscall=none page_poison=0 init_on_alloc=1 init_on_free=1 slab_nomerge quiet splash fbcon=nodefer pcie_port_pm=on pci=pcie_bus_perf pci=noaer pcie_aspm=force iommu=pt random.trust_cpu=off random.trust_bootloader=off split_lock_detect=off processor.max_cstate=4 intel_pstate=active intel_idle.max_cstate=4 intel_iommu=on intel_pmc_core.ltr_ignore=1 cpufreq.default_governor=schedutil preempt=voluntary workqueue.power_efficient=off irqaffinity=0,1 nohz_full=2-11 rcu_nocbs=2-11 rcu_expedited rcu_cpu_stall_timeout=60 rcu_nocb_poll rcu_boost=1 tsx=off mce=ignore_ce page_alloc.shuffle=1 transparent_hugepage=madvise skew_tick=1 tsc=reliable clocksource=tsc tsc=perfect threadirqs mitigations=auto nvidia-drm.modeset=1 nouveau.modeset=0 i915.fastboot=1 i915.enable_dc=1 i915.enable_psr=1 i915.enable_guc=3 i915.semaphores=1 zswap.enabled=1 zswap.compressor=zstd zswap.max_pool_percent=25 zswap.zpool=zbud zswap.same_filled_pages_enabled=1 elevator=mq-deadline scsi_mod.use_blk_mq=1 blk_mq_affinity=2 nvme_core.default_ps_max_latency_us=3000 nvme_core.io_timeout=255 nvme_core.admin_timeout=60 nvme.use_threaded_interrupts=1 snd_pcm_periods=4 snd_pcm_buffer_size=16384 snd_hda_intel.prealloc_buffer=1 snd_hda_intel.dma_period_bytes=4096 snd_hda_intel.dma_buffer_bytes=16384 snd_hda_intel.beep_mode=0 snd_hda_intel.enable_silent_mode=0 snd_hda_intel.max_buffer_size=65536 snd_hda_intel.position_fix=1 snd_hda_intel.align_buffer_size=1 snd_hda_intel.enable_msi=1 snd_pcm_max_preload=1 snd_hwdep=1 snd_hda_intel.enable_auto_mute=0 snd_hda_intel.power_save=1 usbcore.autosuspend=1 xhci_hcd.quirks=270336 raid=noautodetect btusb.enable_autosuspend=1 nosoftlockup nowatchdog nmi_watchdog=0 audit=0 audit_backlog_limit=0 printk.time=0 loglevel=0 systemd.show_status=0 selinux=0 apparmor=0 security=none mem_sleep_default=deep reboot=efi"
GRUB_CMDLINE_LINUX=""
GRUB_TERMINAL=console 




# 2
sudo update-grub
```


<br><br>


## RTIRQ

```bash
# 1


sudo apt update && sudo apt install rtirq-init




# 2
# /etc/default/rtirq


# --- Priority List ---
RTIRQ_NAME_LIST="usb hid nvme snd"
RTIRQ_HIGH_LIST="usb hid nvme"


# --- Priority Settings ---
RTIRQ_PRIO_HIGH=70
RTIRQ_PRIO_DECR=2
RTIRQ_PRIO_LOW=50
RTIRQ_RESET_ALL=0
RTIRQ_SCHED="fifo"

RTIRQ_SPREAD=1

# --- CPU Affinity ---
RTIRQ_CPUS="2-3"
IRQBALANCE_BANNED_CPUS="00000000-00000000-00000000-0000000c" # Маска для ядер 2-3


# --- Real-Time Clocks ---
RTIRQ_NON_THREADED="rtc0"


# --- Stealth & Performance ---
RTIRQ_DELAY=5
RTIRQ_CHECK_INTERVAL=3
RTIRQ_DEBUG=0
RTIRQ_VERBOSE=0
```


<br><br>


## FSTAB 

```bash
# 1
# /etc/fstab


# <file system>                            <mount point>    <type>  <options>                                                   <dump>  <pass>


# EFI System Partition
/dev/disk/by-uuid/{YOUR UUID}              /boot/efi        vfat    defaults                                                     0       1


# Root Partition (SSD Optimized)
UUID={YOUR UUID}                           /                ext4    defaults,noatime,nodiratime,commit=30,errors=remount-ro      0       1


# Swap File (Low priority to keep it as a last resort)
/swapfile                                  none             swap    sw,pri=1                                                     0       0


# Memory Resident File Systems (Fast & Stealthy)
tmpfs                                      /tmp             tmpfs   defaults,noatime,mode=1777,size=10G,nosuid,nodev             0       0
tmpfs                                      /var/log         tmpfs   defaults,noatime,mode=0755,size=256M,nosuid,nodev            0       0
tmpfs                                      /var/tmp         tmpfs   defaults,noatime,mode=1777,size=1G,nosuid,nodev              0       0
tmpfs                                      /var/backups     tmpfs   defaults,noatime,mode=0755,size=256M,nosuid,nodev            0       0
```


<br><br>


## MODPROBE

```bash
# 1
# /etc/modprobe.d/blacklist-optimized.conf


# --- Graphics & Framebuffers ---
# Disable open-source Nouveau to prevent conflicts with proprietary NVIDIA driver
blacklist nouveau
options nouveau modeset=0
# Disable legacy framebuffer drivers for old/non-existent GPUs
blacklist nvidiafb
blacklist rivafb
blacklist radeonfb
blacklist aty128fb
blacklist atyfb
blacklist cirrusfb
blacklist cyber2000fb
blacklist cyblafb
blacklist gx1fb
blacklist hgafb
blacklist i810fb
blacklist intelfb
blacklist kyrofb
blacklist lxfb
blacklist matroxfb_base
blacklist neofb
blacklist pm2fb
blacklist s1d13xxxfb
blacklist savagefb
blacklist sisfb
blacklist sstfb
blacklist tdfxfb
blacklist tridentfb
blacklist vesafb
blacklist vfb
blacklist viafb
blacklist vt8623fb
blacklist udlfb


# --- Multimedia & Privacy (Camera/Audio) ---
# Disable webcam (uvcvideo) - Remove this line if you need your camera
blacklist uvcvideo
# Disable legacy PC speaker (beep)
blacklist snd_pcsp
options pcspkr off
# Disable ancient/obsolete sound architectures and cards
blacklist sound
blacklist soundcard
blacklist sequencer
blacklist snd_intel8x0m
blacklist ac97
blacklist ac97_codec
blacklist snd_aw2
blacklist emu10k1
blacklist maestro3
blacklist mpu401
blacklist opl3
blacklist sb
blacklist sb_lib
blacklist ymfpci


# --- Input Devices ---
# Disable generic USB mouse/kb drivers to force specialized HID drivers (reduces overhead)
blacklist usbmouse
blacklist usbkbd
# Disable legacy joystick support (Remove joydev if you plan to use a controller)
blacklist joydev
# Disable MIDI support for legacy hardware
blacklist usb-midi
blacklist v_midi
# Prevent logging of all input events (Security/Performance)
blacklist evbug


# --- Legacy Hardware & Ports ---
# Disable Floppy disk controller
options floppy index=0
blacklist floppy
# Disable Parallel/Printer ports
blacklist parport
blacklist parport_pc
blacklist ppdev
blacklist lp
# Disable FireWire (IEEE 1394)
blacklist firewire_ohci
blacklist firewire_core
blacklist firewire_sbp2
blacklist ohci1394
blacklist ieee1394


# --- Obsolete Network & Storage Drivers ---
# Disable 10/100 Ethernet and ancient wireless cards
blacklist e100
blacklist 8139cp
blacklist 8139too
blacklist b43
blacklist b43legacy
blacklist ssb
# Disable legacy PATA/IDE and SCSI RAID controllers
blacklist pata_acpi
blacklist pata_amd
blacklist ide_pci_generic
blacklist megaraid_sas
blacklist qla2xxx
blacklist mptsas


# --- Miscellaneous ---
# Disable specific hardware debugging/crypto not needed for standard boot
blacklist hfi1
blacklist blowfish
blacklist amd76x_edac




# 2
# /etc/modprobe.d/nvidia.conf 

# --- Video Memory & Latency Optimizations ---
# Ensure video memory is preserved across suspend/resume cycles
options nvidia NVreg_PreserveVideoMemoryAllocations=1

# Set temporary storage path for video memory data during suspend
options nvidia NVreg_TemporaryFilePath=/tmp/nvidia

# Use Page Attribute Table (PAT) for better CPU-to-GPU communication performance
options nvidia NVreg_UsePageAttributeTable=1

# Do not pre-initialize all system memory allocations to save time/resources
options nvidia NVreg_InitializeSystemMemoryAllocations=0


# --- Bus & Interface Settings ---
# Force PCIe Gen4 speeds (standard for mobile 40-series GPUs)
options nvidia NVreg_EnablePCIeGen4=1

# Enable Message Signaled Interrupts (MSI) to reduce CPU overhead and latency
options nvidia NVreg_EnableMSI=1

# Enable Stream Memory Operations for better asynchronous data handling
options nvidia NVreg_EnableStreamMemOPs=1
# Skip checking PCI config space for faster initialization
options nvidia NVreg_CheckPCIConfigSpace=0

# Disable NvLink as it is not present/used on laptop hardware
options nvidia NVreg_NvLinkDisable=1


# --- Power Management (Battery & Performance Balance) ---
# PowerMizerEnable=0x1: Enable NVIDIA PowerMizer power management
# GpuPowerMizerMode=0x2: Set to Adaptive mode (downclocks on idle, max on load)
# EnableBrightnessControl=1: Ensure backlight control works on laptops
options nvidia NVreg_RegistryDwords="PowerMizerEnable=0x1;EnableBrightnessControl=1;ThermalConfiguration=1;FanControlState=1;GpuPowerMizerMode=0x2;AdaptivePowerMizer=1"

# PersistenceMode=0: Allow the GPU to fully power down when not in use (Crucial for battery)
options nvidia NVreg_PersistenceMode=0


# --- Buffers & Limits ---
# Set the size of the internal memory pool in MB
options nvidia NVreg_MemoryPoolSize=256

# Limit for remapping memory to avoid address space fragmentation
options nvidia NVreg_RemapLimit=64


# --- Device Node Permissions ---
# Root owns the device files
options nvidia NVreg_DeviceFileUID=0

# Assign device to GID 44 (standard 'video' group in Ubuntu/Debian)
options nvidia NVreg_DeviceFileGID=44

# Set read/write permissions for owner and group
options nvidia NVreg_DeviceFileMode=0660




# 3
# /etc/modprobe.d/usbhid.conf

# --- USB HID Latency Optimizations ---

# Set mouse polling rate to 500Hz (2ms interval) for lower input lag
options usbhid mousepoll=2

# Set joystick/gamepad polling rate to 500Hz
options usbhid jspoll=2

# Set keyboard polling rate to 500Hz
options usbhid kbpoll=2

# Increase timeout to 200ms to prevent lag when waking up wireless devices
options usbhid timeout=200




# 4
sudo update-initramfs -u -k all
```


<br><br>


## ENVIRONMENT

```bash
# 1
# /etc/environment


# --- System Paths ---
PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin"


# --- OpenMP & Parallelism (6 Cores Optimized) ---
OMP_NUM_THREADS=6
OMP_STACKSIZE=8M
OMP_PROC_BIND=close
OMP_DYNAMIC=false
OMP_WAIT_POLICY=passive
OMP_NESTED=false


# --- Memory & Network (Extreme Performance) ---
GLIBC_TUNABLES="glibc.malloc.trim_threshold=262144:glibc.malloc.mmap_threshold=262144:glibc.malloc.mmap_max=32768:glibc.malloc.arena_max=4"
IONICE_CLASS=best-effort
IONICE_LEVEL=2
VM_OVERCOMMIT_MEMORY=1
RES_OPTIONS="timeout:1 attempts:3 rotate ndots:1"
NETWORK_BUFFER_SIZE=32768
TCP_QUICKACK=1
PYTHONOPTIMIZE=1


# --- Java & Tools ---
_JAVA_OPTIONS="-XX:+UseG1GC -XX:MaxGCPauseMillis=100"
JAVA_TOOL_OPTIONS="-Xms512m -Xmx2g"


# --- NVIDIA (Ultra Low Latency & Stealth) ---
__GLX_VENDOR_LIBRARY_NAME=nvidia
__GL_PRIORITY=HIGH
__GL_GPU_MEMORY_ALLOCATION=100
__GL_NextGenCompiler=1
__GL_ASYNC_FLIP=1
__GL_ALLOW_UNOFFICIAL_PROTOCOL=0
__GL_MaxFramesAllowed=1
__GL_SYNC_TO_VBLANK=0
__GL_VR_ALLOWED=1
__GLX_DRISW=0
__GL_GSYNC_ALLOWED=1
__GL_YIELD=USLEEP
__GL_OPTIMIZE_FOR_LATENCY=1
__GL_GPU_THREAD_PRIORITY=HIGH
__GL_LOG_VERBOSE=0
__GL_DEBUG_LEVEL=0
__GL_SHADER_DISK_CACHE=1
__GL_SHADER_DISK_CACHE_PATH="/home/{YOUR USER}/.nvidia-shader-cache"
__GL_SHADER_DISK_CACHE_COMPRESS=1


# --- Mesa & Vulkan (Zero Overhead) ---
MESA_DEBUG=0
MESA_NO_ERROR=1
MESA_GLSL_CACHE_DISABLE=0
MESA_GLTHREAD=1
MESA_SHADER_CACHE_DIR="/home/{YOUR USER}/.mesa_shader_cache"
VK_DRIVER_FRAMETIME_ENABLE=0
vulkan_enable_validation=0
VK_SHADER_CACHE_DISABLE=0
VK_EXT_swapchain_maintenance1=1
VK_PRESENT_MODE=mailbox


# --- Wayland & Compositor ---
GBM_BACKEND=nvidia-drm
WLR_NO_HARDWARE_CURSOR=0
WLR_RENDER_MODE=mailbox
EGL_PLATFORM=wayland


# --- DXVK & Video (Silent & Fast) ---
DXVK_LOG_LEVEL=none
DXVK_ASYNC=1
DXVK_STATE_CACHE=1
DXVK_SHADER_DISK_CACHE_PATH="/home/{YOUR USER}/.dxvk-cache"
VDPAU_LOG_LEVEL=0
LIBVA_MESSAGING_LEVEL=0
CUDA_CACHE_PATH="/home/{YOUR USER}/.cuda-cache"
CUDA_LAUNCH_BLOCKING=0


# --- Intel (Efficiency & Hardware Scalability) ---
INTEL_DEBUG=null
INTEL_PERFORMANCE_MODE=1
INTEL_LOW_POWER_ENCODE=1
INTEL_ENABLE_GFX_CLKGATING=1
INTEL_ENABLE_NEW_SCHED=1
INTEL_ENABLE_SSBO=1
INTEL_COMPUTE_SHADER=1
INTEL_HW_BLIT=1
INTEL_COMPRESSION_RENDER_TARGET=1
INTEL_FAST_CLEAR=1


# --- Chromium (Wayland Native + HW Overlays + No Logs) ---
CHROMIUM_FLAGS="--ozone-platform-hint=wayland --enable-gpu-rasterization --enable-zero-copy --enable-native-gpu-memory-buffers --enable-hardware-overlays --enable-features=Vulkan,VulkanFromANGLE,DefaultAngleVulkan,RunVideoAcceleratorOnGpuProcess --ignore-gpu-blocklist --log-level=3 --no-report-upload --disable-logging --disable-breakpad"


# --- GTK, GDK & UI (Minimal Overhead) ---
GTK_ENABLE_ANIMATIONS=0
GDK_BACKEND=wayland
GDK_USE_COMPOSITING=0
GDK_DEBUG=none
NO_AT_BRIDGE=1


# --- SDL & QT ---
SDL_VIDEODRIVER=wayland,x11
QT_QPA_PLATFORM=wayland
QT_LOGGING_RULES="*.debug=false;*.info=false;*.warning=false"
QT_QUICK_NO_ANIMATION=1
QT_OPENGL_NO_ERROR_CHECK=1


# --- Audio & Pipewire (Low Latency Stable) ---
PIPEWIRE_DEBUG=0
PIPEWIRE_ENABLE_3D=1
PIPEWIRE_DISABLE_LATENCY_SMOOTHING=0
PIPEWIRE_CPU_PRIORITY=high


# --- Wine & Steam (Pure Silent) ---
WINEDEBUG=-all
PROTON_LOG=0
STEAM_VERBOSE=0
STEAM_DEBUG=0
```


<br><br>


## I USE GNOME ON WAYLAND AND GDM3 

```bash
# --- 1. Rendering & Efficiency ---
# Triple buffering for smoothness, VRR to save power on static images, and Unredirect for full-screen performance
gsettings set org.gnome.mutter experimental-features "['triple-buffering', 'variable-refresh-rate', 'unredirect-fullscreen-windows']"


# --- 2. Power Management (Battery Life Optimization) ---
# Automatically trigger power-saver profile when battery is low
gsettings set org.gnome.settings-daemon.plugins.power power-saver-profile-on-low-battery true

# On Battery: Set screen dimming/blanking and suspend after 15 minutes (900 seconds)
gsettings set org.gnome.settings-daemon.plugins.power sleep-inactive-battery-timeout 900
gsettings set org.gnome.settings-daemon.plugins.power sleep-inactive-battery-type 'suspend'

# On AC Power: Never automatically suspend
gsettings set org.gnome.settings-daemon.plugins.power sleep-inactive-ac-type 'nothing'


# --- 3. Interface & UI (Reducing Resource Usage) ---
# Disable animations to save CPU/GPU cycles (crucial for battery and responsiveness)
gsettings set org.gnome.desktop.interface enable-animations false

# Disable window snapping, dynamic workspaces, and hot corners to keep the environment lean
gsettings set org.gnome.mutter edge-tiling false
gsettings set org.gnome.mutter dynamic-workspaces false
gsettings set org.gnome.desktop.interface enable-hot-corners false


# --- 4. Background Processes (Reducing CPU Wakeups) ---
# Disable external search providers and indexing (major battery drainers)
gsettings set org.gnome.desktop.search-providers disable-external true
gsettings set org.gnome.desktop.search-providers disabled "['org.gnome.Contacts.desktop', 'org.gnome.Documents.desktop', 'org.gnome.Nautilus.desktop']"

# Stop automatic update downloads to save Wi-Fi power and background CPU usage
gsettings set org.gnome.software download-updates false

# Disable technical problem reporting (whoopsie/telemetry) to minimize background noise
gsettings set org.gnome.desktop.privacy report-technical-problems false


# --- 5. Peripherals & Accessibility ---
# Mouse: Flat acceleration profile for raw, predictable input
gsettings set org.gnome.desktop.peripherals.mouse accel-profile 'flat'
gsettings set org.gnome.desktop.peripherals.mouse speed 0

# Keyboard: Faster typing response with reduced delay and high repeat rate
gsettings set org.gnome.desktop.peripherals.keyboard delay 300
gsettings set org.gnome.desktop.peripherals.keyboard repeat-interval 30
```


<br><br>


## 

```bash

```


<br><br>


