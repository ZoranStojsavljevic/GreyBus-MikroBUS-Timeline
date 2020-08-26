## Greybus Simulator (gbsim)

### Clickboard support under the Grey Bus

https://github.com/vaishnav98/gbsim (forked from projectara/gbsim)

https://github.com/vaishnav98/gbsim/wiki/Beagleboard-GSoC-'19:--Clickboard-Support-Under-Greybus

#### Adding Support for Simple I2C Click

Adding support for simple I2C clicks which has no interrupts or other platform data requirements
requires only the addition of the corresponding entry under clicks.json . An example entry for the
RTC 6 Click based on the MCP79410 I2C RTC is shown below:

	"rtc6": {
		"type": "i2c",
		"class": "ordinary",
		"driver": "mcp7941x",
		"address": "0x6f",
		"info": [
			"RTC 6 Click",
			"Use Command : sudo hwclock -r --rtc /dev/rtc1 to display the RTC Date and Time",
			"use cat /sys/class/rtc/rtc1/date  to get the RTC Date",
			"use cat /sys/class/rtc/rtc1/time  to get the RTC Time",
			"use cat /sys/class/rtc/rtc1/since_epoch  to get the number of seconds that have elapsed since January 1, 1970 (midnight UTC/GMT)"
		]
	}

Here the key for the entry is the name of the click rtc6 , this name will be available with the
insclick/rmclick commands.The other entries are:

	- type : corresponds to the type of bus the click uses (currently SPI/I2C)
	- class : corresponds to whether the click requires additional platform data or not , in this
		case class->ordinary , the available options are : oridnary/platform/display
	- driver : corresponds to the name of linux driver available for the click device
	- address : corresponds to the I2C address of the device
	- info : this section contains the helping information for the user to get started with the
		Click Board , the helper string array can have any length and each array element
		will be displayed in new line when invoking the insclick command

#### Adding Support for Simple SPI Click

Adding support for simple SPI clicks which has no interrupts or other platform data requirements
requires only the addition of the corresponding entry under clicks.json and creation of a greybus
manifest file for the device. An example entry for the MicroSD Click is shown below:

	"microSD": {
		"type": "spi",
		"class": "ordinary",
		"info": [
			"microSD Click",
			"usage information"
		]
	}

Here the key for the entry is the name of the click microSD , this name will be available with the
insclick/rmclick commands.The other entries are:

	- type : corresponds to the type of bus the click uses (currently SPI/I2C)
	- class : corresponds to whether the click requires additional platform data or not , in this
		case class->ordinary , the available options are : oridnary/platform/display
	- info : this section contains the helping information for the user to get started with the
		Click Board , the helper string array can have any length and each array element will
		be displayed in new line when invoking the insclick command

The example manifest for a microSD click is as shown below

	;
	; Simple SPI Interface Manifest
	;
	; Copyright 2015 Google Inc.
	; Copyright 2015 Linaro Ltd.
	;
	; Provided under the three clause BSD license found in the LICENSE file.
	;

	[manifest-header]
	version-major = 0
	version-minor = 1

	[interface-descriptor]
	vendor-string-id = 1
	product-string-id = 2

	; Interface vendor string (id can't be 0)
	[string-descriptor 1]
	string = MikroElektronika
	; Driver Name string (id can't be 0)
	[string-descriptor 2]
	string = mmc_spi

	; Control cport and bundle are optional.
	; - Control cport's id must be 0 and its bundle number must be 0.
	; - No other bundle or control cport may use these values.
	; - Class and protocol of bundle and cport must be marked as 0x00.
	;
	;Control protocol on CPort 0
	[cport-descriptor 0]
	bundle = 0
	protocol = 0x00

	;Control protocol Bundle 0
	[bundle-descriptor 0]
	class = 0

	; SPI protocol on CPort 1
	[cport-descriptor 1]
	bundle = 1
	protocol = 0x0b

	; Bundle 1
	[bundle-descriptor 1]
	class = 0x0a

For adding support for new SPI clicks without additional platform data , a new manifest needs to be
made from the above template where the string-decriptor 2 property should match the device driver
corresponding to the clickboard (here mmc_spi corresponds to the driver for the microSD click), the
newly created manifests should be saved under the manifests/ directory where the name of the manifest
file should be same as the click name.To know more about greybus manifests see : Greybus Manifests
_______

[1] Does Grey Bus use .json format as the primary description manifest?
[2] What about the INT representation in .json?
[3] Does the transformation of .json to .mnfs solve all the GPIO interface definition problems?

Proposal: in MikroBUS driver to introduce instead of a linked list INDEX table, with each entry of
sizeof 64B, with Click IDs representing INDEXes into the table shifted for 6 positions left (<< 6).

The total entries should be 4096 (4K entries), thus the size of the table is 4K x 64 -> 256KB.

First 128 or 256 entries to be system wise reserved for the Future Use.
________

The architecture of the whole system:

The manifest python3 parser passes all the .mnfs files where the each click is described,
translating .mnfs to .mnfb .

While MikroBUS Driver (MBD) parses .mnfb stored in flash, finding Click IDs used, then MBD
converts appropriate .mnfb to dynamic overlay file, immediately dynamicly binding appropriate
kernel driver to the click HW (HW platform's extension) itself.
_______

The Grey Bus Manifest with all defined constants:

https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/tree/include/linux/greybus/greybus_manifest.h

The Driver version 2 corresponds to MikroBUS manifesto version 3.

	Sorry everyone for the version number mismatch between manifesto and the patch, now
	the patch v2 maps to manifesto:mikrobusv3 branch this  happened due to a in-between
	version for manifest(modified version of mikrobus descriptor) which wasn't sent to
	the list.

	This is the Pull Request to projectara/manifesto which maintains the latest version
	of manifesto : https://github.com/projectara/manifesto/pull/2
