## setup on Ubuntu (23.10)
* download https://www.xilinx.com/member/forms/download/xef.html?filename=Xilinx_Unified_2021.1_0610_2318_Lin64.bin
* `sudo bash Xilinx_Unified_2021.1_0610_2318_Lin64.bin` and install it to `/opt/Xilinx`
* or FPGAs_AdaptiveSoCs_Unified_2023.2_1013_2256_Lin64.bin from https://www.xilinx.com/support/download.html
* ![image](https://github.com/eecsmap/logs/assets/71915887/d7d2e6e7-ab4b-4a32-9c49-7cabf5c1ecc6)
* `cd /lib/x86_64-linux-gnu/ && sudo ln -s libtinfo.so libtinfo.so.5`
* add `/opt/Xilinx/Vivado/2023.2/bin` into $PATH
* (maybe no need) `git clone https://github.com/cathalmccabe/pynq-z1_board_files`
* copy pynq-z1 into {Vivado install directory}\data\boards\board_files, if board_files does not exist, create it first.
* this may be necessary: https://digilent.com/reference/programmable-logic/guides/install-cable-drivers

## verify
* `git clone https://github.com/eecsmap/fpga_101`
* `cd fpga_101/01_button_led/src`
* `make build`
