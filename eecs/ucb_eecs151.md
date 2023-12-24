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

