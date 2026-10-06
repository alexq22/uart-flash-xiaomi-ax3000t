### Xiaomi AX3000T RD23 (Global version): OpenWrt installation (UART flash)

* guide for installing OpenWrt on the [Xiaomi AX3000T](https://openwrt.org/toh/xiaomi/ax3000t) **RD23** (Global Version).
* installation method: **UART flash**.
  * *the following OpenWrt installation method requires opening the device, connecting a UART cable, and following a specific set of steps. :warning: This process is recommended only for advanced users, and may soft-brick your device. Please do not forget to make a backup of original router partitions before performing any actions with the router.*

#### 1. Open the router case and connect a UART adapter to the router.

<dl>
<dd>

**Opening the router case**  
Remove the sticker/label on the bottom of the device to reveal two screws (it might be a good idea to use a hot air from a hair dryer to soften the glue under the sticker/label). Unscrew these two screws and use a plastic spudger or pry-tool in order to remove the top cover.

</dd>
</dl>

<dl>
<dd>

**UART adapter: CH340G-2**  
I used USB-to-TTL UART CH340G-2 adapter ([this](https://a.aliexpress.com/_EzT03qB)), and 4-pin UART male header ([this](https://a.aliexpress.com/_EIBTAlt)).  
<img width="690" height="460" alt="image" src="https://github.com/user-attachments/assets/5912611c-e65d-4564-9b90-def7737ec081" />


</dd>
</dl>

<dl>
<dd>

**Connection of UART adapter to the router**  
UART pins: insert the 4-pin UART header into the router's UART slot (no soldering required):  
<img width="408" height="500" alt="image" src="https://github.com/user-attachments/assets/4167b9f4-f2fa-4717-a794-672c827bbbed" />


:information_source: Important:
* set the jumper on the UART adapter to operate at 3.3V.
* the RX pin on the router shall be connected to the TX pin on the UART adapter.
* the TX pin on the router shall be connected to the RX pin on the UART adapter.
* the GND pin on the router shall be connected to the GND pin on the UART adapter.  
  <img width="336" height="499" alt="image" src="https://github.com/user-attachments/assets/7d28b528-ea09-43a1-8d04-6cef028a0641" />


</dd>
</dl>

#### 2. Insert UART adapter into the USB port of your PC, open and configure [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html), and then plug in the power adapter of the router. You should start receiving uart-log output from the router, which will be displayed in PuTTY.  

<dl>
<dd>

**PuTTY configuration**  
Check the COM port to which your UART adapter is assigned in the operating system. For example, if it's assigned to COM5 according to Device Manager, then:
Open PuTTY => select "Connection type:" `Serial` => change Serial Line from `COM1` to `COM5` => set "Speed" to value `115200` => click "Open" button. PuTTY is now configured and waiting to receive information once the router is turned on.  
  <img width="450" height="407" alt="image" src="https://github.com/user-attachments/assets/6c71e1f6-0b56-42d0-9ffc-4c941203f082" />


</dd>
</dl>

<dl>
<dd>

**Receiving information from the router through UART.**  
<img width="690" height="388" alt="image" src="https://github.com/user-attachments/assets/1b436e0c-59f6-4174-8bc2-960a31efb453" />


</dd>
</dl>

#### 3. Close PuTTY and power off the router.
#### 4. Download software for router flashing and prepare it. Also, plug an Ethernet cable into one of the middle ports of the router (the 2nd or 3rd port on the router), and then connect the other end of the Ethernet cable to your PC. Disable Windows firewall.

<dl>
<dd>

**software list**  
* 4.1: download [mtk_uartboot-v0.1.1-x86_64-pc-windows-msvc.zip](https://github.com/981213/mtk_uartboot/releases) for Windows and unzip it.
* 4.2: download [bl2-mt7981-bga-ddr3-ram.bin ](https://github.com/981213/tf-a-mtk/releases) and place it in the folder with unzipped 'mtk_uartboot.exe'.
* 4.3: download FIP file [openwrt-mediatek-filogic-xiaomi_mi-router-ax3000t-ubootmod-bl31-uboot.fip ](https://downloads.openwrt.org/snapshots/targets/mediatek/filogic/) and place it in the folder with unzipped 'mtk_uartboot.exe'.
* 4.4: download [tftpd64](https://github.com/PJO2/tftpd64/releases) and unzip it.
* 4.5: download ITB file [openwrt-mediatek-filogic-xiaomi_mi-router-ax3000t-ubootmod-initramfs-recovery.itb](https://downloads.openwrt.org/snapshots/targets/mediatek/filogic/) and place it in the folder with unzipped 'tftpd64.exe'.
* 4.6: download UBI file [openwrt-mediatek-filogic-xiaomi_mi-router-ax3000t-initramfs-factory.ubi](https://downloads.openwrt.org/snapshots/targets/mediatek/filogic/openwrt-mediatek-filogic-xiaomi_mi-router-ax3000t-initramfs-factory.ubi).
* 4.7: download BIN file [openwrt-mediatek-filogic-xiaomi_mi-router-ax3000t-squashfs-sysupgrade.bin](https://downloads.openwrt.org/snapshots/targets/mediatek/filogic/openwrt-mediatek-filogic-xiaomi_mi-router-ax3000t-squashfs-sysupgrade.bin).
* 4.8: download [WinSCP](https://winscp.net/eng/download.php) and install it.

</dd>
</dl>

#### 5. Start Windows Command Prompt console. In the console, navigate to the folder containing the unzipped 'mtk_uartboot.exe' file. Then, execute the following command (* *before executing the command below, ensure that you replace the COM port number in the command below with the port number assigned by your operating system, which you detected in step 2.*):

```
mtk_uartboot -s COM5 --payload bl2-mt7981-bga-ddr3-ram.bin --aarch64 --fip openwrt-mediatek-filogic-xiaomi_mi-router-ax3000t-ubootmod-bl31-uboot.fip && putty.exe -serial COM5 -sercfg 115200,8,n,1,N
```

#### 6. As soon as the "Handshake..." text appears in the Windows Command Prompt console, turn on the power adapter on the router. If router successfully processes the 'mtk_uartboot' loading, additional information will appear in the Windows console. Following that, a new window with PuTTY will automatically appear, attempting to retrieve the 'openwrt-mediatek-filogic-xiaomi_mi-router-ax3000t-ubootmod-initramfs-recovery.itb' file.

* *note: if an infinite loop ending with the message "NOTICE: Hello BL31!!!" appears in PuTTY, power off the router and start from step 5 again. Sometimes you may need to repeat it 6-10 times before the router attempts to retrieve the 'itb' file, instead of the loop ending with the 'NOTICE: Hello BL31!!!' message*).

<dl>
<dd>

**Windows console output with 'mtk_uartboot' loading, and PuTTY console output with attempts to retrieve the 'itb' file.**  
<img width="690" height="335" alt="image" src="https://github.com/user-attachments/assets/0394f850-2e88-484a-8408-29612faf1c33" />


</dd>
</dl>

#### 7. Double click on "tftpd64.exe" to start TFTP server, and select the Server Interface `192.168.1.254` in tftpd64. It should allow the 'openwrt-mediatek-filogic-xiaomi_mi-router-ax3000t-ubootmod-initramfs-recovery.itb' file to be transferred into the router's RAM, enabling OpenWrt to load from RAM (which will be displayed in PuTTY).

<dl>
<dd>

**PuTTY output log with initramfs-recovery.itb loading**  
```
#################################################################
         #################################################################
         #################################################################
         #################################################################
         #################################################################
         #################################################################
         #################################################################
         #################################################################
         ###
         1.2 MiB/s
done
Bytes transferred = 7667712 (750000 hex)
## Loading kernel from FIT Image at 46000000 ...
   Using 'config-1' configuration
   Trying 'kernel-1' kernel subimage
     Description:  ARM64 OpenWrt Linux-6.6.29
......
......
......
BusyBox v1.36.1 (2024-05-05 09:27:18 UTC) built-in shell (ash)

  _______                     ________        __
 |       |.-----.-----.-----.|  |  |  |.----.|  |_
 |   -   ||  _  |  -__|     ||  |  |  ||   _||   _|
 |_______||   __|_____|__|__||________||__|  |____|
          |__| W I R E L E S S   F R E E D O M
 -----------------------------------------------------
 OpenWrt SNAPSHOT, r26181-d1504a1e76
 -----------------------------------------------------
=== WARNING! =====================================
There is no root password defined on this device!
Use the "passwd" command to set up a new password
in order to prevent unauthorized SSH logins.
--------------------------------------------------
root@OpenWrt:/# 
```

</dd>
</dl>

#### 8. Once OpenWrt from 'initramfs-recovery.itb' is loaded, open WinSCP tool, login to 192.168.1.1, and upload the 'openwrt-mediatek-filogic-xiaomi_mi-router-ax3000t-initramfs-factory.ubi' file to the router's `/tmp` folder.

<dl>
<dd>

**WinSCP configuration**  
Open WinSCP => select "Tabs", "New Tab" => "New Site" => select File Protocol: `SCP` => Host name: `192.168.1.1` => Port number: `22` => User name: `root` => click "Login" button.  
<img width="540" height="366" alt="image" src="https://github.com/user-attachments/assets/4b884ea8-39ea-4929-bca8-85f47a1b3ce8" />


</dd>
</dl>

#### 9. In PuTTY, enter the following command:

```
ubidetach -p /dev/mtd8
```

After `mtd8` is detached, enter the following command:

```
ubiformat /dev/mtd8 -y -f /tmp/openwrt-mediatek-filogic-xiaomi_mi-router-ax3000t-initramfs-factory.ubi
```

After successfully completing the ubiformat, reboot the router by entering `reboot`. It should boot to the OpenWrt initramfs system now.

#### 10. As soon as OpenWrt is reloaded, open WinSCP tool, login to 192.168.1.1, and upload the 'openwrt-mediatek-filogic-xiaomi_mi-router-ax3000t-squashfs-sysupgrade.bin' file to the router's `/tmp` folder.

#### 11. In PuTTY, enter the following command:

```
sysupgrade -n /tmp/openwrt-mediatek-filogic-xiaomi_mi-router-ax3000t-squashfs-sysupgrade.bin
```

After successfully completing the sysupgrade, the router will reboot automatically.

#### 12. As soon as OpenWrt is reloaded, enter the following commands in PuTTY:

```
fw_setenv boot_wait on
fw_setenv uart_en 1
fw_setenv ssh_en 1
fw_setenv boot_fw1 "run boot_rd_img;bootm"
fw_setenv flag_boot_rootfs 0
fw_setenv flag_last_success 1
fw_setenv flag_boot_success 1
fw_setenv flag_try_sys1_failed 0
```

#### 13. Check that the flags from step 12 in `fw_printenv` are set correctly by entering the following command:

```
fw_printenv
```

If everything looks fine, reboot the router by entering `reboot`.

* *note: if bootloop occurs with the red message `"*** UBI is read-only mode ***"` after router is reloaded, the value of `flag_last_success` should be [changed](https://forum.openwrt.org/t/openwrt-support-for-xiaomi-ax3000t/180490/1250?u=alexq). To fix it, go through steps 5 to 13 again, but  in step 12 instead of `fw_setenv flag_last_success 1` use `fw_setenv flag_last_success 0`*.

#### 14. Once OpenWrt is reloaded, connect an internet cable to the [WAN port](https://openwrt.org/_media/media/xiaomi/ax3000t_ports.png) of the router.
**Congratulations, your router is now running under OpenWrt firmware!**

<details>
<summary>this hidden part is no longer required.</summary>

*note: configuration reset issue after 6 reboots, [caused](https://forum.openwrt.org/t/openwrt-support-for-xiaomi-ax3000t/180490/452?u=alexq) by the stock Xiaomi bootloader logic, has been [addressed](https://github.com/openwrt/openwrt/pull/17580) since the OpenWrt 24.10.0 release.*

**So, this hidden part is no longer required:**

* Enter one more command in PuTTY that will modify the `/etc/rc.local` file with nvram settings ensuring that the `flag_try_sys1_failed` is always reset to 0 (*without this command, OpenWrt will be restored to default settings after [6 reboots](https://forum.openwrt.org/t/openwrt-support-for-xiaomi-ax3000t/180490/452?u=alexq), so it's worth adding it even after future clean OpenWrt sysupgrade installations*).

```
sed -i '/exit 0/i sleep 5\nfw_setenv flag_try_sys1_failed 0' /etc/rc.local
```

</details>

#### 15. Optional step (only if Snapshot of OpenWrt is installed).

<dl>
<dd>

**enable wi-fi and install LuCI**  

```
uci set wireless.radio1.country='DE'
uci set wireless.radio1.disabled='0'
uci commit wireless
wifi reload
```

install LuCI from console:

```
apk update && apk add luci
```

Now, you can open the OpenWrt web interface of the router by entering 192.168.1.1 in the browser's address bar.  
<img width="620" height="358" alt="image" src="https://github.com/user-attachments/assets/df759076-ba34-4851-95be-4ccc16bc5389" />
<img width="690" height="486" alt="image" src="https://github.com/user-attachments/assets/db0ed235-795f-4fd3-b643-da9df1843709" />

</dd>
</dl>

**Additional information**:

* *Original Xiaomi firmware can be restored on a router with OpenWrt installed. To do this on the international version of the AX3000T RD23 model, use the official stock Xiaomi firmware ver. [1.0.49 (INT)](https://cdn.cnbj1.fds.api.mi-img.com/xiaoqiang/rom/rd23/miwifi_rd23_firmware_153e1_1.0.49_INT.bin) or [1.0.76 (INT)](https://cdn.cnbj1.fds.api.mi-img.com/xiaoqiang/rom/rd23/miwifi_rd23_firmware_ae9e2_1.0.76_INT.bin) and either the [TFTP recovery method](https://openwrt.org/toh/xiaomi/ax3000t#tftp_instructions_for_the_stock_bootloader) or the Xiaomi [MiWiFi Repair Tool](http://bigota.miwifi.com/xiaoqiang/tools/MIWIFIRepairTool.x86.zip). Simple instructions with screenshots from another Xiaomi router can be found [here](https://forum.openwrt.org/t/xiaomi-mi-router-4a-gigabit-edition-r4ag-r4a-gigabit-fully-supported-and-flashable-with-openwrtinvasion/36685/747?u=alexq).*
* *It's possible to avoid opening the case to access UART pins, as they are [accessible through holes](https://forum.openwrt.org/t/openwrt-support-for-xiaomi-ax3000t/180490/709?u=alexq) in the router's case. For example, see the following [connection option](https://forum.openwrt.org/t/openwrt-support-for-xiaomi-ax3000t/180490/1543?u=alexq).*
* *Changing default bootloader to the OpenWrt U-Boot bootloader has not been covered in this guide, but there is a [report](https://forum.openwrt.org/t/openwrt-support-for-xiaomi-ax3000t/180490/592?u=alexq) about a successful OpenWrt U-Boot installation on the RD23. Please [understand](https://forum.openwrt.org/t/openwrt-support-for-xiaomi-ax3000t/180490/721?u=alexq) the benefits and risks involved, especially the fact that the [TFTP recovery method](https://openwrt.org/inbox/toh/xiaomi/ax3000t#tftp_instructions_for_the_stock_bootloader) or Xiaomi [MiWiFi Repair Tool](http://bigota.miwifi.com/xiaoqiang/tools/MIWIFIRepairTool.x86.zip) cannot be used when a custom bootloader is installed.*
* *The `"software list"` in the instruction above references [Snapshot](https://downloads.openwrt.org/snapshots/targets/mediatek/filogic/) OpenWrt images. However, the stable ~~24.10~~ [25.12 release](https://downloads.openwrt.org/releases/25.12.5/targets/mediatek/filogic/) is already available and can be used instead.*
* :exclamation: *The UART flash method described in this guide is also suitable for the AX3000T RD03 model (Chinese version)*.
* *You can also find my original version of this guide on the OpenWrt forum: https://forum.openwrt.org/t/openwrt-support-for-xiaomi-ax3000t/180490/420*

<sub>Licensed under <a href="https://creativecommons.org/licenses/by/4.0/">CC BY 4.0</a>.</sub>
