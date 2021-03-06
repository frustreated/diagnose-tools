#
# ../../vender/module.mk 用于支持厂商自有ＯＳ版本
#

DIAG_EXTRA_CFLAGS := -DMODULE

UNAME := $(shell uname -r)
ifeq ($(KERNEL_BUILD_PATH),)
	KERNEL_BUILD_PATH := /lib/modules/$(UNAME)/build
endif

ifeq ($(CFLAGS_MODULE),)
	DIAG_EXTRA_CFLAGS += $(CFLAGS_MODULE)
endif

ifeq ($(EXPERIENTIAL),1)
	DIAG_EXTRA_CFLAGS += -DEXPERIENTIAL
endif

ifneq ($(findstring 3.10.0-862.el7.x86_64,$(KERNEL_BUILD_PATH)),)
	DIAG_EXTRA_CFLAGS += -DCENTOS_3_10_862 -DCENTOS_7U
endif

ifneq ($(findstring 3.10.0-957.el7.x86_64,$(KERNEL_BUILD_PATH)),)
	DIAG_EXTRA_CFLAGS += -DCENTOS_3_10_957 -DCENTOS_7U
endif

ifneq ($(findstring 3.10.0-957.21.3.el7.x86_64,$(KERNEL_BUILD_PATH)),)
	DIAG_EXTRA_CFLAGS += -DCENTOS_3_10_957_21_3 -DCENTOS_7U
endif

ifneq ($(findstring 3.10.0-1062.1.2.el7.x86_64,$(KERNEL_BUILD_PATH)),)
	DIAG_EXTRA_CFLAGS += -DCENTOS_3_10_1062_1_2 -DCENTOS_7U
endif

ifneq ($(findstring 3.10.0-1062.9.1.el7.x86_64,$(KERNEL_BUILD_PATH)),)
	DIAG_EXTRA_CFLAGS += -DCENTOS_3_10_1062_9_1 -DCENTOS_7U
endif

ifneq ($(KERNELRELEASE),)
	TARGET := diagnose
	obj-m = $(TARGET).o

	ccflags-y += -I$(PWD) -I$(PWD)/../ -Wuninitialized

	$(TARGET)-objs = pub/trace_buffer.o pub/trace_file.o pub/variant_buffer.o pub/trace_point.o \
			pub/kprobe.o pub/stack.o pub/cgroup.o pub/fs_utils.o pub/uprobe.o
	ifeq ($(EXPERIENTIAL),1)
		$(TARGET)-objs += pub/remote_stack.o
	endif

	$(TARGET)-objs += entry.o misc.o symbol.o mm_tree.o stack.o stub.o pupil.o
	$(TARGET)-objs += kernel/kern_entry.o kernel/mutex.o kernel/load.o kernel/exit.o \
			kernel/irq_stats.o kernel/irq_delay.o kernel/sys_delay.o \
			kernel/exec.o kernel/perf.o kernel/run_trace.o kernel/irq_trace.o \
			kernel/kprobe.o kernel/utilization.o kernel/sched_delay.o kernel/reboot.o \
			kernel/uprobe.o kernel/sys_cost.o

	$(TARGET)-objs += mm/mm_entry.o mm/alloc_page.o mm/alloc_top.o mm/high_order.o
	$(TARGET)-objs += io/io_entry.o
	$(TARGET)-objs += fs/fs_entry.o fs/orphan.o fs/shm.o fs/cache.o fs/rw_top.o
	$(TARGET)-objs += net/net_entry.o net/tcp_retrans.o net/drop_packet.o net/ping_delay.o

	ifeq ($(EXPERIENTIAL),1)
		$(TARGET)-objs += test/test.o
	endif

	ifeq ($(EXPERIENTIAL),1)
		$(TARGET)-objs += mm/memory_leak.o
	endif
else
    include ${shell pwd}/../../vender/module.mk
    ifeq ($(KERNEL_BUILD_PATH),)
        KERNEL_BUILD_PATH=/lib/modules/$(shell uname -r)/build
    endif
    MOD_PATH := $(shell pwd)
    default:
	    $(MAKE) CFLAGS_MODULE="$(DIAG_EXTRA_CFLAGS)" -C $(KERNEL_BUILD_PATH) M=$(MOD_PATH) modules
	    strip --strip-debug diagnose.ko

    clean:
	    rm -rf *.o *.mod.c *.mod.o *.ko* Module.symvers modules.order .*.cmd
	    rm -rf net/*.o net/*.mod.c net/*.mod.o net/*.ko* net/Module.symvers net/modules.order net/.*.cmd
	    rm -rf io/*.o io/*.mod.c io/*.mod.o io/*.ko* io/Module.symvers io/modules.order io/.*.cmd
	    rm -rf kernel/*.o kernel/*.mod.c kernel/*.mod.o kernel/*.ko* kernel/Module.symvers kernel/modules.order kernel/.*.cmd
	    rm -rf hooks/*.o hooks/*.mod.c hooks/*.mod.o hooks/*.ko* hooks/Module.symvers hooks/modules.order hooks/.*.cmd
	    rm -rf fs/*.o fs/*.mod.c fs/*.mod.o fs/*.ko* fs/Module.symvers fs/modules.order fs/.*.cmd
	    rm -rf mm/*.o mm/*.mod.c mm/*.mod.o mm/*.ko* mm/Module.symvers mm/modules.order mm/.*.cmd
	    rm -rf pub/*.o pub/*.mod.c pub/*.mod.o pub/*.ko* pub/Module.symvers pub/modules.order pub/.*.cmd
	    rm -rf test/*.o test/*.mod.c test/*.mod.o test/*.ko* test/Module.symvers test/modules.order test/.*.cmd
endif
