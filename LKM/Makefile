obj-m+=new_lkm.o

all:
	make -C /lib/modules/6.5.0-17-generic/build/ M=$(shell pwd) modules

clean:
	make -C /lib/modules/6.5.0-17-generic/build/ M=$(shell pwd) clean
