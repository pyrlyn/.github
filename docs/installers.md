# Install, update and uninstall

Required behavior for pyrlyn install scripts and for tools that install programs. The same
rules apply to the tool's own installer (`install.sh`, `install.ps1`) and to anything it
installs for the user.

## Install

- Not installed: install it.
- Already installed: ask "update?".
  - Yes: run the update below.
  - No: exit without changes.
  - No newer version available: print that the program is already installed and cannot be
    installed again, and exit without changes.

## Update

Delete the program's install folder completely, then install the new version fresh. Never
patch files in place or merge old and new files. User data and configuration that live
outside the install folder are kept.

## Uninstall

- Delete the install folder.
- On Windows, also remove the registry entry the install created (the uninstall entry under
  `Software\Microsoft\Windows\CurrentVersion\Uninstall`, and any other key the installer
  wrote).
- Program not installed: print only "program not found" and exit, with no other output and no
  changes.
