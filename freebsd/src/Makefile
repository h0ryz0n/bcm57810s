
SYSDIR  ?= ${SRCTOP}/sys

BXE     = ${SYSDIR}/dev/bxe
.PATH:  ${BXE}

KMOD    = if_bxe
SRCS    = device_if.h bus_if.h pci_if.h
SRCS    += bxe.c               \
           bxe_stats.c         \
           bxe_debug.c         \
           bxe_elink.c         \
           ecore_sp.c          \
           57710_init_values.c \
           57711_init_values.c \
           57712_init_values.c

#CFLAGS += -I${BXE}
#bypasses the compiler error: "undeclared function 'bxe_ilog2'; ISO C99 and later do not support implicit function declarations [-Werror,-Wimplicit-function-declaration]"
CFLAGS += -I${BXE} -Wno-error=implicit-function-declaration

.include <bsd.kmod.mk>
CWARNFLAGS+=    ${NO_WUNUSED_BUT_SET_VARIABLE}
