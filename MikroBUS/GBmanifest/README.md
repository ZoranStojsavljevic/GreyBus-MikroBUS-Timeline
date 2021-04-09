## .../include/linux/greybus/greybus_manifest.h

In the present directory:

	[vuser@fedora32-ssd GBmanifest]$ ls -al
	total 32
	drwxr-xr-x. 2 vuser vboxusers 4096 Aug 26 14:30 .
	drwxr-xr-x. 3 vuser vboxusers 4096 Aug 26 05:48 ..
	-rw-r--r--. 1 vuser vboxusers 6073 Aug 26 08:02 greybus_manifest.h
	-rw-r--r--. 1 vuser vboxusers 4904 Aug 26 05:50 greybus_manifest.h.orig
	-rw-r--r--. 1 vuser vboxusers 2293 Aug 26 13:21 patchfile.patch
	-rw-r--r--. 1 vuser vboxusers   77 Aug 26 14:34 README.md
	[vuser@fedora32-ssd GBmanifest]$

There is a patchfile.patch patch, which, applied to the greybus_manifest.h.orig
(taken from the latest linux tree git master), gives the proposed greybus_manifest.h .

This file actually defines all the fields in the .mnfs manifesto (version 3):

https://github.com/vaishnav98/manifesto/tree/mikrobusv3

### Click ENC28J60

The click to be tested with the manifesto version 3 is the following click: enc28j60

https://github.com/vaishnav98/manifesto/blob/mikrobusv3/manifests/ETH-CLICK.mnfs

#### ETH-CLICK.mnfs

	;
	; ETH CLICK
	; https://www.mikroe.com/eth-click
	; CONFIG_ENC28J60
	;
	; Copyright 2020 BeagleBoard.org Foundation
	; Copyright 2020 Texas Instruments
	;

	[manifest-header]
	version-major = 0
	version-minor = 1

	[interface-descriptor]
	vendor-string-id = 1
	product-string-id = 2

	[string-descriptor 1]
	string = MikroElektronika

	[string-descriptor 2]
	string = ETH Click

	[mikrobus-descriptor]
	pwm-state = 4
	int-state = 1
	rx-state = 7
	tx-state = 7
	scl-state = 6
	sda-state = 6
	mosi-state = 5
	miso-state = 5
	sck-state = 5
	cs-state = 5
	rst-state = 2
	an-state = 1

	[bundle-descriptor 1]
	class = 0xa

	[cport-descriptor 1]
	bundle = 1
	protocol = 0x2

	[cport-descriptor 2]
	bundle = 1
	protocol = 0xb

	[device-descriptor 1]
	driver-string-id = 3
	protocol = 0x0b
	mode = 0x0
	reg = 0
	max-speed-hz = 16000000
	irq = 1
	irq-type = 0x2

	[string-descriptor 3]
	string = enc28j60

The full description of the fields are established between:

	Updated with MikroBUS defn. greybus_manifest.h <-> ETH-CLICK.mnfs

Compiled with the command:

	./manifesto -i manifests/ETH-CLICK.mnfs -o manifests/ETH-CLICK.mnfb

#### HEX Editor View (manifesto mikrobusv3 ETH-CLICK.mnfb)

		00 01 02 03 04 05 06 07 08 09 0A 0B 0C 0D 0E 0F	0123456789ABCDEF

	0000	80 00 00 01 08 00 01 00 01 02 00 00 18 00 02 00 €...............
	0010	10 01 4D 69 6B 72 6F 45 6C 65 6B 74 72 6F 6E 69 ..MikroElektroni
	0020	6B 61 00 00 10 00 02 00 09 02 45 54 48 20 43 6C ka........ETH Cl
	0030	69 63 6B 00 10 00 05 00 04 01 07 07 06 06 05 05 ick.............
	0040	05 05 02 01 08 00 03 00 01 0A 00 00 08 00 04 00 ................
	0050	01 00 01 02 08 00 04 00 02 00 01 0B 14 00 07 00 ................
	0060	01 03 0B 00 00 24 F4 00 01 02 00 00 00 00 00 00 ................
	0070	10 00 02 00 08 03 65 6E 63 32 38 6A 36 30 00 00 ......enc28j60..

