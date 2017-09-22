# Installing EOS on a Windows computer

However the MS Visual Studio is a natural choice for a Windows C++ practitioner, it is not an option here, rather. 

We have tried. The main problem is that, while the eos code is branched with the `WIN32` flag, it is done inconsistently. Also, the *Windows* and *Unix* *clang* compilers are not equal: the *Windows* one is more restrictive. For example expressions like  `std::move(u8"env")` are invalid there (in Windows `u8"env"` does the same).

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
    
After installation the Linux distribution will be located at: `%localappdata%\lxss\`.

It may happen later that would like to clean the Ubuntu. Open a Windows command prompt:
1. lxrun /uninstall /full
2. lxrun /install /y

You may dislike the dark-blue output of the *bash*. If so, open the menu ot the *Windows Command Prompt*.
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

1. Make a workspace for EOS on the Windows file system, to control it from both systems. Let it be `E:\Workspaces\EOS', for example. On the Linux file system it is `/mnt/e/Workspaces/EOS`.

2. Add to `~/.profile`: 
`EOS_HOME=/mnt/e/Workspaces/EOS` and `EOS_PROGRAMS=$EOS_HOME/eos/build/programs`.
Better do it with the nano editor: `nano ~/.bashrc`

3. Do clean install Ubuntu

```bash    
cd $EOS_HOME
git clone https://github.com/eosio/eos --recursive
cd eos
./build.sh ubuntu 
```

## That is all

Now, you have the EOS code in your *Windows 10* computer, compiled resulting with  libraries and executables. Executables are placed in the `$EOS_PROGRAMS` folder:
* eosd - server-side blockchain node component
* eosc - command line interface to interact with the blockchain
* eos-walletd - EOS wallet
* launcher - application for nodes network composing and deployment; [more on launcher](https://github.com/EOSIO/eos/blob/master/testnet.md)

You can run them in many ways, but not together:
* With the *Visual Studio Code* terminal, for example:
```bash
cd $EOS_PROGRAMS/eosd
./eosd
```
* With the *Bush On Ubuntu on Windows* prompt that can be launched from the *Start Up* menu:
```bash
cd $EOS_PROGRAMS/eosd
./eosd
``` 
* With the *Windows Command Prompt*:
```bash
bash
cd $EOS_PROGRAMS/eosd
./eosd
```
Now, you can do tests described in eos/README.md.