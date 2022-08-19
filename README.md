# Radxa Zero DT Overlays
Some device tree overlays for Radxa Zero based on my own personal needs. 

## meson-g12a-gpio-shutdown.dts
This sets up the GPIOAO_4 pin (pin #32 in the 40-pin connector) to trigger a KEY_POWER event when shorted to ground.

This needs CONFIG_KEYBOARD_GPIO_POLLED to be either built into the kernel or compiled as a module.

## meson-g12a-gpio-poweroff.dts
This implements the [gpio-poweroff](https://www.kernel.org/doc/Documentation/devicetree/bindings/power/reset/gpio-poweroff.txt) driver on the GPIOAO_9 pin (pin #37 in the 40-pin connector). This pin will by high while the system is running, but when the system is shut down, the pin will go low momentarily once the system is safe to power off. This enables the use of "safe shutdown" circuits to turn off an external power supply.

For this to work, you also need to patch the kernel by editing /drivers/firmware/psci/psci.c and deleting or commenting out the line `pm_power_off = psci_sys_poweroff;` This is hacky and not an ideal implementation, but it works for me and I haven't spent the time yet figuring out a better way to make this work without declaring competing pm_power_off handlers.

This also depends on a kernel that has with CONFIG_POWER_RESET_GPIO either built in or compiled as a module.

## meson-g12a-pwm-backlight.dts
This sets up pin GPIOAO_11 (pin #40 in the 40-pin connector) as a PWM pin that is recognized by the kernel as a backlight, complete with an entry in /sys/class/backlight to allow system control of the backlight intensity. I use this to control the backlight on an external HDMI display that has a backlight driven by a PT4103 boost converter. I lifted the 4103's "EN" pin from the PCB and soldered it to the PWM pin. This driver uses a PWM frequency of 100kHz.

This depends on the [official meson-g12a-pwmao-a-on-gpioao-11.dts overlay](https://github.com/radxa/kernel/blob/linux-5.10.y-radxa-zero/arch/arm64/boot/dts/amlogic/overlay/meson-g12a-pwmao-a-on-gpioao-11.dts) from Radxa. 