#### .../include/linux/greybus/greybus_manifest.h HEX interpretation

	struct greybus_manifest {
		struct greybus_manifest_header		header;
		struct greybus_descriptor		descriptors[0];
	} __packed;

	struct greybus_manifest_header {
					address 0x0000: header	[80 00 00 01]
		__le16	size;		==>> 80 00
		__u8	version_major;	==>> 00
		__u8	version_minor;	==>> 01
	} __packed;

	struct greybus_descriptor {
		struct greybus_descriptor_header		header;
		union {
			struct greybus_descriptor_string	string;
			struct greybus_descriptor_interface	interface;
			struct greybus_descriptor_bundle	bundle;
			struct greybus_descriptor_cport		cport;
			struct greybus_descriptor_mikrobus	mikrobus;
			struct greybus_descriptor_property	property;
			struct greybus_descriptor_device	device;
		};
	} __packed;

	/*
	 * The device descriptor is used to describe a device on the
	 * mikrobus port and has necessary fields from `struct i2c_board_info`
	 * and `struct spi_board_info` to describe a device on these buses
	 * in a mikrobus port, even though SPI/I2C device info structs are used
	 * this descriptor has enough information to describe other kinds of
	 * devices relevant to mikrobus as well.(serdev/platform devices).
	 * The device descriptor is a fixed-length descriptor and there can be
	 * multiple instances of device descriptors in an add-on board manifest
	 * in cases where the add-on board presents more than one device to the
	 * host.
	 */

	struct greybus_descriptor_interface {
					address 0x0004: header	[08 00 01 00]
					address 0x0008:	data	[01 02 00 00]
		__u8 vendor_stringid;	==>> 01
		__u8 product_stringid;	==>> 02
		__u8 features;		==>> 00
		__u8 pad;		==>> 00
	} __packed;

	struct greybus_descriptor_string (1) {
					address 0x000C: header	[18 00 02 00]
					address 0x0010: data	[10 01 4D 69 6B 72 6F 45 6C 65 6B 74 72 6F 6E 69 6B 61 00 00]
		__u8	length;		==>> 10
		__u8	id;		==>> 01
		__u8	string[0];	==>> 4D 69 6B 72 6F 45 6C 65 6B 74 72 6F 6E 69 6B 61 00 00
	} __packed;

	struct greybus_descriptor_string (2) {
					address 0x0024: header	[10 00 02 00]
					address 0x0028: data	[09 02 45 54 48 20 43 6C 69 63 6B 00]
		__u8	length;		==>> 09
		__u8	id;		==>> 02
		__u8	string[0];	==>> 45 54 48 20 43 6C 69 63 6B 00
	} __packed;

	/*
	 * The mikrobus descriptor is used to pass information about
	 * the specific pinmux settings and the default GPIO states on
	 * the mikrobus port to be set up for the add-on board to work
	 * correctly, this descriptor has 12 u8 fields(corresponding to
	 * the 12 pins on the mikrobus port) which includes information
	 * about the prior setup required on the mikroBUS port for the
	 * device(s) on the add-on board to work correctly. The mikrobus
	 * descriptor is a fixed-length descriptor and there will be only a
	 * single instance of mikrobus descriptor per add-on board manifest.
	 */

	struct greybus_descriptor_mikrobus {
					address 0x0034: header	[10 00 05 00]
					address 0x0038; data	[04 01 07 07 06 06 05 05 05 05 02 01]
		__u8 pin_state[12];	==>> 04 01 07 07 06 06 05 05 05 05 02 01
	} __packed;

	struct greybus_descriptor_bundle {
					address 0x0044: header	[08 00 03 00]
					address 0x0048: data:	[01 0A 00 00]
		__u8	id;		==>> 01
		__u8	class;		==>> 0A
		__u8	pad[2];		==>> 00 00
	} __packed;

	struct greybus_descriptor_cport (1) {
					address 0x004C: header	[08 00 04 00]
					address 0x0050: data	[01 00 01 02]
		__le16	id;		==>> 01 00
		__u8	bundle;		==>> 01
		__u8	protocol_id;	==>> 02
	} __packed;

	struct greybus_descriptor_cport (2) {
					address 0x0054: header	[08 00 04 00]
					address 0x0058: data	[02 00 01 0B]
		__le16	id;		==>> 02 00
		__u8	bundle;		==>> 01
		__u8	protocol_id;	==>> 0B
	} __packed;

	struct greybus_descriptor_device {
					address 0x005C: header  [14 00 07 00]
					address 0x0060: data	[01 03 0B 00 00 24 F4 00 01 02 00 00 00 00 00 00]
		__u8 id;		==>> 01
		__u8 driver_stringid;	==>> 03
		__u8 protocol;		==>> 0B		/* GREYBUS_PROTOCOL_SPI */
		__u8 reg;		==>> 00
		__le32 max_speed_hz;	==>> 00 24 F4 00 /* 16000000 dec. */
		__u8 irq;		==>> 01
		__u8 irq_type;		==>> 02
		__u8 mode;		==>> 00
		__u8 prop_link;		==>> 00
		__u8 gpio_link;		==>> 00
		__u8 pad[3];		==>> 00 00 00
	} __packed;

	struct greybus_descriptor_string (3) {
					address 0x0070: header	[10 00 02 00]
					address 0x0074: data	[08 03 65 6E 63 32 38 6A 36 30 00 00]
		__u8	length;		==>> 08
		__u8	id;		==>> 03
		__u8	string[0];	==>> 65 6E 63 32 38 6A 36 30 00 00
	} __packed;

