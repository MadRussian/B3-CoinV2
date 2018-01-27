AM_CPPFLAGS = \
	$(BOOST_CPPFLAGS) \
	$(HARDENED_CPPFLAGS)

AM_CXXFLAGS = \
	$(HARDENED_CXXFLAGS)

AM_LDFLAGS = \
	$(BOOST_LDFLAGS) \
	$(HARDENED_LDFLAGS)


B3COIN_INCLUDES= \
	$(BDB_CPPFLAGS) \
	$(BOOST_CPPFLAGS) \
	$(LEVELDB_CPPFLAGS) \
	$(CRYPTO_CFLAGS) \
	$(SSL_CFLAGS)

bin_PROGRAMS = \
	b3coind

leveldb/libleveld.a:
	cd leveldb; make libleveldb.a libmemenv.a; cd ..;


b3coind_SOURCES = \
  bitpool.cpp \
  fn-activity.cpp \
	fn-config.cpp \
	fn-manager.cpp \
	fn-service.cpp \
	spork.cpp \
  fundamentalnode.cpp \
  rpcfundamentalnode.cpp \
  alert.cpp \
  version.cpp \
  checkpoints.cpp \
  netbase.cpp \
  addrman.cpp \
  crypter.cpp \
  key.cpp \
  init.cpp \
  bitcoind.cpp \
  keystore.cpp \
  core.cpp \
  main.cpp \
  net.cpp \
  protocol.cpp \
  rpcclient.cpp \
  rpcprotocol.cpp \
  rpcserver.cpp \
  rpcmisc.cpp \
  rpcnet.cpp \
  rpcblockchain.cpp \
  rpcrawtransaction.cpp \
  timedata.cpp \
  script.cpp \
  sync.cpp \
  txmempool.cpp \
  util.cpp \
  hash.cpp \
  noui.cpp \
  kernel.cpp \
  pbkdf2.cpp \
  scrypt.cpp \
  chainparams.cpp \
	txdb-leveldb.cpp \
	db.cpp \
	miner.cpp \
	rpcdump.cpp \
	rpcmining.cpp \
	rpcwallet.cpp \
	wallet.cpp \
	walletdb.cpp

b3coind_CPPFLAGS = \
	$(AM_CPPFLAGS) \
	$(B3COIND_INCLUDES)

b3coind_CXXFLAGS = \
	$(AM_CXXFLAGS) \
	-Ileveldb/include \
	-Ileveldb/helpers \
	-DENABLE_WALLET

b3coind_LDFLAGS = \
	$(AM_LDFLAGS) \
	-Wl,-Bdynamic

b3coind_LDADD = \
	$(LIBLEVELDB) \
	$(BOOST_LIBS) \
	$(BDB_LIBS) \
	-lpthread \
	$(SSL_LIBS) \
	$(CRYPTO_LIBS) \
	$(EVENT_PTHREAD_LIBS) \
	$(EVENT_LIBS) \
	-lz \
	-ldl \
	-ldb_cxx \
	leveldb/libleveldb.a \
	leveldb/libmemenv.a

leveldb/libleveldb.a:
	@echo "Building LevelDB ..."; cd leveldb; make libleveldb.a; cd ..;

leveldb/libmemenv.a:
	@echo "Building LevelDB libmemenv ..."; cd leveldb; make libmemenv.a; cd ..;


scrypt-arm.o: scrypt-arm.S
	$(CC) -c $(CFLAGS) -MMD -o $@ $<

all: b3coind

