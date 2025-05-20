# SiFive HiFive P550 Premier

This is the first generally available board for EIC7700X. The board is [available from SiFive](https://www.sifive.com/boards/hifive-premier-p550) and released at $600 USD. 

## Hardware notes

The board is using the DTX form factor, which is between the Mini-ITX and Micro-ATX form factor. Some ITX cases will work, but for best compatibility, a Micro-ATX case is your best bet.

For power, you should use the ATX connector and not the barrel conector (which is only used for debugging).

There are three Ethernet interfaces: two are for the EIC7700X Gigabit interfaces, and one is for the BMC.

## BMC

On release, the BMC is very restrictive and barebones. It only supports HTTP and not HTTPS for access, and only supports plain text authentication. It also seems to have a few issues around session cookies. For best security and compatibility, avoiding the Ethernet interface is advisable.

There is also a USB-C connector on the board which attaches to an FTDI FT4232-HS USB device which exposes four interfaces:
```
/dev/serial/by-id/usb-FTDI_Quad_RS232-HS-if00-port0 <- JTAG for EIC7700X
/dev/serial/by-id/usb-FTDI_Quad_RS232-HS-if01-port0 <- Serial Console for EIC7700X @ 115200bps, No Parity, 8 data bits, 1 stop bit (speed configurable by OS)
/dev/serial/by-id/usb-FTDI_Quad_RS232-HS-if02-port0 <- JTAG for BMC
/dev/serial/by-id/usb-FTDI_Quad_RS232-HS-if03-port0 <- BMC Serial Interface @ 115200bps, No Parity, 8 data bits, 1 stop bit
```

The BMC serial interface has a simple CLI and supports a "help" command:
```
#cmd: help                                                             
                                                                                                                                              
help:                                                                  
 Lists all the registered commands                                     
                                                                                                                                              
                                                                       
stats: Displays a table with the state of each FreeRTOS task.
                                                                       
cbinfo-g: Display carrierboard information.
                                                                       
cbinfo-s <magic/format/productid/pcbr/bomr/bomv/boardsn/manu> <value in hex>: Set magicNumber...
                                   
sominfo: Display somboard information.

account-g: Show account name and password.                                                                                   

account-s <name> <password>: Set account name and password.

ifconfig: Display network configuration.

setip <ipaddr>: Set ip address.

setmask <ipaddr>: Set netmask address.

setgateway <ipaddr>: Set gateway address.

setmac <index,0-2> <mac,like a1:26:39:91:b0:22>: Set mac address.

bootsel-g: Show software bootsel configuration.

bootsel-s <hw/sw> <bootsel(hex), like, F>: Set software bootsel configuration.

date: Get the current date and time.

date-s <Year> <Month> <Date> <WeekDay 1-7>: Set a new date.

time-s <Hours> <Minutes> <Seconds>: Set a new time.

temp: Get the temperature and fan speed.

pd: Get the power dissipation

sompower-g: Get the som power status. ON or OFF.

sompower-s <1/0>: Power (1)ON or (0)OFF.

somwork: Get the kernel work status of the som board.

reboot <cold/warm>: cold or warm reboot the kernel on som board.

devmem-r <address in hex>: Read the memory/io address of som board.

devmem-w <address in hex,like 0x180000000> <value in hex,like 0xabcd1234>: write the memory/io address of som board.

powerlost-g: Get the som power lost attr. Enable or Disable.

powerlost-s <1/0>: (1)Enable or (0)Disable.

pwrlast-g: Get the som power last state. 1 or 0.

somconsole-g: Get the som console configuration. UART or Telnet

somconsole-s: Set the som console configuration. 0(via UART), 1(via Telnet).

echo <string to echo>

heap: Display free heap memory.

version: Get BMC version
```

# See also

- [related pmOS wiki page](https://wiki.postmarketos.org/wiki/SiFive_HiFive_Premier_P550_(sifive-hifive-premier-p550))