
CROSS=riscv64-unknown-elf-
CFLAGS=

# ---- iCE40 HX8K Breakout Board ----

hx8ksim: hx8kdemo_tb.vvp hx8kdemo_fw.hex
	vvp -N $< +firmware=hx8kdemo_fw.hex

hx8ksynsim: hx8kdemo_syn_tb.vvp hx8kdemo_fw.hex
	vvp -N $< +firmware=hx8kdemo_fw.hex

hx8kdemo.json: hx8kdemo.v spimemio.v simpleuart.v picosoc.v ../picorv32.v
	yosys -ql hx8kdemo.log -p 'synth_ice40 -top hx8kdemo -json hx8kdemo.json' $^

hx8kdemo_tb.vvp: hx8kdemo_tb.v hx8kdemo.v spimemio.v simpleuart.v picosoc.v ../picorv32.v spiflash.v
	iverilog -DNO_ICE40_DEFAULT_ASSIGNMENTS -s testbench -o $@ $^ `yosys-config --datdir/ice40/cells_sim.v`

hx8kdemo_syn_tb.vvp: hx8kdemo_tb.v hx8kdemo_syn.v spiflash.v
	iverilog -DNO_ICE40_DEFAULT_ASSIGNMENTS -s testbench -o $@ $^ `yosys-config --datdir/ice40/cells_sim.v`

hx8kdemo_syn.v: hx8kdemo.json
	yosys -p 'read_json hx8kdemo.json; write_verilog hx8kdemo_syn.v'

hx8kdemo.asc: hx8kdemo.pcf hx8kdemo.json
	nextpnr-ice40 --hx8k --package ct256 --asc hx8kdemo.asc --json hx8kdemo.json --pcf hx8kdemo.pcf

hx8kdemo.bin: hx8kdemo.asc
	icetime -d hx8k -c 12 -mtr hx8kdemo.rpt hx8kdemo.asc
	icepack hx8kdemo.asc hx8kdemo.bin

hx8kprog: hx8kdemo.bin hx8kdemo_fw.bin
	iceprog hx8kdemo.bin
	iceprog -o 1M hx8kdemo_fw.bin

hx8kprog_fw: hx8kdemo_fw.bin
	iceprog -o 1M hx8kdemo_fw.bin

hx8kdemo_sections.lds: sections.lds
	$(CROSS)cpp -P -DHX8KDEMO -o $@ $^

hx8kdemo_fw.elf: hx8kdemo_sections.lds start.s firmware.c
	$(CROSS)gcc $(CFLAGS) -DHX8KDEMO -mabi=ilp32 -march=rv32imc -Wl,--build-id=none,-Bstatic,-T,hx8kdemo_sections.lds,--strip-debug -ffreestanding -nostdlib -o hx8kdemo_fw.elf start.s firmware.c

hx8kdemo_fw.hex: hx8kdemo_fw.elf
	$(CROSS)objcopy -O verilog hx8kdemo_fw.elf hx8kdemo_fw.hex

hx8kdemo_fw.bin: hx8kdemo_fw.elf
	$(CROSS)objcopy -O binary hx8kdemo_fw.elf hx8kdemo_fw.bin

# ---- iCE40 IceBreaker Board ----

icebsim: icebreaker_tb.vvp icebreaker_fw.hex
	vvp -N $< +firmware=icebreaker_fw.hex

icebsynsim: icebreaker_syn_tb.vvp icebreaker_fw.hex
	vvp -N $< +firmware=icebreaker_fw.hex

icebreaker.json: icebreaker.v ice40up5k_spram.v spimemio.v simpleuart.v picosoc.v ../picorv32.v
	yosys -ql icebreaker.log -p 'synth_ice40 -dsp -top icebreaker -json icebreaker.json' $^

icebreaker_tb.vvp: icebreaker_tb.v icebreaker.v ice40up5k_spram.v spimemio.v simpleuart.v picosoc.v ../picorv32.v spiflash.v
	iverilog -s testbench -o $@ $^ `yosys-config --datdir/ice40/cells_sim.v` -DNO_ICE40_DEFAULT_ASSIGNMENTS

