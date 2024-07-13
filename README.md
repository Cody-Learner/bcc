# Build in Clean Chroot

bcc: <br>
Builds Arch linux AUR and official packages in a clean chroot using the official Arch devtools package.<br>
The 'devtools-info' and 'devtools-scripts' files provide information on the devtools package.<br>
This was preliminary work leading up to writing my 'aurch' AUR helper.<br>
<br>
Note: dependencies must be installed prior to using this script.<br>
Dependencies: base-devel devtools expac asp git auracle-git<br>
<br>
<br>
 Usage: bcc [operation] \<package-name\> <br>
 Operations: <br>
	-f  = Fetch Build Requirements <br>
	-b  = Build Package <br>
	-h  = Help <br>
<br>
 Run 'bcc -f <package-name>' to fetch the build requirements before building.<br>
<br>
 Chroot location:	/var/lib/archbuild/* <br>
 Build location:	/tmp/bcc/ <br>
 Built packages:	/tmp/bcc/\<package-name\>/\<package-name\>-\<version\>-\<arch\>.pkg.tar.zst <br>
<br>
<br>
**News for July 13, 2024:**<br>
This script has not worked for official packages since Arch migrated packages to git long ago now.
However, it still works well for AUR packages and I continue to use it occasionally.

**Updated for Jan 15, 2023:**<br>
Ran a test build successfully on AUR python2: https://aur.archlinux.org/packages/python2 <br>
Cleaned up this README.md file and clarify help and comments in the script.<br>
<br>