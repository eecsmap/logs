## setup on Ubuntu (23.10)
1. download latest Vivado ML installer from https://www.xilinx.com/support/download.html (for now it is [2023.2](https://www.xilinx.com/member/forms/download/xef.html?filename=FPGAs_AdaptiveSoCs_Unified_2023.2_1013_2256_Lin64.bin))
* `sudo bash {your_installer_file}.bin` and install it to `/opt/Xilinx` with options as the following screenshot
* ![image](https://github.com/eecsmap/logs/assets/71915887/d7d2e6e7-ab4b-4a32-9c49-7cabf5c1ecc6)
* add `/opt/Xilinx/Vivado/2023.2/bin` into $PATH so that you can run `vivado`
* if running `vivado` complains missing of libtinfo, then `cd /lib/x86_64-linux-gnu/ && sudo ln -s libtinfo.so libtinfo.so.5`
* try verify the setup with instructions in section **verify**
* if cannot find the part or board, try `git clone https://github.com/cathalmccabe/pynq-z1_board_files`
* then copy pynq-z1 into {Vivado install directory}\data\boards\board_files, if board_files does not exist, create it.
* This may be necessary: https://digilent.com/reference/programmable-logic/guides/install-cable-drivers

## verify
* `git clone https://github.com/eecsmap/fpga_101`
* `cd fpga_101/01_button_led/src`
* `make build`

## or you can verify with a vivado project
* launch vivado
* create a new RTL project
![image](https://github.com/eecsmap/logs/assets/71915887/03205fc5-b504-46a7-aa59-5d30cd970d5a)
![image](https://github.com/eecsmap/logs/assets/71915887/56abc386-6b70-4cff-9aef-1cc30de233dc)
* add a top module file: top.v
```
module top(
    input button,
    output led
    );
    assign led = button;
endmodule
```
* add the constraints file: constraints.xdc
```
set_property -dict {PACKAGE_PIN D19 IOSTANDARD LVCMOS33} [get_ports {button}]
set_property -dict {PACKAGE_PIN R14 IOSTANDARD LVCMOS33} [get_ports {led}]
```
* pick up the part: `xc7z020clg400-1`
![image](https://github.com/eecsmap/logs/assets/71915887/bed249fe-8cc5-46bc-9eda-a962be52d4ff)
* generate bitstream
![image](https://github.com/eecsmap/logs/assets/71915887/cb302f88-25c8-4c3a-aad3-07c6051a8508)
![image](https://github.com/eecsmap/logs/assets/71915887/41eb1a14-d705-4c53-86f6-d5e43663c3dc)
* open target and auto connect
![image](https://github.com/eecsmap/logs/assets/71915887/36c7ea75-e592-4364-9382-e366a01aab7d)
* program device and verify the function on board

## prepare the labs and project
Create private clone of labs and project as:
* https://github.com/eecsmap/ucb_eecs151_2022_fall_fpga_labs (from https://github.com/EECS150/fpga_labs_fa22)
* https://github.com/eecsmap/ucb_eecs151_2022_fall_fpga_project (from https://github.com/EECS150/fpga_project_skeleton_fa22)

