# Installing EOS on a remote Linux computer

## Tooling up

### [Install Visual Studio Code](#https://code.visualstudio.com/docs/setup/linux)

```
cd \tmp
wget -O code.deb https://go.microsoft.com/fwlink/?LinkID=760868
sudo dpkg -i code.deb
sudo apt-get install -f # Install dependencies
```
This will automatically install the apt repository and signing key to enable auto-updating using the regular system mechanism.

Then update the package cache and install the package:
```
sudo apt-get update
sudo apt-get install code # or code-insiders
```
Missing libasound2 shared library.
```
sudo apt-get install libasound2
```
Visual Studio Code binaries are there: `/user/share/code`

Add to `~/.profile`: `PATH="$PATH:\usr\share\code" # Visual Studio Code`. This can be done with nano text editor: `nano ~/.bashrc`.

Also, add `EOS_HOME=~/Workspaces/EOS` and `EOS_PROGRAMS=$EOS_HOME/eos/build/programs` to the `~/.bashrc`.

## X Window System Server for Windows

[MobaXterm](#http://mobaxterm.mobatek.net/)

I do not know now, whether this is useful: *Visual Studio Code* is not stable here. And, we do not use it for installing EOS. But, perhaps, it can be useful for production.

## Set up a workspace

1. Make a workspace for EOS. Let it be `~/Workspaces/EOS`, for example.

2. Add to `~/.profile`: 
`EOS_HOME=~/Workspaces/EOS` and `EOS_PROGRAMS=$EOS_HOME/eos/build/programs`.
This can be done with nano text editor: `nano ~/.bashrc`

3. Do clean install Ubuntu

```bash    
cd $EOS_HOME
git clone https://github.com/eosio/eos --recursive
cd eos
./build.sh ubuntu 
```