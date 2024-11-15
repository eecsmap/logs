# Install Vivado 2024.1 on ubuntu 22.04.4 LTS

According to https://docs.amd.com/r/en-US/ug973-vivado-release-notes-install-license/Supported-Operating-Systems
, the most recent ubuntu version supported by Vivado 2024.1 is the 22.04.3 LTS.
But it works fine on ubuntu 22.04.4 LTS.

## Download the installer

download Vivado ML installer from https://www.xilinx.com/support/download.html

At the time of this writing, the most recent version is 2024.1.

The package file is FPGAs_AdaptiveSoCs_Unified_2024.1_0522_2023.tar.gz

It is about 108GB.

## Extract the files

When extracted, the folder also takes about 108GB.

```
tar xfz /media/engineer/data/Data/FPGAs_AdaptiveSoCs_Unified_2024.1_0522_2023.tar.gz
```

## Install on machine with GUI

Yes, you can do everything without a GUI. If that is what you want, go [here](#install-without-gui)

Now let's install Vivado in the normal interactive way.

### run setup to launch GUI installer

```
cd FPGAs_AdaptiveSoCs_Unified_2024.1_0522_2023
./xsetup
```

If you look carefully you will see in the welcome dialog:

> Note: This installation program will not install cable drivers on Linux. This item will need to be installed separately, with administrative privileges.

This is why we are going to install the drivers later.

Now we are going to install page by page. We only need to install Devices->Production Devices->SoCs->Zynq-7000

![Screenshot from 2024-08-11 15-43-07](https://github.com/user-attachments/assets/7e9ff32e-2cee-4d67-9a04-d18af06a0c43)
![Screenshot from 2024-08-11 15-43-21](https://github.com/user-attachments/assets/1c68aafd-58d4-4c06-9a24-8158290bf311)
![Screenshot from 2024-08-11 15-43-24](https://github.com/user-attachments/assets/847a5766-5bd3-4793-87b2-3483da65737f)
![Screenshot from 2024-08-11 15-43-53](https://github.com/user-attachments/assets/39ebf9df-2d2f-4fe5-8aec-f9daefa9eed7)
![Screenshot from 2024-08-11 15-44-25](https://github.com/user-attachments/assets/5b9226f2-b671-4120-b1cb-5284207bb4c3)
![Screenshot from 2024-08-11 15-45-54](https://github.com/user-attachments/assets/06cfd5ff-833d-4a49-a42f-7ca53b12fe61)
![Screenshot from 2024-08-11 15-45-59](https://github.com/user-attachments/assets/47cdf962-f8e7-4200-8460-6612c1509250)

After installed, it takes 44GB.

## Install without GUI

### Generate the config file

```
./xsetup -b ConfigGen -p 'Vivado' -e 'Vivado ML Standard' -l $HOME/tools/Xilinx
```

Find it as `~/.Xilinx/install_config.txt`, tail it to meet your need. The one matters most to me is to make sure only Zynq-7000 is selected.

You can use [this template](./vivado_install_config.txt) as a reference.

### Install with config file

```bash
./xsetup --agree XilinxEULA,3rdPartyEULA --batch Install --config ~/.Xilinx/install_config.txt
```

![Screenshot from 2024-08-11 16-00-35](https://github.com/user-attachments/assets/64b50ab7-87e4-421c-9973-2aedf902d32b)


## Add Vivado into search PATH

Add `/home/engineer/tools/Xilinx/Vivado/2024.1/bin` into `$PATH` so that you can run `vivado`

```bash
cat <<eof >> ~/.profile

# set PATH if vivado 2024.1 installed
if [ -d "\$HOME/tools/Xilinx/Vivado/2024.1/bin" ]; then
    PATH="\$HOME/tools/Xilinx/Vivado/2024.1/bin:\$PATH"
fi
eof

source ~/.profile
```

## Install drivers to recognize the PYNQ Z-1 board

Without this step, your lab/project will fail on `make program` when it tries to write bitstream onto the device.

```bash
sudo ~/tools/Xilinx/Vivado/2024.1/data/xicom/cable_drivers/lin64/install_script/install_drivers/install_drivers
```

![Screenshot from 2024-08-11 16-02-13](https://github.com/user-attachments/assets/1b59b3a1-06fd-4940-b7c1-206f736072b2)


## Fix lib deps

When you see:

> couldn't load file "librdi_commontasks.so": libtinfo.so.5: cannot open shared object file: No such file or directory

Since we are running ubuntu 22.04.4 LTS, and most likely the libtinfo 6 is installed already. We just use it.

```bash
pushd /lib/x86_64-linux-gnu && sudo ln -s libtinfo.so.6 libtinfo.so.5 && popd
```

## Verify installation without a need of GUI

```bash
sudo apt install make
git clone https://github.com/eecsmap/fpga_101
cd fpga_101/01_button_led/src
make
```

If it works, then you should be able to push the right most little button at the bottom of the PYNQ Z-1 board to light the led above it.

--------

# Verify your installation with a Vivado project

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

----

# UCB EECS151 Notes

## prepare the labs and project
Create private clone of labs and project as:
* https://github.com/eecsmap/ucb_eecs151_2022_fall_fpga_labs (from https://github.com/EECS150/fpga_labs_fa22)
* https://github.com/eecsmap/ucb_eecs151_2022_fall_fpga_project (from https://github.com/EECS150/fpga_project_skeleton_fa22)

## basic concepts
Phases:
* simulation: verify the design using test data
* synthesis: translate the design into gates and wire, (LUTs, FFs etc)
* implementation: place and route
* bitstream generating: serialized result
* programming: load the result into FPGA board

## lab1
on linux
```
make setup
make synth
make program
```

on windows with Vivado GUI
* src/*.v as design files
* sim/*.v as simulation files
* src/*.xdc as constraints file

## lab2
Structural and Behavioral: structural represtation is something called declarative in software programming, while behavioral imperative.

```
make lint - Lint your Verilog with Verilator; checks for common Verilog typos, mistakes, and syntax errors
make elaborate - Elaborate (but don't synthesize) the Verilog with Vivado and open the GUI to view the schematic
make synth - Synthesize z1top and put logs and outputs in build/synth
make impl - Implement (place and route) the design, generate the bitstream, and put logs and outputs in build/impl
make program - Program the FPGA with the bitstream in build/impl
make program-force - Program the FPGA with the bitstream in build/impl without re-running synthesis and implementation if the source Verilog has changed
make vivado - Launch the Vivado GUI
```
install verilator to try `make lint`: `sudo apt install verilator`
install iverilog to do test: `sudo apt install iverilog`

terms:
* schematic
* HDL (hardware definition language)
* RTL (register-transfer level)

Basic building blocks: Module (as function in software programming language)

* check examples on how to write module
* check examples on how to write test on board
* check examples on how to write testbench

system tasks starting with '$' are implemented by simulator
* $dumpfile
* $dumpvars

We can advance simulation time using delay statements.

```
assert(sum == 'd1) else $display("ERROR: Expected sum to be 1, actual value: %d", sum);
$error("error number: %d", 42); // error shows error message with line number
```
these print functions run when you do `vvp demo.tbi`
```
iverilog demo.v -o demo.tbi -g2012
vvp demo.tbi
```
You need to do `-g2012` in order to use `assert`

for test
* `$urandom()` return unsigned 32-bit integers.

## lab5
A ready-valid transaction only occurs when both ready and valid are high on a rising clock edge.
If both ready and valid are high on a rising edge, the source can assume that the sink has received and internally stored the bits on data.

* The baudrate is the number of bits sent per second; in this lab the baudrate will be 115200
* both sides must agree on a baudrate for this scheme to be feasible.

```
    // we might think reset should clean bit_count and put down
    always @(reset) begin
        bit_count <= 0;
    end
    // then we realize bit_count should also update when bit_sent
    always @(posedge reset or posedge bit_sent) begin
        if (reset) bit_count <= 0;
        else if (bit_sent) bit_count <= bit_count + 1;
        if (bit_count == BIT_COUNT_MAX - 1) bit_count <= 0;
    end
    // which could be simplified as
    always @(posedge reset or posedge bit_sent) begin
        if (reset || bit_count == BIT_COUNT_MAX - 1) bit_count <= 0;
        else if (bit_sent) bit_count <= bit_count + 1;
    end
```

---
This is important  :)
```
    assign data_in_valid = ~tx_fifo_empty_delayed;
    always @(posedge CLK_125MHZ_FPGA) begin
        tx_fifo_empty_delayed <= tx_fifo_empty;
    end
```

* read the test bench examples in lab5, especially the mem_controller_tb.v and the system_tb.v


## reading list
* https://inst.eecs.berkeley.edu/~eecs151/fa22/files/verilog/Verilog_Primer_Slides.pdf
* https://inst.eecs.berkeley.edu/~eecs151/fa22/files/verilog/always_at_blocks.pdf
* https://inst.eecs.berkeley.edu/~eecs151/fa22/files/verilog/wire_vs_reg.pdf
* https://inst.eecs.berkeley.edu/~eecs151/fa22/files/verilog/verilog_fsm.pdf
* https://www.youtube.com/watch?v=1FiaAPFCMFU
* https://inst.eecs.berkeley.edu/~eecs151/fa21/files/verilog/ready_valid_interface.pdf

```
sudo apt-get install git make
```

## project

Update submodules:

```
git submodule update --init --recursive
```

under software folder, run:

```
cd software
for i in `ls`; do (cd $i && make -B); done
cd c_tests
for i in `ls`; do (cd $i && make -B); done
```

If `make: riscv64-unknown-elf-gcc: No such file or directory`

* run `sudo apt install gcc-riscv64-unknown-elf`.

if you run into errors like:

```
Error: unrecognized opcode `csrw 0x51e,a5', extension `zicsr' required
Error: unrecognized opcode `fence.i', extension `zifencei' required
```

Update file `software/Makefrag` and `software/riscv-isa-tests/Makefile`, change `-march=rv32i` to `-march=rv32i_zicsr_zifencei` in `GCC_OPTS`.
Or you can search `-march=rv32i` and replace it as `-march=rv32i_zicsr_zifencei`.

If `make: riscv64-unknown-elf-bin2hex: No such file or directory`:

* get a copy of https://github.com/sifive/elf2hex/blob/master/freedom-bin2hex.py
* save it as `/usr/bin/riscv64-unknown-elf-bin2hex`
* add a line to its front with content: `#!/usr/bin/env python3`
* run `chmod a+x /usr/bin/riscv64-unknown-elf-bin2hex`
* NOTE: it is part of a elf2hex project https://github.com/sifive/elf2hex, but here we don't need the whole thing.

If your python does not have `serial` lib installed, install it:

```
sudo apt install python3-pip
pip install pyserial
```

before testing using
```
cd software/riscv-isa-tests && make
```

install iverilog for hardware design

```
sudo apt install iverilog
```

run
```
cd hardware
make sim/cpu_tb.fst -B
make isa-tests -B && grep -r -i "\(timeout\)\|\(failed\)" sim/isa/*.log
make c-tests -B
make sim/echo_tb.fst -B
make sim/uart_parse_tb.fst -B
make sim/bios_tb.fst -B
```

Prepare serial communication:
run
```
sudo apt install screen
sudo usermod -aG dialout $USER
```

build program:

```
make program
```

Then load echo program:
```
./scripts/hex_to_serial ../software/echo/echo.hex 30000000
```

connect serial port:
```
screen /dev/ttyUSB0 115200
```

Type `jal 10000000` after the prompt, if you did not see the prompt `151>`, press `enter` key:

```
151> jal 10000000
(now you can type and see the echo)
```

Run another program:
```
(cd ../software/mmult/ && make)
./scripts/hex_to_serial ../software/mmult/mmult.hex 30000000
```

check in screen:

```
make program
make screen
151> jal 10000000
Result: 0001f800
Cycle Count: 00000000
Instruction Count: 00000000
Branch Instruction Count: 00000000
Correct Branch Prediction Count: 00000000
```

checkpoint 1&2 done!