### Structure greybus_manifest mapping

		00 01 02 03 04 05 06 07 08 09 0A 0B 0C 0D 0E 0F	0123456789ABCDEF

	0000	80 00 00 01 08 00 01 00 01 02 00 00 18 00 02 00 €...............
	0010	10 01 4D 69 6B 72 6F 45 6C 65 6B 74 72 6F 6E 69 ..MikroElektroni
	0020	6B 61 00 00 10 00 02 00 09 02 45 54 48 20 43 6C ka........ETH Cl
	0030	69 63 6B 00 10 00 05 00 04 01 07 07 06 06 05 05 ick.............
	0040	05 05 02 01 08 00 03 00 01 0A 00 00 08 00 04 00 ................
	0050	01 00 01 02 08 00 04 00 02 00 01 0B 14 00 07 00 ................
	0060    01 03 0B 00 00 24 F4 00 01 02 00 00 00 00 00 00 ................
	0070	10 00 02 00 08 03 65 6E 63 32 38 6A 36 30 00 00 ......enc28j60..

	struct greybus_manifest_header		0x0000 [ header	80 00 00 01 ]
	struct greybus_descriptor_interface	0x0004 [ header	08 00 01 00 ]
						0x0008 [ data	01 02 00 00 ]
	struct greybus_descriptor_string (1)	0x000C [ header	18 00 02 00 ]
						0x0010 [ data	10 01 4D 69 6B 72 6F 45 6C 65 6B 74 72 6F 6E 69 6B 61 00 00 ]
	struct greybus_descriptor_string (2)	0x0024 [ header	10 00 02 00 ]
						0x0028 [ data	09 02 45 54 48 20 43 6C 69 63 6B 00 ]
	struct greybus_descriptor_mikrobus	0x0034 [ header	10 00 05 00 ]
						0x0038 [ data	04 01 07 07 06 06 05 05 05 05 02 01 ]
	struct greybus_descriptor_bundle	0x0044 [ header	08 00 03 00 ]
						0x0048 [ data	01 0A 00 00 ]
	struct greybus_descriptor_cport (1)	0x004C [ header	08 00 04 00 ]
						0x0050 [ data	01 00 01 02 ]
	struct greybus_descriptor_cport (2)	0x0054 [ header	08 00 04 00 ]
						0x0058 [ data	02 00 01 0B ]
	struct greybus_descriptor_device	0x005C [ header	14 00 07 00 ]
						0x0060 [ data	01 03 0B 00 24 F4 00 00 01 02 00 00 00 00 00 00 ]
	struct greybus_descriptor_string (3)	0x0070 [ header	10 00 02 00 ]
						0x0074 [ data	08 03 65 6E 63 32 38 6A 36 30 00 00 ]
