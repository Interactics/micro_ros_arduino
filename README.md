# micro-ROS for Arduino

This is a micro-ROS library for baremetal projects based on Arduino IDE or Arduino CLI.

As the build process for ROS 2 and micro-ROS is based on custom meta-build system tools and CMake, this library is provided as a precompiled library. Anyways, users can rebuild its own precompiled libraries in order to modify micro-ROS parameters, for example customizing prebuild meta file.

- [micro-ROS for Arduino](#micro-ros-for-arduino)
  - [Supported boards](#supported-boards)
  - [How to use the precompiled library](#how-to-use-the-precompiled-library)
    - [Arduino IDE](#arduino-ide)
    - [PlatformIO](#platformio)
  - [How to build the precompiled library](#how-to-build-the-precompiled-library)
  - [Patch Arduino board for support precompiled libraries](#patch-arduino-board-for-support-precompiled-libraries)
    - [Patch Teensyduino](#patch-teensyduino)
    - [Patch SAMD](#patch-samd)
  - [Purpose of the Project](#purpose-of-the-project)
  - [License](#license)
  - [Known Issues/Limitations](#known-issueslimitations)

## Supported boards

Supported boards are:

| Board                                                                               | Min version | State      | Details                                                                                             | Prebuild meta            |
| ----------------------------------------------------------------------------------- | ----------- | ---------- | --------------------------------------------------------------------------------------------------- | ------------------------ |
| [Arduino Portenta H7 M7 Core](https://store.arduino.cc/portenta-h7)                 | v1.8.5      | Supported  | Official Arduino support                                                                            | `colcon.meta`            |
| [Arduino Nano RP2040 Connect](https://docs.arduino.cc/hardware/nano-rp2040-connect) | v1.8.5      | Supported  | Official Arduino support                                                                            | `colcon_verylowmem.meta` |
| [OpenCR](https://emanual.robotis.com/docs/en/parts/controller/opencr10/)            | v1.4.16     | Supported  | [Based on custom board](https://emanual.robotis.com/docs/en/parts/controller/opencr10/#arduino-ide) | `colcon.meta`            |
| [Teensy 4.0](https://www.pjrc.com/store/teensy40.html)                              | v1.8.5      | Not tested | [Based on Teensyduino](https://www.pjrc.com/teensy/td_download.html)                                | `colcon.meta`            |
| [Teensy 4.1](https://www.pjrc.com/store/teensy41.html)                              | v1.8.5      | Supported  | [Based on Teensyduino](https://www.pjrc.com/teensy/td_download.html)                                | `colcon.meta`            |
| [Teensy 3.2/3.1](https://www.pjrc.com/store/teensy32.html)                          | v1.8.5      | Supported  | [Based on Teensyduino](https://www.pjrc.com/teensy/td_download.html)                                | `colcon_lowmem.meta`     |
| [Teensy 3.5](https://www.pjrc.com/store/teensy35.html)                              | v1.8.5      | Not tested | [Based on Teensyduino](https://www.pjrc.com/teensy/td_download.html)                                | `colcon_lowmem.meta`     |
| [Teensy 3.6](https://www.pjrc.com/store/teensy36.html)                              | v1.8.5      | Supported  | [Based on Teensyduino](https://www.pjrc.com/teensy/td_download.html)                                | `colcon_lowmem.meta`     |

Community contributed boards are:

| Board                                                                                    | Min version | Contributor                                    | Details | Prebuild meta            |
| ---------------------------------------------------------------------------------------- | ----------- | ---------------------------------------------- | ------- | ------------------------ |
| [Arduino Due](https://store.arduino.cc/arduino-due)                                      | -           | [@lukicdarkoo](https://github.com/lukicdarkoo) |         | `colcon_verylowmem.meta` |
| [Arduino Zero](https://store.arduino.cc/arduino-zero)                                    | -           | [@lukicdarkoo](https://github.com/lukicdarkoo) |         | `colcon_verylowmem.meta` |
| [Kakute F7](http://www.holybro.com/product/kakute-f7-aio-v1-5/)                          | -           | [@amfern](https://github.com/amfern)           |         | `colcon.meta`            |
| [STM32-E407](https://www.olimex.com/Products/ARM/ST/STM32-E407/resources/STM32-E407.pdf) | -           | [@dominikn](https://github.com/dominikn)       |         | `colcon.meta`            |

You can find the available precompiled ROS 2 types for messages and services in [available_ros2_types](available_ros2_types).

## How to use the precompiled library

### Arduino IDE

Go to [link to release section](https://github.com/micro-ROS/micro_ros_arduino/releases) and download the last release of micro-ROS library for Arduino.

Include it in your proyect using `Sketch -> Include library -> Add .ZIP Library...`

You can test micro-ROS examples located in this repo examples folder.

Remember that is possible to use a micro-ROS Agent just with this docker command:

```bash
# Serial micro-ROS Agent
docker run -it --rm -v /dev:/dev --privileged --net=host microros/micro-ros-agent:galactic serial --dev [YOUR BOARD PORT] -v6
```
### PlatformIO

For boards supported by micro-ROS, all you have to do to add the library to your project is including the following lines in the existing `platformio.ini` file:

```ini
[env:<YOUR_BOARD>]

...
lib_deps =
    https://github.com/micro-ROS/micro_ros_arduino

build_flags =
    -L ./.pio/libdeps/<YOUR_BOARD>/micro_ros_arduino/src/<BOARD_ARCHITECTURE>/
    -l microros
    -D <TARGET_DEFINITION>
```

Now to proceed with the PlatformIO workflow:

```bash
pio lib install # Install dependencies
pio run # Build the firmware
pio run --target upload # Flash the firmware
```

An example of a micro-ROS application using PlatformIO is available [here](https://github.com/husarion/micro_ros_stm32_template).
## How to build the precompiled library

```bash
docker pull microros/micro_ros_static_library_builder:galactic
docker run -it --rm -v $(pwd):/project --env MICROROS_LIBRARY_FOLDER=extras microros/micro_ros_static_library_builder:galactic
```
Note that folders added to `extras/library_generation/extra_packages` and entries added to `extras/library_generation/extra_packages/extra_packages.repos` will be taken into account by this build system.

## Patch Arduino board for support precompiled libraries
### Patch Teensyduino

Go inside your Arduino + Teensyduino installation and replace `platform.txt`:

```bash
export ARDUINO_PATH=[Your Arduino + Teensiduino path]
cd $ARDUINO_PATH/hardware/teensy/avr/
curl https://raw.githubusercontent.com/micro-ROS/micro_ros_arduino/galactic/extras/patching_boards/platform_teensy.txt > platform.txt
```

### Patch SAMD

Go inside your Arduino + Teensyduino installation and replace `platform.txt`:

```bash
export ARDUINO_PATH=[Your Arduino path]
cd $ARDUINO_PATH/hardware/sam/1.6.12/
curl https://raw.githubusercontent.com/micro-ROS/micro_ros_arduino/galactic/extras/patching_boards/platform_arduinocore_sam.txt > platform.txt
```

## Purpose of the Project

This software is not ready for production use. It has neither been developed nor
tested for a specific use case. However, the license conditions of the
applicable Open Source licenses allow you to adapt the software to your needs.
Before using it in a safety relevant setting, make sure that the software
fulfills your requirements and adjust it according to any applicable safety
standards, e.g., ISO 26262.

## License

This repository is open-sourced under the Apache-2.0 license. See the [LICENSE](LICENSE) file for details.

For a list of other open-source components included in this repository,
see the file [3rd-party-licenses.txt](3rd-party-licenses.txt).

## Known Issues/Limitations

- When using provided precompiled libraries, users should take into account the already configured static memory pools in middleware layers. [More info here](https://micro-ros.github.io/docs/tutorials/core/microxrcedds_rmw_configuration/).
- micro-ROS transports should be refactored in order to provide a pluggable mechanims. Only USB serial transports are provided.
- Teensyduino support files have to be patched in order to use precompiled libraries.
