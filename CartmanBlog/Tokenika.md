# Installing EOS on a remote Linux computer

## Tooling up

### Enhanced terminal for Windows

Download [MobaXterm](#http://mobaxterm.mobatek.net/download.html). It is much easer than *PuTTY*: it can open GUI applications.

## Set up a workspace

1. Upgrade Ubuntu:
```bash
sudo apt upgrade
sudo apt install nedit
```
2. Make a workspace for EOS. Let it be `~/Workspaces/EOS`, for example.

3. Make environment variables, defining the workspace:
```bash
export EOS_HOME=~/Workspaces/EOS 
echo "EOS_HOME=~/Workspaces/EOS" >> ~/.bashrc
export EOS_PROGRAMS=${EOS_HOME}/eos/build/programs
echo "EOS_PROGRAMS=${EOS_HOME}/eos/build/programs" >> ~/.bashrc
source ~/.bashrc
``
4. Do clean install Ubuntu

```bash
export BOOST_ROOT=${HOME}/opt/boost_1_64_0 >> ~/.bashrc
echo "export BOOST_ROOT=${HOME}/opt/boost_1_64_0" >> ~/.bashrc
source ~/.bashrc
export TEMP_DIR=/tmp

cd $EOS_HOME
git clone https://github.com/eosio/eos --recursive
cd eos && ./build.sh ubuntu full
```

## All is ready now

Now, you have the EOS code in your *Windows 10* computer, compiled resulting with  libraries and executables. Executables are placed in the `$EOS_PROGRAMS` folder:
* eosd - server-side blockchain node component
* eosc - command line interface to interact with the blockchain
* eos-walletd - EOS wallet
* launcher - application for nodes network composing and deployment; [more on launcher](https://github.com/EOSIO/eos/blob/master/testnet.md)

Now, you can do tests described in eos/README.md. For completeness, let us prove that eos can be started.

```bash 
cd ${EOS_PROGRAMS}/eosd
./eosd
```
If *eosd* does not exit with an error, close it immediately with <kbd>Ctrl-C</kbd>.

Open *nedit* GUI editor:

```bash
nedit ${EOS_PROGRAMS}/eosd/
```

Edit *data-dir/config.ini*, appending the following text (be careful to set the proper value for the `genesis-json` path, and comment out the original `enable-stale-production` definition):

```
genesis-json = /mnt/e/Workspaces/EOS/eos/genesis.json # !!!!!!!!!!!!!!!!!
enable-stale-production = true

producer-name = inita
producer-name = initb
producer-name = initc
producer-name = initd
producer-name = inite
producer-name = initf
producer-name = initg
producer-name = inith
producer-name = initi
producer-name = initj
producer-name = initk
producer-name = initl
producer-name = initm
producer-name = initn
producer-name = initq
producer-name = initr
producer-name = inits
producer-name = initt
producer-name = initu
# Load the block producer plugin, so you can produce blocks
plugin = eos::producer_plugin
# Wallet plugin
plugin = eos::wallet_api_plugin
# As well as API and HTTP plugins
plugin = eos::chain_api_plugin
plugin = eos::http_plugin 
```

## Update EOS

```bash
cd $EOS_HOME/eos
git pull

cd

cmake -DCMAKE_BUILD_TYPE=Release \
-DCMAKE_C_COMPILER=clang-4.0 \
-DCMAKE_CXX_COMPILER=clang++-4.0 \
-DWASM_LLVM_CONFIG=${HOME}/opt/wasm/bin/llvm-config \
-DBINARYEN_BIN=${HOME}/opt/binaryen/bin \
-DOPENSSL_ROOT_DIR=/usr/local/opt/openssl \
-DOPENSSL_LIBRARIES=/usr/local/opt/openssl/lib \
../
make
```