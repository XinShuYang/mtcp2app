DPDK=1
PS=0
NETMAP=0
ONVM=0
CFLAGS=-DMAX_CPUS=20

# DPDK LIBRARY and HEADER
DPDK_INC=/local/onvm/openNetVM/dpdk/x86_64-native-linuxapp-gcc/include
DPDK_LIB=/local/onvm/openNetVM/dpdk/x86_64-native-linuxapp-gcc/lib/

MTCP_FLD    =../../../core/mtcp
MTCP_INC    =-I${MTCP_FLD}/include
MTCP_LIB    =-L${MTCP_FLD}/lib
MTCP_TARGET = ${MTCP_LIB}/libmtcp.a

UTIL_FLD = ../../../core/util
UTIL_INC = -I${UTIL_FLD}/include
UTIL_OBJ = ${UTIL_FLD}/http_parsing.o ${UTIL_FLD}/tdate_parse.o ${UTIL_FLD}/netlib.o


PS_DIR = ../../../core/io_engine/
PS_INC = ${PS_DIR}/include
INC = -I./include/ ${UTIL_INC} ${MTCP_INC} -I${UTIL_FLD}/include
LIBS = ${MTCP_LIB}
ifeq ($(PS),1)
INC += -I{PS_INC}
LIBS += -lmtcp -L${PS_DIR}/lib -lps -lpthread -lnuma -lrt
endif

ifeq ($(NETMAP),1)
LIBS += -lmtcp -lpthread -lnuma -lrt
endif

# CFLAGS for DPDK-related compilation
INC += ${MTCP_INC}
ifeq ($(DPDK),1)
DPDK_MACHINE_FLAGS = $(shell cat /local/onvm/openNetVM/dpdk/x86_64-native-linuxapp-gcc/include/cflags.txt)
INC += ${DPDK_MACHINE_FLAGS} -I${DPDK_INC} -include $(DPDK_INC)/rte_config.h
endif

ifeq ($(shell uname -m),x86_64)
LIBS += -m64
endif

ifeq ($(DPDK),1)
DPDK_LIB_FLAGS = $(shell cat /local/onvm/openNetVM/dpdk/x86_64-native-linuxapp-gcc/lib/ldflags.txt)
#LIBS += -m64 -g -O3 -pthread -lrt -march=native -Wl,-export-dynamic ${MTCP_FLD}/lib/libmtcp.a -L../../dpdk/lib -Wl,-lnuma -Wl,-lmtcp -Wl,-lpthread -Wl,-lrt -Wl,-ldl -Wl,${DPDK_LIB_FLAGS}
LIBS += -g -O3 -pthread -lrt -march=native -export-dynamic ${MTCP_FLD}/lib/libmtcp.a -L../../dpdk/lib -lnuma -lmtcp -lpthread -lrt -ldl ${DPDK_LIB_FLAGS}
else
#LIBS += -m64 -g -O3 -pthread -lrt -march=native -Wl,-export-dynamic ${MTCP_FLD}/lib/libmtcp.a -L../../dpdk/lib -Wl,-lnuma -Wl,-lmtcp -Wl,-lpthread -Wl,-lrt -Wl,-ldl -Wl,${DPDK_LIB_FLAGS}
LIBS += -g -O3 -pthread -lrt -march=native -export-dynamic ${MTCP_FLD}/lib/libmtcp.a -L../../dpdk/lib -lnuma -lmtcp -lpthread -lrt -ldl ${DPDK_LIB_FLAGS}
endif

ifeq ($(ONVM),1)
ifeq ($(RTE_TARGET),)
$(error "Please define RTE_TARGET environment variable")
endif

INC += -I/onvm_nflib
INC += -DENABLE_ONVM
LIBS += /onvm_nflib/$(RTE_TARGET)/libonvm.a
endif
ifeq ($V,) # no echo
	export MSG=@echo
	export HIDE=@
else
	export MSG=@\#
	export HIDE=
endif




CC = gcc -fPIC -g
LDFLAGS = -lm

# set DEBUG options
ifdef DEBUG
CFLAGS = -Wall -Wextra -ggdb -pg -DDEBUG 
else
CFLAGS = -Wall -O2 
endif

#name all the object files
OBJS = server.o  poll.o request.o string.o 

all : epoll

epoll : $(OBJS) ${MTCP_FLD}/lib/libmtcp.a
	$(CC) $(LDFLAGS) ${LIBS} ${UTIL_OBJ} -o server $^

debug :
	make all DEBUG=1

%.o : %.c
	$(CC) $(CFLAGS) -o $@ -c $^


clean :
	rm -rf $(OBJS) server



