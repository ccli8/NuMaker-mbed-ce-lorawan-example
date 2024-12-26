![](./resources/official_armmbed_example_badge.png)
# Example for LoRaWAN on Nuvoton's Mbed CE enabled boards

This is an example application based on `Mbed-OS` LoRaWAN protocol APIs.
The Mbed-OS LoRaWAN stack implementation is compliant with LoRaWAN v1.0.2 specification.
See this [link](https://os.mbed.com/blog/entry/Introducing-LoRaWAN-11-support/) for information on support for other LoRaWAN spec versions. This application can work with any Network Server if you have correct credentials for the said Network Server.

Check out [Mbed CE](https://github.com/mbed-ce)
for details on Mbed OS community edition.

## Getting Started

### Supported Hardware
[Mbed Enabled board with an Arduino form factor](https://os.mbed.com/platforms/?q=&Form+Factor=Arduino+Compatible) and one of the following:
- [SX126X shield](https://os.mbed.com/components/SX126xMB2xAS/)
- [SX1276 shield](https://os.mbed.com/components/SX1276MB1xAS/)
- [SX1272 shield](https://os.mbed.com/components/SX1272MB2xAS/) 

OR

[Mbed Enabled LoRa Module](#module-support)

## Support development tools

Use cmake-based build system.
Check out [hello world example](https://github.com/mbed-ce/mbed-ce-hello-world) for getting started.

> **⚠️ Warning**
>
> Legacy development tools below are not supported anymore.
> - [Arm's Mbed Studio](https://os.mbed.com/docs/mbed-os/v6.15/build-tools/mbed-studio.html)
> - [Arm's Mbed CLI 2](https://os.mbed.com/docs/mbed-os/v6.15/build-tools/mbed-cli-2.html)
> - [Arm's Mbed CLI 1](https://os.mbed.com/docs/mbed-os/v6.15/tools/developing-mbed-cli.html)

For [VS Code development](https://github.com/mbed-ce/mbed-os/wiki/Project-Setup:-VS-Code)
or [OpenOCD as upload method](https://github.com/mbed-ce/mbed-os/wiki/Upload-Methods#openocd),
install below additionally:

-   [NuEclipse](https://github.com/OpenNuvoton/Nuvoton_Tools#numicro-software-development-tools): Nuvoton's fork of Eclipse
-   Nuvoton forked OpenOCD: Shipped with NuEclipse installation package above.
    Checking openocd version `openocd --version`, it should fix to `0.10.022`.


### Example configuration and radio selection

Because of the pin differences between the SX126x and SX127x radios, example application configuration files are provided with the correct pin sets in the `config/` dir of this project. 

Please start by selecting the correct example configuration for your radio:  
- For [Mbed Online Compiler](https://ide.mbed.com/compiler/) users, this can be done by simply replacing the contents of the `mbed_app.json5` at the root of the project with the content of the correct example configuration in `config/` dir.
- For [mbed-cli](https://github.com/ARMmbed/mbed-cli) users, the config file can be specifed on the command line with the `--app-config` option (ie `--app-config config/SX12xx_example_config.json`)

With the correct config file selected, the user can then provide a pin set for their target board in the `NC` fields at the top if it is different from the default targets listed.  If your device is one of the LoRa modules supported by Mbed-OS, the pin set is already provided for the modules in the `target-overrides` field of the config file. For more information on supported modules, please refer to the [module support section](#module-support)

### Add network credentials

Open the file `mbed_app.json5` in the root directory of your application. This file contains all the user specific configurations your application and the Mbed OS LoRaWAN stack need. Network credentials are typically provided by LoRa network provider.

#### For OTAA

Please add `Device EUI`, `Application EUI` and `Application Key` needed for Over-the-air-activation(OTAA). For example:

```json5
"lora.device-eui": "{ YOUR_DEVICE_EUI }",
"lora.application-eui": "{ YOUR_APPLICATION_EUI }",
"lora.application-key": "{ YOUR_APPLICATION_KEY }"
```

#### For ABP

For Activation-By-Personalization (ABP) connection method, modify the `mbed_app.json5` to enable ABP. You can do it by simply turning off OTAA. For example:

```json5
"lora.over-the-air-activation": false,
```

In addition to that, you need to provide `Application Session Key`, `Network Session Key` and `Device Address`. For example:

```json5
"lora.appskey": "{ YOUR_APPLICATION_SESSION_KEY }",
"lora.nwkskey": "{ YOUR_NETWORK_SESSION_KEY }",
"lora.device-address": " YOUR_DEVICE_ADDRESS_IN_HEX  " 
```

## Configuring the application

The Mbed OS LoRaWAN stack provides a lot of configuration controls to the application through the Mbed OS configuration system. The previous section discusses some of these controls. This section highlights some useful features that you can configure.

### Selecting a PHY

The LoRaWAN protocol is subject to various country specific regulations concerning radio emissions. That's why the Mbed OS LoRaWAN stack provides a `LoRaPHY` class that you can use to implement any region specific PHY layer. Currently, the Mbed OS LoRaWAN stack provides 10 different country specific implementations of `LoRaPHY` class. Selection of a specific PHY layer happens at compile time. By default, the Mbed OS LoRaWAN stack uses `EU 868 MHz` PHY. An example of selecting a PHY can be:

```josn
        "phy": {
            "help": "LoRa PHY region. 0 = EU868 (default), 1 = AS923, 2 = AU915, 3 = CN470, 4 = CN779, 5 = EU433, 6 = IN865, 7 = KR920, 8 = US915, 9 = US915_HYBRID",
            "value": "0"
        },
```

### Duty cycling

LoRaWAN v1.0.2 specifcation is exclusively duty cycle based. This application comes with duty cycle enabled by default. In other words, the Mbed OS LoRaWAN stack enforces duty cycle. The stack keeps track of transmissions on the channels in use and schedules transmissions on channels that become available in the shortest time possible. We recommend you keep duty cycle on for compliance with your country specific regulations. 

However, you can define a timer value in the application, which you can use to perform a periodic uplink when the duty cycle is turned off. Such a setup should be used only for testing or with a large enough timer value. For example:

```josn 
"target_overrides": {
	"*": {
		"lora.duty-cycle-on": false
		},
	}
}
```

## Module support

Here is a nonexhaustive list of boards and modules that we have tested with the Mbed OS LoRaWAN stack:

- MultiTech mDot (SX1272)
- MultiTech xDot (SX1272)
- LTEK_FF1705 (SX1272)
- Advantech Wise 1510 (SX1276)
- ST B-L072Z-LRWAN1 LoRa®Discovery kit with Murata CMWX1ZZABZ-091 module (SX1276)

Here is a list of boards and modules that have been tested by the community:

- IMST iM880B (SX1272)
- Embedded Planet Agora (SX1276)

## Developer guide

In the following, we take **NuMaker-IoT-M252** board as an example for Mbed CE support.

### Hardware requirements

-   NuMaker-IoT-M252 board
-   Host OS: Windows or others

### Build the example

1.  Clone the example and navigate into it
    ```
    $ git clone https://github.com/mbed-nuvoton/NuMaker-mbed-ce-lorawan-example
    $ cd NuMaker-mbed-ce-lorawan-example
    $ git checkout -f master
    ```

1.  Deploy necessary libraries
    ```
    $ git submodule update --init
    ```
    Or for fast install:
    ```
    $ git submodule update --init --filter=blob:none
    ```

1.  Compile with cmake/ninja
    ```
    $ mkdir build; cd build
    $ cmake .. -GNinja -DCMAKE_BUILD_TYPE=Develop -DMBED_TARGET=NUMAKER_IOT_M252
    $ ninja
    $ cd ..
    ```

### Flash the image

Flash by drag-n-drop built image `NuMaker-mbed-ce-lorawan-example.bin` or `NuMaker-mbed-ce-lorawan-example.hex` onto **NuMaker-IoT-M252** board

### Verify the result

On host terminal (115200/8-N-1), you should see an output similar to this:

```
Mbed LoRaWANStack initialized 

 CONFIRMED message retries : 3 

 Adaptive data  rate (ADR) - Enabled 

 Connection - In Progress ...

 Connection - Successful 

 Dummy Sensor Value = 2.1 

 25 bytes scheduled for transmission 
 
 Message Sent to Network Server

```

## [Optional] Adding trace library
To enable Mbed trace, add to your `mbed_app.json5` the following fields:

```json5
    "target_overrides": {
        "*": {
            "mbed-trace.enable": true
            }
     }
```
The trace is disabled by default to save RAM and reduce main stack usage (see chapter Memory optimization).

**Please note that some targets with small RAM size (e.g. DISCO_L072CZ_LRWAN1 and MTB_MURATA_ABZ) mbed traces cannot be enabled without increasing the default** `"main_stack_size": 1024`**.**

## [Optional] Memory optimization 

Using `Arm CC compiler` instead of `GCC` reduces `3K` of RAM. Currently the application takes about `15K` of static RAM with Arm CC, which spills over for the platforms with `20K` of RAM because you need to leave space, about `5K`, for dynamic allocation. So if you reduce the application stack size, you can barely fit into the 20K platforms.

For example, add the following into `config` section in your `mbed_app.json5`:

```
"main_stack_size": {
    "value": 2048
}
```

Essentially you can make the whole application with Mbed LoRaWAN stack in 6K if you drop the RTOS from Mbed OS and use a smaller standard C/C++ library like new-lib-nano. Please find instructions [here](https://os.mbed.com/blog/entry/Reducing-memory-usage-with-a-custom-prin/).
 

For more information, please follow this [blog post](https://os.mbed.com/blog/entry/Reducing-memory-usage-by-tuning-RTOS-con/).


### License and contributions

The software is provided under Apache-2.0 license. Contributions to this project are accepted under the same license. Please see [contributing.md](CONTRIBUTING.md) for more info.

This project contains code from other projects. The original license text is included in those source files. They must comply with our license guide.

