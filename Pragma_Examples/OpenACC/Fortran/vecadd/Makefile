EXEC = vecadd
default: ${EXEC}
all: ${EXEC}

ROCM_GPU ?= $(strip $(shell rocminfo |grep -m 1 -E gfx[^0]{1} | sed -e 's/ *Name: *//'))

FC1=$(notdir $(FC))

ifeq ($(findstring, flang, $(FC1)), flang)
  OPENACC_FLAGS = Flang_does_not_support_OpenACC_for_AMD
  FREE_FORM_FLAG = -Mfreeform
else ifeq ($(findstring, amdflang, $(FC1)), amdflang)
  OPENACC_FLAGS = amdflang_does_not_support_OpenACC_for_AMD
  FREE_FORM_FLAG = -Mfreeform
else
  OPENACC_FLAGS = -fopenacc -foffload=-march=${ROCM_GPU} -fopt-info-optimized-omp
  FREE_FORM_FLAG = -ffree-form
endif

FFLAGS = -g -O3 ${FREE_FORM_FLAG} -fstrict-aliasing ${OPENACC_FLAGS}
ifeq (${FC1},gfortran-13)
   LDFLAGS = ${OPENACC_FLAGS} -fno-lto
else
   LDFLAGS = ${OPENACC_FLAGS}
endif

ftimers.o: timers.o
vecadd.o: ftimers.o

${EXEC}: ${EXEC}.o timers.o ftimers.o
	$(FC) $(LDFLAGS) $^ -o $@

# Cleanup
clean:
	rm -f *.o ${EXEC} *.mod
