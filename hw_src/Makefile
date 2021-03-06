include config.mk

PFM := $(PLATFORM_REPO_PATHS)/$(PLATFORM)/$(PLATFORM).xpfm

NCPUS := $(shell grep -c ^processor /proc/cpuinfo)
JOBS := $(shell expr $(NCPUS) - 1)

VISION_LIB_FLAGS := -I. -I./Vitis_Libraries/vision/L1/include --config vision_config.ini

VPPFLAGS := --platform $(PFM) -t $(TARGET) -s -g
VPPLFLAGS := --jobs $(JOBS) --config profile.ini
ifeq ($(PLATFORM), xilinx_u250_xdma_201830_2)
	BOARD_CONFIG := connectivity_u250.ini
else
	BOARD_CONFIG := connectivity_u200.ini
endif
VPPLFLAGS += --config $(BOARD_CONFIG)

XOS = vadd.xo wide_vadd.xo resize_rgb.xo resize_blur.xo

.phony: clean traces help

all: alveo_examples.xclbin

alveo_examples.xclbin: $(XOS) $(BOARD_CONFIG)
	v++ -l $(VPPFLAGS) $(VPPLFLAGS) -o $@ $(XOS)

vadd.xo: vadd.cpp
	v++ --kernel vadd $(VPPFLAGS) -c -o $@ $<

wide_vadd.xo: wide_vadd.cpp
	v++ --kernel wide_vadd $(VPPFLAGS) -c -o $@ $<

resize_rgb.xo: resize_rgb.cpp vision_config.ini
	v++ --kernel resize_accel_rgb $(VPPFLAGS) $(VISION_LIB_FLAGS) -c -o $@ $<

resize_blur.xo: resize_blur.cpp vision_config.ini
	v++ --kernel resize_blur_rgb $(VPPFLAGS) $(VISION_LIB_FLAGS) -c -o $@ $<

clean:
	$(RM) -r *.xo _x .Xil sd_card *.xclbin *.ltx *.log *.info *compile_summary* vitis_analyzer* *link_summary*
