# gpiomem driver TinkerBoard

To compile the driver, the source code need to be integrated to the kernel source into the folder:

```
drivers/char
```
Next this patches need to be implemented:
```
diff --git a/drivers/char/Makefile b/drivers/char/Makefile
index e0047ed..54e0608 100644
--- a/drivers/char/Makefile
+++ b/drivers/char/Makefile
@@ -63,3 +63,6 @@ obj-$(CONFIG_JS_RTC)          += js-rtc.o
 js-rtc-y = rtc.o
 
 obj-$(CONFIG_TILE_SROM)                += tile-srom.o
+
+obj-$(CONFIG_ARCH_ROCKCHIP) += rk3288-gpiomem.o

diff --git a/Documentation/devicetree/bindings/vendor-prefixes.txt b/Documentation/devicetree/bindings/vendor-prefixes.txt
index 548c794..e958ebe 100644
--- a/Documentation/devicetree/bindings/vendor-prefixes.txt
+++ b/Documentation/devicetree/bindings/vendor-prefixes.txt
@@ -65,3 +65,4 @@ wlf   Wolfson Microelectronics
 wm     Wondermedia Technologies, Inc.
 winbond Winbond Electronics corp.
 xlnx   Xilinx
+rk3288-gpiomem

diff --git a/arch/arm/boot/dts/rk3288.dtsi b/arch/arm/boot/dts/rk3288.dtsi
index f148577..a519a4a 100755
--- a/arch/arm/boot/dts/rk3288.dtsi
+++ b/arch/arm/boot/dts/rk3288.dtsi
@@ -86,6 +86,12 @@
                             <GIC_SPI 154 IRQ_TYPE_LEVEL_HIGH>;
        };
 
+       rk3288-gpiomem {
+               compatible = "rockchip,rk3288-gpiomem";
+               reg = <0xff750000 0x1000>;
+               status = "okay";
+       };
+
        cpu_axi_bus: cpu_axi_bus {
                compatible = "rockchip,cpu_axi_bus";
                #address-cells = <1>;
```

<br>
To compile the Android kernel you need a toolchain, I used the toolchain(x86_64) from radxa.com: http://dl.radxa.com/rock/source/x86_64_arm-eabi-4.6.zip
<br>
Clone or download the kernel source from https://github.com/TinkerBoard/android_kernel.
```
cd android_kernel
export ARCH=arm
export CROSS_COMPILE=/path/to/your/toolchain/arm-eabi-4.6/bin/arm-eabi-
make rockchip_defconfig
make rk3288-miniarm.img

```
After successful compilation, you will get kernel.img and resource.img that you'll need to flash to the TinkerBoard.
```
#backup original kernel and resource
sudo ./rkflashtool r kernel >kernel-org.img

#flash new kernel and resource
sudo ./rkflashtool w resource <resource.img
sudo ./kflashtool w kernel <kernel.img

#reboot the TinkerBoard
sudo ./kflashtool b
```
You'll find the rkflashtool here: https://github.com/neo-technologies/rkflashtool

