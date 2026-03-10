# 🌟 ubuntu-realtime-lowlatency


<br><br>


## Linux Kernel

```bash
# 1
sudo mkdir -p /etc/apt/keyrings
wget -qO - https://dl.xanmod.org/archive.key | sudo gpg --dearmor -vo /etc/apt/keyrings/xanmod-archive-keyring.gpg

# 2
echo "deb [signed-by=/etc/apt/keyrings/xanmod-archive-keyring.gpg] http://deb.xanmod.org $(lsb_release -sc) main" | sudo tee /etc/apt/sources.list.d/xanmod-release.list

# 3
sudo apt install linux-xanmod-lts-x64v3
```


<br>


## GRUB

```bash
# 1
# Place in file /etc/default/grub

GRUB_DEFAULT=0
GRUB_TIMEOUT=3
GRUB_DISTRIBUTOR="Ubuntu realtime-lowlatency"
GRUB_CMDLINE_LINUX_DEFAULT="lockdown=integrity pti=on vsyscall=none page_poison=0 init_on_alloc=1 init_on_free=1 slab_nomerge quiet splash fbcon=nodefer pcie_port_pm=on pci=pcie_bus_perf pci=noaer pcie_aspm=force iommu=pt random.trust_cpu=off random.trust_bootloader=off split_lock_detect=off processor.max_cstate=4 intel_pstate=active intel_idle.max_cstate=4 intel_iommu=on intel_pmc_core.ltr_ignore=1 cpufreq.default_governor=schedutil preempt=voluntary workqueue.power_efficient=off irqaffinity=0,1 nohz_full=2-11 rcu_nocbs=2-11 rcu_expedited rcu_cpu_stall_timeout=60 rcu_nocb_poll rcu_boost=1 tsx=off mce=ignore_ce page_alloc.shuffle=1 transparent_hugepage=madvise skew_tick=1 tsc=reliable clocksource=tsc tsc=perfect threadirqs mitigations=auto nvidia-drm.modeset=1 nouveau.modeset=0 i915.fastboot=1 i915.enable_dc=1 i915.enable_psr=1 i915.enable_guc=3 i915.semaphores=1 zswap.enabled=1 zswap.compressor=zstd zswap.max_pool_percent=25 zswap.zpool=zbud zswap.same_filled_pages_enabled=1 elevator=mq-deadline scsi_mod.use_blk_mq=1 blk_mq_affinity=2 nvme_core.default_ps_max_latency_us=3000 nvme_core.io_timeout=255 nvme_core.admin_timeout=60 nvme.use_threaded_interrupts=1 snd_pcm_periods=4 snd_pcm_buffer_size=16384 snd_hda_intel.prealloc_buffer=1 snd_hda_intel.dma_period_bytes=4096 snd_hda_intel.dma_buffer_bytes=16384 snd_hda_intel.beep_mode=0 snd_hda_intel.enable_silent_mode=0 snd_hda_intel.max_buffer_size=65536 snd_hda_intel.position_fix=1 snd_hda_intel.align_buffer_size=1 snd_hda_intel.enable_msi=1 snd_pcm_max_preload=1 snd_hwdep=1 snd_hda_intel.enable_auto_mute=0 snd_hda_intel.power_save=1 usbcore.autosuspend=1 xhci_hcd.quirks=270336 raid=noautodetect btusb.enable_autosuspend=1 nosoftlockup nowatchdog nmi_watchdog=0 audit=0 audit_backlog_limit=0 printk.time=0 loglevel=0 systemd.show_status=0 selinux=0 apparmor=0 security=none mem_sleep_default=deep reboot=efi"
GRUB_CMDLINE_LINUX=""
GRUB_TERMINAL=console 

# 2
sudo update-grub
```

