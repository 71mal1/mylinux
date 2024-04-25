в конфиге обратить внимание на ссылки к локальным файлам

https://mega.nz/file/1TUCzQSK#QX3NW_ZFv39t4N9kPQuv_3ZVt65_v6Kvn1VrU2OyGzQ

- winecfg -> current_USER -> software -> adobe -> scxs.8
add  PlayerDebugMode (1)


-- config change:
```
function replacement() {
    local filename="replacement.tgz"
    local filemd5="6441a8e77c082897a99c2b7b588c9ac4"
    local filelink="https://victor.poshtiban.io/p/gictor/photoshopCC/replacement.tgz"
    local filepath="/home/a71mal1/Downloads/photoshopCC/$filename"  # Изменение этой строки

    if [ ! -f "$filepath" ]; then
        error "File $filename not found in the specified directory"
        return 1
    fi

    mkdir "$RESOURCES_PATH/replacement"
    show_message "extract replacement component..."
    tar -xzf $filepath -C "$RESOURCES_PATH/replacement"

    local replacefiles=("IconResources.idx" "PSIconsHighRes.dat" "PSIconsLowRes.dat")
    local destpath="$WINE_PREFIX/drive_c/users/$USER/PhotoshopSE/Resources"

    for f in "${replacefiles[@]}";do
        local sourcepath="$RESOURCES_PATH/replacement/$f"
        cp -f "$sourcepath" "$destpath" || error "cant copy replacement $f file..."
    done

    show_message "replace component completed..."
    unset filename filemd5 filelink filepath
}

function install_photoshopSE() {
    local filename="photoshopCC-V19.1.6-2018x64.tgz"
    local filemd5="b63f6ed690343ee12b6195424f94c33f"
    local filepath="/home/a71mal1/Downloads/photoshopCC/$filename"

    if [ ! -f "$filepath" ]; then
        error "File $filename not found in the specified directory"
        return 1
    fi

    mkdir "$RESOURCES_PATH/photoshopCC"
    show_message "extract photoshop..."
    tar -xzf "$filepath" -C "$RESOURCES_PATH/photoshopCC"

    echo "===============| photoshop CC v19 |===============" >> "$SCR_PATH/wine-error.log"
    show_message "install photoshop..."
    show_message "\033[1;33mPlease don't change default Destination Folder\e[0m"

    wine64 "$RESOURCES_PATH/photoshopCC/photoshop_cc.exe" &>> "$SCR_PATH/wine-error.log" || error "sorry something went wrong during photoshop installation"

    show_message "removing useless helper.exe plugin to avoid errors"
    rm "$WINE_PREFIX/drive_c/users/$USER/PhotoshopSE/Required/Plug-ins/Spaces/Adobe Spaces Helper.exe"

    notify-send "Photoshop CC" "photoshop installed successfully" -i "photoshop"
    show_message "photoshopCC V19 x64 installed..."
}
```
