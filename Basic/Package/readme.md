https://wiki.archlinux.org/title/Pacman_(%D0%A0%D1%83%D1%81%D1%81%D0%BA%D0%B8%D0%B9)/Tips_and_tricks_(%D0%A0%D1%83%D1%81%D1%81%D0%BA%D0%B8%D0%B9)#%D0%A1%D0%BF%D0%B8%D1%81%D0%BE%D0%BA_%D1%83%D1%81%D1%82%D0%B0%D0%BD%D0%BE%D0%B2%D0%BB%D0%B5%D0%BD%D0%BD%D1%8B%D1%85_%D0%BF%D0%B0%D0%BA%D0%B5%D1%82%D0%BE%D0%B2

pacman -Qqe -n > pkglist.txt
pacman -Qqem > foreignpkglist.txt 

sudo pacman -S --needed - < pkglist.txt
yay -S --needed - < foreignpkglist.txt 


