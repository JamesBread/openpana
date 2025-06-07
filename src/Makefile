# Makefile for OpenPANA src
CC = gcc
CFLAGS = -std=c99 -g -Wall -O2 -I. -Istate_machines -Ilibeapstack -I../src/wpa_supplicant/src -I../src/wpa_supplicant/src/utils `xml2-config --cflags`
CPPFLAGS = -DCONFIGDIR=\"/etc/openpana\"
LDFLAGS = -lpthread -lcrypto `xml2-config --libs`

# Common source files
COMMON_SRCS = state_machines/session.c state_machines/statemachine.c prf_plus.c panamessages.c lalarm.c panautils.c loadconfig.c aes.c cmac.c

# Server sources
SERVER_SRCS = $(COMMON_SRCS) state_machines/paamachine.c mainserver.c
SERVER_OBJS = $(SERVER_SRCS:.c=.server.o)

# Client sources  
CLIENT_SRCS = $(COMMON_SRCS) state_machines/pacmachine.c mainclient.c
CLIENT_OBJS = $(CLIENT_SRCS:.c=.client.o)

# Pre sources
PRE_SRCS = mainpre.c loadconfig.c panautils.c panamessages.c aes.c
PRE_OBJS = $(PRE_SRCS:.c=.pre.o)

# Targets
TARGETS = openpaa openpac openpre

all: libeapstack/libeap.a $(TARGETS)

libeapstack/libeap.a:
	cd libeapstack && $(MAKE) -f Makefile.real

openpaa: $(SERVER_OBJS) libeapstack/libeap.a
	$(CC) $(SERVER_OBJS) libeapstack/libeap.a -Wl,--start-group wpa_supplicant/src/tls/libtls.a wpa_supplicant/src/utils/libutils.a wpa_supplicant/src/crypto/libcrypto.a -Wl,--end-group -o $@ $(LDFLAGS)

openpac: $(CLIENT_OBJS) libeapstack/libeap.a  
	$(CC) $(CLIENT_OBJS) libeapstack/libeap.a -Wl,--start-group wpa_supplicant/src/tls/libtls.a wpa_supplicant/src/utils/libutils.a wpa_supplicant/src/crypto/libcrypto.a -Wl,--end-group -o $@ $(LDFLAGS)

openpre: $(PRE_OBJS)
	$(CC) $(PRE_OBJS) -o $@ $(LDFLAGS)

# Server object compilation
%.server.o: %.c
	$(CC) $(CFLAGS) $(CPPFLAGS) -DISSERVER -c $< -o $@

# Client object compilation
%.client.o: %.c
	$(CC) $(CFLAGS) $(CPPFLAGS) -DISCLIENT -c $< -o $@

# Pre object compilation
%.pre.o: %.c
	$(CC) $(CFLAGS) $(CPPFLAGS) -DISPRE -c $< -o $@

clean:
	rm -f $(SERVER_OBJS) $(CLIENT_OBJS) $(PRE_OBJS) $(TARGETS)
	cd libeapstack && $(MAKE) -f Makefile.real clean

install: all
	mkdir -p $(DESTDIR)/usr/local/bin $(DESTDIR)/etc/openpana
	cp $(TARGETS) $(DESTDIR)/usr/local/bin/
	cp config.xml $(DESTDIR)/etc/openpana/

.PHONY: all clean install