# Custom Desktop Shortcut & Launcher

## Custom Desktop Shortcut

First, create a new text file with extension .desktop:

```
[Desktop Entry]
Type=Application
Terminal=true
Name=UploadSync
Icon=utilities-terminal
Exec=gnome-terminal -e "bash -c '~/bin/upload.bash;$SHELL'"
Categories=Application
```

Change the arguments according to your choice. Save the file on the desktop. After that right click the icon and in the menu that arises select “Allow Launching” to make it runable.

## Custom Launcher

Move the previous `.desktop` file to `~/.local/share/applications` and look for the app in the app drawer. 