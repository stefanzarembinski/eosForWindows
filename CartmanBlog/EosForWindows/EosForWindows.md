# Installing EOS on a Windows computer

However the MS Visual Studio is a natural choice for a Windows C++ practitioner, it is not an option here, rather. 

We have tried. The main problem is that, while the eos code is branched with the `WIN32` flag, it is done inconsistently. Also, the *Windows* and *Unix* *clang* compilers are not equal: the *Windows* one is more restrictive. For example expressions like  `std::move(u8"env")` are invalid there (in Windows, `u8"env"` does the same).

The *plan B* solution is the [Windows Subsystem for Linux](#https://msdn.microsoft.com/en-us/commandline/wsl/about) implementing the *Linux* *bash*, combined with the [Visual Studio Code](#https://code.visualstudio.com/). Prerequisite is *64-bit version of Windows 10 Anniversary Update or later (build 1607+)*

Now, we see that the alternative is not worse, or even better than the first guess.

## Tooling up

### Windows Subsystem for Linux
The *Windows Subsystem for Linux* can be only installed on the system drive. If there is not enough space on your computer there - at least several GB is needed - you can rearrange the partition by means of a tool sold [there](#https://www.partition-tool.com/).

An installation guide is [there](#https://msdn.microsoft.com/en-us/commandline/wsl/install_guide). Here, we cite a simplified procedure; there is another, conditional option [there](#https://msdn.microsoft.com/en-us/commandline/wsl/install_guide).
1. Open *PowerShell* *as Administrator* and run:
```
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
```
2. Restart your computer when prompted.
3. Turn on Developer Mode: 
    * Open Settings -> Update and Security -> For developers 
    * Select the Developer Mode radio button.
4. Open a Windows command prompt. 
    * Run bash. 
    ```bash
    C:\>bash
        ....
        and licensed under its terms available here:
        ....
    Type "y" to continue:
    ```
    * After you have accepted the License, the Ubuntu user-mode image will be downloaded and extracted. A "Bash on Ubuntu on Windows" shortcut will be added to your start menu.
5. Launch a new Ubuntu shell by either:
    * Running bash from a command-prompt
    * Clicking the start menu shortcut
6. Upgrade Ubuntu:
```bash
sudo apt upgrade
```
    
After installation the Linux distribution will be located at: `%localappdata%\lxss\`.

It may happen later that would like to clean the Ubuntu. Open Windows *Command Prompt*:
1. lxrun /uninstall /full
2. lxrun /install

You may dislike the dark-blue output of the *bash*. If so, open the menu ot the Windows *Command Prompt*.
1. Select *Defaults > Popup Text*. Set *Selected Color Values* \[190\]\[190\]\[256\].
2. Select *Defaults > Popup Background*. Set *Selected Color Values* \[0\]\[0\]\[0\].
3. Do the same selecting *Properties*. 

### Visual Studio Code
1. Download a windows version from [there](#https://code.visualstudio.com/Download).

2. Start Visual Studio code. Modify User Settings in VS Code:
    * File => Preferences => User Settings
    * and add the following within the settings.json pane:
“terminal.integrated.shell.windows”: “C:\\Windows\\sysnative\\bash.exe”
    * You can now toggle the terminal view with ``CTRL+` `` or `View => Toggle Integrated Terminal`
3. Consider adding extensions that can be useful for C++ development:
    * `Ctr + Shift + X` to open the EXTENSIONS panel.
    * C/C++
    * C++ Intelisense
    * CMakeTools
    * CMake Tools Helper
    * Code Runner
    * Visual Studio Team

## Set up a workspace

1. Make a workspace for EOS on the Windows file system, to control it from both systems. Let it be `E:\Workspaces\EOS', for example. On the Linux file system, it is `/mnt/e/Workspaces/EOS`.

2. Make environment variables, defining the workspace:

```bash
export EOS_HOME=/mnt/e/Workspaces/EOS && \
echo "export EOS_HOME=/mnt/e/Workspaces/EOS" >> ~/.bashrc && \
export EOS_PROGRAMS=${EOS_HOME}/eos/build/programs && \
echo "export EOS_PROGRAMS=${EOS_HOME}/eos/build/programs" >> ~/.bashrc && \
source ~/.bashrc
```

3. Do clean install Ubuntu

```bash
# Try with a clean installation:
cmake --version
# if not installed, then
sudo apt install cmake 
# else remove this issue.

export BOOST_ROOT=${HOME}/opt/boost_1_64_0 && \
echo "export BOOST_ROOT=${HOME}/opt/boost_1_64_0" >> ~/.bashrc && \
source ~/.bashrc && \ # reload .bashrc
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
cd $EOS_PROGRAMS/eosd
./eosd
```
If *eosd* does not exit with an error, close it immediately with <kbd>Ctrl-C</kbd>.

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
Start *eosd* again, it should start block production. It happens that is hangs at the first attempt. Do <kbd>Ctrl-C</kbd>, wait, and try again.

You can run *eosd*, as well as other executables, in mother any ways, but not in the same time:

* With the *Bush On Ubuntu on Windows* prompt that can be launched from the *Start Up* menu:
```bash
cd $EOS_PROGRAMS/eosd
./eosd
``` 
* With the Windows *Command Prompt*:
```bash
bash
cd $EOS_PROGRAMS/eosd
./eosd
```
## If you need to update EOS...

```bash
cd $EOS_HOME/eos
git pull

rm -r build && mkdir build && cd build

cmake -DCMAKE_BUILD_TYPE=Debug \
-DCMAKE_C_COMPILER=clang-4.0 \
-DCMAKE_CXX_COMPILER=clang++-4.0 \
-DWASM_LLVM_CONFIG=${HOME}/opt/wasm/bin/llvm-config \
-DBINARYEN_BIN=${HOME}/opt/binaryen/bin \
-DOPENSSL_ROOT_DIR=/usr/local/opt/openssl \
-DOPENSSL_LIBRARIES=/usr/local/opt/openssl/lib \
../
make
```