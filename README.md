# espf file format

This file format is required for the espforge rust no_std embedded project https://github.com/mohankumargupta/espforge , a esphome-like clone on a 
rust base. 

It seeks to replace yaml with elements combining markdown for headings and xml for leaf nodes.

## Motivation

here is yaml:


--- ========
--- chip: esp32c3, esp32c6
--- runtime: blocking,runtime
--- =========
espforge:
  project: example project
  chip: esp32c3
  runtime: embassy
 
# ==============================================================================
# 1. PERIPHERALS: Physical hardware allocation and SoC pin routing.
# ==============================================================================
peripherals:
  # ONLY general-purpose pins go here (LEDs, Buttons, Chip-Selects, etc.)
  gpio:
    gpio4: 4
    gpio5: 5

  # Dedicated hardware peripherals use raw pin numbers directly
  i2c0:
    sda: 21
    scl: 22
    frequency: 400kHz

  spi2:
    sclk: 18
    mosi: 23
    miso: 19

  uart1:
    tx: 17
    rx: 16

# ==============================================================================
# 2. COMPONENTS: Bus slicing, configuration layers, and resource sharing wrappers.
# ==============================================================================
components:
  gpio_component:
    status_led_io:
      pin: gpio4
      direction: output

  i2c_component:
    temp_sensor_bus:
      bus: i2c0
      address: 0x48

  spi_component:
    display_bus:
      bus: spi2
      cs_pin: gpio5  # References general-purpose pin allocated in peripherals
      mode: 0
      frequency: 40MHz

  uart_component:
    gps_serial_stream:
      bus: uart1
      baud_rate: 9600
      parity: none
      stop_bits: 1

# ==============================================================================
# 3. DEVICES: High-level protocol drivers consuming specific components.
# ==============================================================================
devices:
  status_indicator:
    driver: gpio_led
    gpio_component: status_led_io

  environment_sensor:
    driver: tmp102
    i2c_component: temp_sensor_bus

  main_display:
    driver: ili9341
    spi_component: display_bus

  navigation_system:
    driver: neo6m
    uart_component: gps_serial_stream


here is the equivalent in espf:
