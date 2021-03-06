# Copyright (c) 2018, Marcelo Samsoniuk
# All rights reserved.
# 
# Redistribution and use in source and binary forms, with or without
# modification, are permitted provided that the following conditions are met:
# 
# * Redistributions of source code must retain the above copyright notice, this
#   list of conditions and the following disclaimer.
# 
# * Redistributions in binary form must reproduce the above copyright notice,
#   this list of conditions and the following disclaimer in the documentation
#   and/or other materials provided with the distribution.
# 
# * Neither the name of the copyright holder nor the names of its
#   contributors may be used to endorse or promote products derived from
#   this software without specific prior written permission.
# 
# THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS"
# AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
# IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE
# DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE
# FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL
# DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR
# SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER
# CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY,
# OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE
# OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE. 

#CROSS = riscv-elf
#CCPATH = /usr/local/share/toolchain-$(CROSS)/bin

CROSS = riscv32-unknown-elf
CCPATH = /usr/local/share/gcc-$(CROSS)/bin/

    CC = $(CCPATH)/$(CROSS)-gcc
    AS = $(CCPATH)/$(CROSS)-as
    LD = $(CCPATH)/$(CROSS)-ld
    OC = $(CCPATH)/$(CROSS)-objcopy
    OD = $(CCPATH)/$(CROSS)-objdump

CCFLAGS = -Wall -I./include -Os -fomit-frame-pointer -march=rv32i
ASFLAGS = 
LDFLAGS = -T$(PROJ).ld -Map=$(PROJ).map # -Ttext=0

MEMSIZE = 4096

OCFLAGS = -O binary
ODFLAGS = -d

PROJ = darksocv
OBJS = boot.o stdio.o main.o io.o hello.o
ASMS = boot.s stdio.s main.s io.s hello.s

all:
	make $(PROJ).rom
	make $(PROJ).ram

clean:
	-rm *.s *.o $(PROJ).{bin,lst,map,ram,rom}

%.o: %.s
	$(AS) $(ASFLAGS) -c $< -o $@

%.s: %.c $(PROJ).ld
	$(CC) $(CCFLAGS) -S $< -o $@

$(PROJ).o: $(ASMS) $(OBJS)
	$(LD) $(LDFLAGS) $(OBJS) -o $@

$(PROJ).bin: $(PROJ).o
	$(OC) $(OCFLAGS) $< $@
	$(OD) $(ODFLAGS) $< > $(PROJ).lst

$(PROJ).rom: $(PROJ).bin
	hexdump -ve '1/4 "%08x\n"' -n $(MEMSIZE) $< | grep -v 00000000 > $@
	wc -l $@

$(PROJ).ram: $(PROJ).bin
	hexdump -ve '1/4 "%08x\n"' -s $(MEMSIZE) $< > $@
	wc -l $@