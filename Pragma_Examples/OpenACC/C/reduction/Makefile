EXEC = reduction
default: ${EXEC}
all: ${EXEC}

ROCM_GPU ?= $(strip $(shell rocminfo |grep -m 1 -E gfx[^0]{1} | sed -e 's/ *Name: *//'))
UNAMEP = $(shell uname -p)
ROCM_CPUTARGET = $(UNAMEP)-pc-linux-gnu

ifeq ($(notdir $(CC)), clang)
  OPENACC_FLAGS = Clang_does_not_support_OpenACC_for_AMD_GPUs
else ifeq ($(notdir $(CC)), amdclang)
  OPENACC_FLAGS = amdclang_does_not_support_OpenACC_for_AMD_GPUs
else
  OPENACC_FLAGS = -fopenacc -foffload=-march=${ROCM_GPU} -fopt-info-optimized-omp
endif

CFLAGS = -g -O3 -fstrict-aliasing ${OPENACC_FLAGS}
ifeq (${CC1},gcc-13)
   LDFLAGS = ${OPENACC_FLAGS} -fno-lto -lm
else
   LDFLAGS = ${OPENACC_FLAGS} -lm
endif

${EXEC}: ${EXEC}.o codelet.o
	$(CC) $(LDFLAGS) $^ -o $@

# Cleanup
clean:
	rm -f *.o ${EXEC}
