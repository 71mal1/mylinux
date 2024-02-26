/usr/share/themes/

/usr/share/icons/

 git clone https://gitlab.com/kalilinux/packages/kali-themes 
sudo mkdir -p ~/.local/share/themes; mv ~/kali-themes/share/themes/* ~/.local/share/themes
sudo mkdir -p ~/.local/share/themes
sudo mv ~/kali-themes/share/themes/* ~/.local/share/themes
sudo cp -R /home/71mal1/kali-themes/share/icons/* /usr/share/icons/
gtk-update-icon-cache /usr/share/icons/
gtk-update-icon-cache /usr/share/icons/Adwaita+Flat-Remix-Blue/
sudo gtk-update-icon-cache /usr/share/icons/Adwaita+Flat-Remix-Blue/
sudo gtk-update-icon-cache /usr/share/icons/Flat-Remix-Blue-Dark/
sudo gtk-update-icon-cache /usr/share/icons/Flat-Remix-Blue-Light/
sudo gtk-update-icon-cache /usr/share/icons/Flat-Remix-Purple-Dark/
sudo gtk-update-icon-cache /usr/share/icons/Flat-Remix-Purple-Light/


panel: xfconf-query -c xfce4-panel -l | xargs -I {} xfconf-query -c xfce4-panel -p {} > panel-settings.xfconf

импорт
xfconf-query -c xfce4-panel -R
xfconf-query -c xfce4-panel -f panel-settings.xfconf

config /home/71mal1/.config/xfce4/

