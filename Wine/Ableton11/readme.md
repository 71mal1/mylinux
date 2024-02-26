Install ableton

export WINEARCH=win64
export WINEPREFIX=~/.wine64.ableton
mkdir -p "$WINEPREFIX"

- run wine and choose Windows 7
	wine64 winecfg
	
cd "$WINEPREFIX"

winetricks gdiplus msxml3 msxml4 msxml6 vcrun2005 quicktime72 fontsmooth=rgb msls31 d3dcompiler_43 corefonts atmlib

- unzip the installer go inside the installer folder run the installer
export WINEARCH=win64
export WINEPREFIX=~/.wine64.ableton
wine64 Ableton\ Live\ 11\ Suite\ Installer.exe

- run Ableton Live
export WINEARCH=win64
export WINEPREFIX=~/.wine64.ableton
wine64 explorer.exe

- переместить из crack файл .exe

- rename 
- /home/71mal1/.wine64.ableton/drive_c/ProgramData/Ableton/Live 11 Suite -> /home/71mal1/.wine64.ableton/drive_c/ProgramData/Ableton/Live
- /home/71mal1/.wine64.ableton/drive_c/ProgramData/Ableton/Live/Program/Ableton Live 11 Suite.exe -> /home/71mal1/.wine64.ableton/drive_c/ProgramData/Ableton/Live/Program/Ableton.exe

- Первый запуск без интернета, keygen
- для запуска без интернета создать группу. отключить авто обновления в настройках
(sg no-internet -c "WINEPREFIX='/home/71mal1/.wine64.ableton' wine '/home/71mal1/.wine64.ableton/drive_c/ProgramData/Ableton/Live/Program/Ableton.exe'")
После можно запускать с ярлыка

https://mega.nz/file/ECVjzShS#g8MAavE69iQxVe2TLJyhcV_Boae0MqfIklvaq6pFwAY