icebreaker_syn_tb.vvp: icebreaker_tb.v icebreaker_syn.v spiflash.v
	iverilog -s testbench -o $@ $^ `yosys-config --datdir/ice40/cells_sim.v` -DNO_ICE40_DEFAULT_ASSIGNMENTS

icebreaker_syn.v: icebreaker.json
	yosys -p 'read_json icebreaker.json; write_verilog icebreaker_syn.v'

icebreaker.asc: icebreaker.pcf icebreaker.json
	nextpnr-ice40 --freq 13 --up5k --package sg48 --asc icebreaker.asc --pcf icebreaker.pcf --json icebreaker.json

icebreaker.bin: icebreaker.asc
	icetime -d up5k -c 12 -mtr icebreaker.rpt icebreaker.asc
	icepack icebreaker.asc icebreaker.bin

icebprog: icebreaker.bin icebreaker_fw.bin
	iceprog icebreaker.bin
	iceprog -o 1M icebreaker_fw.bin

icebprog_fw: icebreaker_fw.bin
	iceprog -o 1M icebreaker_fw.bin

icebreaker_sections.lds: sections.lds
	$(CROSS)cpp -P -DICEBREAKER -o $@ $^

icebreaker_fw.elf: icebreaker_sections.lds start.s firmware.c
	$(CROSS)gcc $(CFLAGS) -DICEBREAKER -mabi=ilp32 -march=rv32ic -Wl,-Bstatic,-T,icebreaker_sections.lds,--strip-debug -ffreestanding -nostdlib -o icebreaker_fw.elf start.s firmware.c

icebreaker_fw.hex: icebreaker_fw.elf
	$(CROSS)objcopy -O verilog icebreaker_fw.elf icebreaker_fw.hex

icebreaker_fw.bin: icebreaker_fw.elf
	$(CROSS)objcopy -O binary icebreaker_fw.elf icebreaker_fw.bin

# ---- Olimex iCE40HX8K-EVB ----

olimex_hx8ksim: olimex_hx8kdemo_tb.vvp olimex_hx8kdemo_fw.hex
	vvp -N $< +firmware=olimex_hx8kdemo_fw.hex

olimex_hx8ksynsim: olimex_hx8kdemo_syn_tb.vvp olimex_hx8kdemo_fw.hex
	vvp -N $< +firmware=olimex_hx8kdemo_fw.hex

olimex_hx8kdemo.json: olimex_hx8kdemo.v spimemio.v simpleuart.v picosoc.v ../picorv32.v
	yosys -ql olimex_hx8kdemo.log -p 'synth_ice40 -top hx8kdemo -json olimex_hx8kdemo.json' $^

olimex_hx8kdemo_tb.vvp: olimex_hx8kdemo_tb.v olimex_hx8kdemo.v spimemio.v simpleuart.v picosoc.v ../picorv32.v spiflash.v
	iverilog -DNO_ICE40_DEFAULT_ASSIGNMENTS -s testbench -o $@ $^ `yosys-config --datdir/ice40/cells_sim.v`

olimex_hx8kdemo_syn_tb.vvp: olimex_hx8kdemo_tb.v olimex_hx8kdemo_syn.v spiflash.v
	iverilog -DNO_ICE40_DEFAULT_ASSIGNMENTS -s testbench -o $@ $^ `yosys-config --datdir/ice40/cells_sim.v`

olimex_hx8kdemo_syn.v: olimex_hx8kdemo.json
	yosys -p 'read_json olimex_hx8kdemo.json; write_verilog olimex_hx8kdemo_syn.v'

olimex_hx8kdemo.asc: olimex_hx8kdemo.pcf olimex_hx8kdemo.json
	nextpnr-ice40 --freq 10 --hx8k --package ct256 --asc olimex_hx8kdemo.asc --json olimex_hx8kdemo.json --pcf olimex_hx8kdemo.pcf

