# Makefile
ifeq ($(wildcard ${HR_TOP_DIR}),)
	KDIR := $(shell realpath ../../kernel)
	EXTRA_CFLAGS += -I$(src) -I../../kernel/drivers/soc/hobot/bpu -I../../kernel/drivers/iommu
	BUILD_OPTIONS := "ARCH=arm64"
	INSTALL_MOD_PATH = ../../../deploy/kernel/modules
else
	EXTRA_CFLAGS += -I$(src) -I${HR_TOP_DIR}/source/kernel/drivers/soc/hobot/bpu -I${HR_TOP_DIR}/source/kernel/drivers/iommu
	ifeq ($(wildcard ${HR_KERNEL_OUTPUT_DIR}),)
		KDIR := $(shell realpath ${HR_TOP_DIR}/source/kernel)
		BUILD_OPTIONS :=
	else
		KDIR := $(shell realpath ${HR_KERNEL_OUTPUT_DIR})
		BUILD_OPTIONS := O=${HR_KERNEL_OUTPUT_DIR}
	endif
	INSTALL_MOD_PATH = ${HR_TOP_DIR}/deploy/kernel/modules
endif

ifeq ($(wildcard $(PWD)/.git),)
	BPUIO_GIT_COMMIT := unknown
else
	BPUIO_GIT_COMMIT := $(shell cd ${PWD} && git rev-parse --verify --short HEAD)
	BPUIO_GIT_STATUS := $(shell cd ${PWD} && git diff-index --name-only HEAD)
	ifneq ($(strip $(BPUIO_GIT_STATUS)),)
		BPUIO_GIT_COMMIT := ${BPUIO_GIT_COMMIT}-dirty
	endif
endif
EXTRA_CFLAGS += -DBPUIO_GIT_COMMIT=\"$(BPUIO_GIT_COMMIT)\"

ifneq ($(KERNELRELEASE),)

obj-m += bpu_hw_io_x5.o

else

CROSS_COMPILE ?= /opt/gcc-arm-11.2-2022.02-x86_64-aarch64-none-linux-gnu/bin/aarch64-none-linux-gnu-
ARCH ?= arm64


# install: /lib/modules/6.1.12-rt7*/kernel/drivers/soc/hobot/bpu/hw_io

all:
	$(MAKE) INSTALL_MOD_PATH=${INSTALL_MOD_PATH} INSTALL_MOD_STRIP=${INSTALL_MOD_STRIP} \
			-C $(KDIR) M=$(PWD) ARCH=$(ARCH) CROSS_COMPILE=$(CROSS_COMPILE) modules modules_install

clean:
	$(MAKE) -C $(KDIR) M=$(PWD) ARCH=$(ARCH) CROSS_COMPILE=$(CROSS_COMPILE) clean

# Module specific targets
genbin:
	echo "X" > bpu_hw_io_x5.o_shipped

endif
