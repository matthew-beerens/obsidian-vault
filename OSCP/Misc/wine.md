
Install wine and wine32

`sudo apt install wine`
`dpkg --add-architecture i386 && apt-get update && apt-get install wine32`


fix wine config for could not load kernel32.dll error - RUN IN ROOT 

```bash
mkdir -p ~/myapp/prefix

export WINEPREFIX=$HOME/myapp/prefix 

export WINEARCH=win32

export WINEPATH=$HOME/myapp

wineboot --init

winetricks // not needed
```

delete ~/.wine and /tmp

if more issues persist make sure system is up to date