olimex_hx8kdemo.bin: olimex_hx8kdemo.asc
	icetime -d hx8k -c 10 -mtr olimex_hx8kdemo.rpt olimex_hx8kdemo.asc
	icepack olimex_hx8kdemo.asc olimex_hx8kdemo.bin

olimex_hx8kprog: olimex_hx8kdemo.bin olimex_hx8kdemo_fw.bin
	iceprogduino -I/dev/cu.usbmodem14301 -v olimex_hx8kdemo.bin
	iceprogduino -I/dev/cu.usbmodem14301 -o 1M -v olimex_hx8kdemo_fw.bin
	
olimex_hx8kprog_fw: olimex_hx8kdemo_fw.bin
	iceprogduino -I/dev/cu.usbmodem14301 -o 1M -v olimex_hx8kdemo_fw.bin

olimex_hx8kdemo_sections.lds: sections.lds
	$(CROSS)cpp -P -DHX8KDEMO -o $@ $^

olimex_hx8kdemo_fw.elf: olimex_hx8kdemo_sections.lds start.s olimex_firmware.c
	$(CROSS)gcc $(CFLAGS) -DHX8KDEMO -mabi=ilp32 -march=rv32imc -Wl,--build-id=none,-Bstatic,-T,olimex_hx8kdemo_sections.lds,--strip-debug -ffreestanding -nostdlib -o olimex_hx8kdemo_fw.elf start.s olimex_firmware.c

olimex_hx8kdemo_fw.hex: olimex_hx8kdemo_fw.elf
	$(CROSS)objcopy -O verilog olimex_hx8kdemo_fw.elf olimex_hx8kdemo_fw.hex

olimex_hx8kdemo_fw.bin: olimex_hx8kdemo_fw.elf
	$(CROSS)objcopy -O binary olimex_hx8kdemo_fw.elf olimex_hx8kdemo_fw.bin

# ---- Testbench for SPI Flash Model ----

spiflash_tb: spiflash_tb.vvp icebreaker_fw.hex
	vvp -N $< +firmware=icebreaker_fw.hex

spiflash_tb.vvp: spiflash.v spiflash_tb.v
	iverilog -s testbench -o $@ $^

# ---- ASIC Synthesis Tests ----

cmos.log: spimemio.v simpleuart.v picosoc.v ../picorv32.v
	yosys -l cmos.log -p 'synth -top picosoc; abc -g cmos2; opt -fast; stat' $^

# ---- Clean ----

clean:
	rm -f testbench.vvp testbench.vcd spiflash_tb.vvp spiflash_tb.vcd
	rm -f hx8kdemo_fw.elf hx8kdemo_fw.hex hx8kdemo_fw.bin cmos.log
	rm -f icebreaker_fw.elf icebreaker_fw.hex icebreaker_fw.bin
	rm -f hx8kdemo.json hx8kdemo.blif hx8kdemo.log hx8kdemo.asc hx8kdemo.rpt hx8kdemo.bin
	rm -f hx8kdemo_syn.v hx8kdemo_syn_tb.vvp hx8kdemo_tb.vvp
	rm -f icebreaker.json icebreaker.log icebreaker.asc icebreaker.rpt icebreaker.bin
	rm -f icebreaker_syn.v icebreaker_syn_tb.vvp icebreaker_tb.vvp
	rm -f olimex_hx8kdemo_fw.elf olimex_hx8kdemo_fw.hex olimex_hx8kdemo_sections.lds olimex_hx8kdemo_tb.vvp
	rm -f olimex_hx8kdemo.log olimex_hx8kdemo.json olimex_hx8kdemo_syn.v olimex_hx8kdemo_syn_tb.vvp 
.PHONY: spiflash_tb clean
.PHONY: hx8kprog hx8kprog_fw hx8ksim hx8ksynsim
.PHONY: icebprog icebprog_fw icebsim icebsynsim
