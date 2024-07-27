# Wyze Camera Experiments

## Notes

Testing on a Wyze Cam v2, model numner WYZEC2.

### Preparing the SD Card

Downloaded [openmiko_firmware.bin](https://github.com/openmiko/openmiko/releases/download/v1.0.0-alpha.1/openmiko_firmware.bin), renamed it to `boot.bin`, and copied it to the root of a freshly VFAT formatted SD card.

Also on the card, created the following file:

`/config/overlay/etc/hostname`

```
puck
```

Used the scripts included in the `utilities/` directory to create `wpa_supplicant.conf` and `openmiko.conf` files, which were also copied to the `/config/overlay/etc/` directory on the SD card.

`/config/overlay/etc/wpa_supplicant.conf`

```
ctrl_interface=/var/run/wpa_supplicant
ctrl_interface_group=0
ap_scan=1
bgscan="simple:30:-45:300"

network={
        ssid="K"
        scan_ssid=0
        key_mgmt=WPA-PSK
        pairwise=CCMP TKIP
        group=CCMP TKIP WEP104 WEP40
        psk="[REDACTED]"
        priority=2
}
```

`/config/overlay/etc/openmiko.conf`

```
ENABLE_DEBUG=0
# Starts ffmpeg on startup recording from the v4l2rtspserver
ENABLE_RECORDING=0
# Start the micropython based api
ENABLE_API=1
# Use a swapfile on the sdcard
ENABLE_SWAP=1
# Use this setting if swapfile is a dedicated partition
SWAPFILE=/dev/mmcblk0p1
# Set partition to use for mounting at /sdcard
SD_PARTITION=/dev/mmcblk0p2
SD_FILESYSTEM=vfat
# Enable MJPEG over HTTP
ENABLE_MJPEG_OVER_HTTP=0
MJPEG_FPS=15
# Script that detects nighttime/daytime and turns on IR LEDs
ENABLE_AUTONIGHT=1
AUTONIGHT_PARAMS="-j 3 -w 3 -1 1200000 -2 930000,14,10 -3 3000,17,8"
# Set alternate videocapture settings file
# VIDEO_CAPTURE_SETTINGS=/etc/videocapture_settings_1_encoder.json
# Set enable audio (is currently buggy, use at your own risk!)
ENABLE_AUDIO=0
# Make sure these settings match the /etc/videocapture_settings.json file
VIDEO_DEV_1=/dev/video3
VIDEO_DEV_2=/dev/video4
VIDEO_DEV_3=/dev/video5
# Use 8189fs for WyzeCam V2. For the WyzeCam Pan and Dafang use 8189es
WIFI_MODULE=8189fs
# Disables LEDs on the camera
DISABLE_LEDS=0
# Enable Logging
ENABLE_LOGGING=0
```

### Flashing Wyze Cam

* Insert the SD card into the Wyze Cam.
* Hold down the button, plug in the power, wait about 3 seconds for the blue and white LEDs to light (and stay lit), and let go of the button.
* The camera should be ready to SSH in to when the amber LED blinks at a rate of about half a second.

### Playback

VLC
:   `rtsp://10.0.0.122:8554/video3_unicast`

### SSH

You can SSH in to a camera with `ssh root@10.0.0.122`. The default password is `root`.

### Misc.

```console
# dmesg
[    0.000000] Initializing cgroup subsys cpu
[    0.000000] Initializing cgroup subsys cpuacct
[    0.000000] Linux version 3.10.14 (root@a79fc9893cc9) (gcc version 4.7.4 (Buildroot 2016.02) ) #1 PREEMPT Fri Mar 24 17:50:47 UTC 2023
[    0.000000] bootconsole [early0] enabled
[    0.000000] CPU0 RESET ERROR PC:80207FE0
[    0.000000] [<80207fe0>] __delay+0x0/0x10
[    0.000000] CPU0 revision is: 00d00101 (Ingenic Xburst)
[    0.000000] FPU revision is: 00b70000
[    0.000000] cgu_get_rate, parent = 860160000, rate = 0, m = 0, n = 0, reg val = 0x000020ff
[    0.000000] CCLK:860MHz L2CLK:430Mhz H0CLK:200MHz H2CLK:200Mhz PCLK:100Mhz
[    0.000000] Determined physical RAM map:
[    0.000000]  memory: 004f7000 @ 00010000 (usable)
[    0.000000]  memory: 00079000 @ 00507000 (usable after init)
[    0.000000] User-defined physical RAM map:
[    0.000000]  memory: 06000000 @ 00000000 (usable)
[    0.000000] Initrd not found or empty - disabling initrd
[    0.000000] Zone ranges:
[    0.000000]   Normal   [mem 0x00000000-0x05ffffff]
[    0.000000] Movable zone start for each node
[    0.000000] Early memory node ranges
[    0.000000]   node   0: [mem 0x00000000-0x05ffffff]
[    0.000000] On node 0 totalpages: 24576
[    0.000000] free_area_init_node: node 0, pgdat 80503cf0, node_mem_map 81000000
[    0.000000]   Normal zone: 192 pages used for memmap
[    0.000000]   Normal zone: 0 pages reserved
[    0.000000]   Normal zone: 24576 pages, LIFO batch:3
[    0.000000] Primary instruction cache 32kB, 8-way, VIPT, linesize 32 bytes.
[    0.000000] Primary data cache 32kB, 8-way, VIPT, no aliases, linesize 32 bytes
[    0.000000] pls check processor_id[0x00d00101],sc_jz not support!
[    0.000000] MIPS secondary cache 128kB, 8-way, linesize 32 bytes.
[    0.000000] pcpu-alloc: s0 r0 d32768 u32768 alloc=1*32768
[    0.000000] pcpu-alloc: [0] 0
[    0.000000] Built 1 zonelists in Zone order, mobility grouping off.  Total pages: 24384
[    0.000000] Kernel command line: console=tty0 console=ttyS1,115200n8 mem=96M@0x0 ispmem=8M@0x6000000 rmem=24M@0x6800000 init=/linuxrc rootfstype=squashfs root=/dev/mtdblock2 rw mtdparts=jz_sfc:256k(boot),2048k(kernel),13504k(root),256k(config),256k(para),-(flag)
[    0.000000] PID hash table entries: 512 (order: -1, 2048 bytes)
[    0.000000] Dentry cache hash table entries: 16384 (order: 4, 65536 bytes)
[    0.000000] Inode-cache hash table entries: 8192 (order: 3, 32768 bytes)
[    0.000000] Memory: 91360k/98304k available (3877k kernel code, 6944k reserved, 1205k data, 484k init, 0k highmem)
[    0.000000] SLUB: HWalign=32, Order=0-3, MinObjects=0, CPUs=1, Nodes=1
[    0.000000] Preemptible hierarchical RCU implementation.
[    0.000000] NR_IRQS:418
[    0.000000] clockevents_config_and_register success.
[    0.000024] Calibrating delay loop... 858.52 BogoMIPS (lpj=4292608)
[    0.087752] pid_max: default: 32768 minimum: 301
[    0.092734] Mount-cache hash table entries: 512
[    0.097831] Initializing cgroup subsys debug
[    0.102092] Initializing cgroup subsys freezer
[    0.107912] devtmpfs: initialized
[    0.112580] regulator-dummy: no parameters
[    0.116894] NET: Registered protocol family 16
[    0.136778] bio: create slab <bio-0> at 0
[    0.143086] jz-dma jz-dma: JZ SoC DMA initialized
[    0.148208] usbcore: registered new interface driver usbfs
[    0.153749] usbcore: registered new interface driver hub
[    0.159239] usbcore: registered new device driver usb
[    0.164452] i2c-gpio i2c-gpio.1: using pins 57 (SDA) and 58 (SCL)
[    0.170709]  (null): set:249  hold:250 dev=100000000 h=500 l=500
[    0.176836] media: Linux media interface: v0.10
[    0.181396] Linux video capture interface: v2.00
[    0.188130] Switching to clocksource jz_clocksource
[    0.193269] cfg80211: Calling CRDA to update world regulatory domain
[    0.200514] jz-dwc2 jz-dwc2: cgu clk gate get error
[    0.205434] jz-dwc2 jz-dwc2: regulator vbus get error
[    0.210518] DWC IN OTG MODE
[    0.363798] sft id =========================off
[    0.368390] dwc2 dwc2: Keep PHY ON
[    0.371758] dwc2 dwc2: Using Buffer DMA mode
[    0.575937] dwc2 dwc2: Core Release: 3.00a
[    0.580048] dwc2 dwc2: DesignWare USB2.0 High-Speed Host Controller
[    0.586403] dwc2 dwc2: new USB bus registered, assigned bus number 1
[    0.593909] hub 1-0:1.0: USB hub found
[    0.597649] hub 1-0:1.0: 1 port detected
[    0.601750] dwc2 dwc2: DWC2 Host Initialized
[    0.606298] NET: Registered protocol family 2
[    0.611234] TCP established hash table entries: 1024 (order: 1, 8192 bytes)
[    0.618339] TCP bind hash table entries: 1024 (order: 0, 4096 bytes)
[    0.624766] TCP: Hash tables configured (established 1024 bind 1024)
[    0.631214] TCP: reno registered
[    0.634466] UDP hash table entries: 256 (order: 0, 4096 bytes)
[    0.640356] UDP-Lite hash table entries: 256 (order: 0, 4096 bytes)
[    0.647001] NET: Registered protocol family 1
[    0.680049] dwc2 dwc2: ID PIN CHANGED!
[    0.683837] init DWC as A_HOST
[    0.973231] freq_udelay_jiffys[0].max_num = 10
[    0.977660] cpufreq  udelay  loops_per_jiffy
[    0.982075] 12000     59885   59885
[    0.985383] 24000     119771  119771
[    0.988786] 60000     299428  299428
[    0.992229] 120000    598857  598857
[    0.995889] 200000    998095  998095
[    0.999342] 300000    1497142         1497142
[    1.003074] 600000    2994285         2994285
[    1.006758] 792000    3952457         3952457
[    1.010467] 1008000   5030400         5030400
[    1.014276] 1200000   5988571         5988571
[    1.024454] exFAT: Version 1.2.9
[    1.027949] jffs2: version 2.2. © 2001-2006 Red Hat, Inc.
[    1.033929] msgmni has been set to 178
[    1.039027] io scheduler noop registered
[    1.042964] io scheduler cfq registered (default)
[    1.050547] jz-uart.1: ttyS1 at MMIO 0x10031000 (irq = 58) is a uart1
[    1.058832] console [ttyS1] enabled, bootconsole disabled
[    1.074933] brd: module loaded
[    1.080617] loop: module loaded
[    1.084745] zram: Created 2 device(s) ...
[    1.088969] wifi regulator missing
[    1.093310] jz SADC driver registeres over!
[    1.099075] jz TCU driver register completed
[    1.104003] the id code = c84018, the flash name is GD25Q127C
[    1.109967] JZ SFC Controller for SFC channel 0 driver register
[    1.116165] 6 cmdlinepart partitions found on MTD device jz_sfc
[    1.122298] Creating 6 MTD partitions on "jz_sfc":
[    1.127304] 0x000000000000-0x000000040000 : "boot"
[    1.132947] 0x000000040000-0x000000240000 : "kernel"
[    1.138791] 0x000000240000-0x000000f70000 : "root"
[    1.144475] 0x000000f70000-0x000000fb0000 : "config"
[    1.150302] 0x000000fb0000-0x000000ff0000 : "para"
[    1.155992] 0x000000ff0000-0x000001000000 : "flag"
[    1.161616] SPI NOR MTD LOAD OK
[    1.165442] jzmmc_v1.2 jzmmc_v1.2.0: vmmc regulator missing
[    1.203156] jzmmc_v1.2 jzmmc_v1.2.0: register success!
[    1.208559] jzmmc_v1.2 jzmmc_v1.2.1: vmmc regulator missing
[    1.254080] jzmmc_v1.2 jzmmc_v1.2.1: register success!
[    1.259872] ledtrig-cpu: registered to indicate activity on CPUs
[    1.266173] ------------ init codec driver start!
[    1.271109] jz_codec_probe: probe() start
[    1.275306] jz_codec_probe, codec iomem is :0xb0021000
[    1.280633] request spk en gpio 63 ok!
[    1.280641] jz_codec_probe: probe() done
[    1.284789] i2s global init
[    1.287693] current codec is :8206db80
[    1.291795] cgu_set_rate, parent = 860160000, rate = 2048000, n = 420, reg val = 0x000021a4
[    1.291809] i2s_global_init, i2s clk rate is 2048000
[    1.291821] cgu_enable,cgu_i2s reg val = 0x200021a4
[    1.291835] i2s init success.
[    1.693806] TCP: cubic registered
[    1.697252] NET: Registered protocol family 17
[    1.702971] input: gpio-keys as /devices/platform/gpio-keys/input/input0
[    1.710288] drivers/rtc/hctosys.c: unable to open rtc device (rtc0)
[    1.719634] Freeing unused kernel memory: 484K (80507000 - 80580000)
[    1.793143] dwc2 dwc2:  ++OTG Interrupt: A-Device Timeout Change++
[    3.933040] jzmmc_v1.2 jzmmc_v1.2.0: card inserted, state=0
[    5.196508] mmc0: new high speed SDHC card at address aaaa
[    5.202507] mmcblk0: mmc0:aaaa SC32G 29.7 GiB
[    5.211703]  mmcblk0: p1
[   13.483421] zram0: detected capacity change from 0 to 50331648
[   13.509937] EXT4-fs (zram0): couldn't mount as ext3 due to feature incompatibilities
[   13.521960] EXT4-fs (zram0): mounting ext2 file system using the ext4 subsystem
[   13.534773] EXT4-fs (zram0): mounted filesystem without journal. Opts: (null)
[   26.830060] [EXFAT] trying to mount...
[   28.345485] Adding 2097148k swap on /dev/mmcblk0p1.  Priority:-1 extents:1 across:2097148k SS
[   28.433991] RTL871X: module init start
[   28.437888] RTL871X: rtl8189fs v4.3.24.8_22657.20170607
[   28.447695] ######platform_wifi_power_on:
[   28.488751] mmc1: card claims to support voltages below the defined range. These will be ignored.
[   28.508861] mmc1: new SDIO card at address 0001
[   29.844988] RTL871X: HW EFUSE
[   29.848094] RTL871X: hal_com_config_channel_plan chplan:0x20
[   29.952102] RTL871X: rtw_regsty_chk_target_tx_power_valid return _FALSE for band:0, path:0, rs:0, t:-1
[   29.963589] RTL871X: rtw_ndev_init(wlan0) if1 mac_addr=c8:02:8f:82:a7:9a
[   29.998789] RTL871X: module init ret=0
[   32.513592] RTL871X: rtw_set_802_11_connect(wlan0)  fw_state=0x00000008
[   32.750175] RTL871X: start auth
[   32.756378] RTL871X: OnDisassoc(wlan0) reason=8, ta=54:af:97:3a:fe:d0
[   34.413971] RTL871X: rtw_set_802_11_connect(wlan0)  fw_state=0x00000008
[   34.512652] RTL871X: start auth
[   34.518960] RTL871X: auth success, start assoc
[   34.529016] RTL871X: assoc success
[   34.560838] RTL871X: recv eapol packet
[   34.584620] RTL871X: send eapol packet
[   34.601472] RTL871X: recv eapol packet
[   34.608219] RTL871X: send eapol packet
[   34.622758] RTL871X: set pairwise key camid:4, addr:54:af:97:3a:fe:d0, kid:0, type:AES
[   34.643970] RTL871X: set group key camid:5, addr:54:af:97:3a:fe:d0, kid:1, type:AES
[   37.468617] name : i2c-gpio1 nr : 1
[   37.472241] name : i2c0 nr : 0
[   37.563475]  sensor_read: addr=0xa value = 0xf
[   37.568706]  sensor_read: addr=0xb value = 0x23
[   37.663441]  sensor_read: addr=0xa value = 0xf
[   37.668657]  sensor_read: addr=0xb value = 0x23
[   37.673680] info: success sensor find : jxf23
[   37.721135] register all isp device successfully!
[   37.731181] @@@@ tx-isp-probe ok @@@@@
[   40.053338] v4l2loopback driver version 0.12.5 loaded
[   40.120654] set sensor gpio as PA-low-10bit
[   40.127205] probe ok ------->jxf23
[   40.278189] -----jxf23_detect: 529 ret = 0, v = 0x0f
[   40.278691] -----jxf23_detect: 537 ret = 0, v = 0x23
[   40.278705] jxf23 0-0040: jxf23 chip found @ 0x40 (i2c0)
[   40.284625] tx_isp: Registered sensor subdevice jxf23 0-0040
[   40.290522] do not support ctrl->cmd ====1
[   40.290540] do not support ctrl->cmd ====0
[   40.294884] do not support ctrl->cmd ====1
[   40.569357] do not support ctrl->cmd ====1
[   40.570268] jxf23 stream on
[   40.592407] ###### image_tuning_v4l2_open 4329 #######
[   40.617143] &&& chan1  scaler.max_width = 1920 max_height = 1080  min_width = 128 min_height = 128 &&&
```

```console
# lsmod
Module                  Size  Used by    Tainted: G
v4l2loopback           22369  4
snd_aloop              12002  0
snd_pcm                68453  1 snd_aloop
snd_page_alloc          3810  1 snd_pcm
snd_timer              18670  1 snd_pcm
snd                    39168  3 snd_aloop,snd_pcm,snd_timer
sensor_jxf23            9136  1
tx_isp                337860  3
sinfo                  12972  0
8189fs               1114399  0
```

```
# cat /proc/cpuinfo
system type             : bull
machine                 : Unknown
processor               : 0
cpu model               : Ingenic Xburst V0.1  FPU V0.0
BogoMIPS                : 858.52
wait instruction        : yes
microsecond timers      : no
tlb_entries             : 32
extra interrupt vector  : yes
hardware watchpoint     : yes, count: 1, address/irw mask: [0x0fff]
isa                     : mips32r1
ASEs implemented        :
shadow register sets    : 1
kscratch registers      : 7
core                    : 0
VCED exceptions         : not available
VCEI exceptions         : not available

Hardware                : isvp
Serial                  : 00000000 00000000 00000000 00000000
```

```console
# cat /proc/meminfo
MemTotal:          91844 kB
MemFree:            4200 kB
Buffers:             804 kB
Cached:            14236 kB
SwapCached:            0 kB
Active:             8768 kB
Inactive:          12384 kB
Active(anon):       1880 kB
Inactive(anon):     4384 kB
Active(file):       6888 kB
Inactive(file):     8000 kB
Unevictable:           0 kB
Mlocked:               0 kB
SwapTotal:       2097148 kB
SwapFree:        2097148 kB
Dirty:                 8 kB
Writeback:             0 kB
AnonPages:          6132 kB
Mapped:            38068 kB
Shmem:               152 kB
Slab:               5980 kB
SReclaimable:       3168 kB
SUnreclaim:         2812 kB
KernelStack:         552 kB
PageTables:          476 kB
NFS_Unstable:          0 kB
Bounce:                0 kB
WritebackTmp:          0 kB
CommitLimit:     2143068 kB
Committed_AS:     197800 kB
VmallocTotal:    1048372 kB
VmallocUsed:       35532 kB
VmallocChunk:     999352 kB
```

## Cameras

### Puck

<dl>
  <dt>Hostname</dt>
  <dd><code>puck</code></dd>
  <dt>IP</dt>
  <dd><code>10.0.0.122</code></dd>
  <dt>MAC</dt>
  <dd><code>C8:02:8F:82:A7:9A</code></dd>
</dl>


