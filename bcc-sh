#!/bin/bash
# bcc: (b)uild packages in (c)lean (c)hroot 2023-02-15
# depends: base-devel devtools expac asp git auracle-git

# Chroot location: /var/lib/aurbuild/x86_64/root
# Built Packages:  /tmp/bcc/<package>

#----------------------------------------------------------------------
CHROOT=/var/lib/archbuild/extra-x86_64/
P="${2}"
basedir="/tmp/bcc" 		; [[ ! -d ${basedir} ]] && mkdir -p "${basedir}"
builddir="${basedir}"/"${P}"
#----------------------------------------------------------------------
help (){
	echo "
 bcc: Builds official and AUR repo packages in a clean chroot.

 Usage: bcc [operation] <package-name>
 Operations:
	-f  = Fetch Build Requirements
	-b  = Build Package
	-h  = Help

 Run 'bcc -f <package-name>' to fetch the build requirements before building.

 Chroot location:	/var/lib/archbuild/*
 Build location:	/tmp/bcc/
 Built packages:	/tmp/bcc/<package-name>/<package-name>-<version>-<arch>.pkg.tar.zst
"
}
#----------------------------------------------------------------------
buildachroot(){

if	[[ ! -d "${CHROOT}"/root ]]; then
	echo "::
 	Building an Arch chroot.
	"
	sudo mkdir -p "${CHROOT}"	
	mkarchroot "${CHROOT}"/root base-devel
fi
}
#----------------------------------------------------------------------
### Set official or AUR package ###

setrepo (){

if	pkg=$(auracle search ^"${P}"$ 2>/dev/null) && [[ -n ${pkg} ]] ; then
	repo=AUR
   elif	expac -S '%n' "${P}" &>/dev/null ; then
	repo=OFFICIAL
   else
	echo " Package NA "
	exit
fi
}
#----------------------------------------------------------------------
### Fetch build requirements ###

fetch (){

	cd "${basedir}" || exit

if	find "${builddir}" -maxdepth 0 -type d  &>/dev/null; then
	cd "${builddir}" || exit
	git pull
	exit
fi
if	[[ ${repo} == OFFICIAL ]] ; then
	asp checkout "${P}"
	cd "${builddir}" || exit
	dir=$(find repos/*/PKGBUILD | awk -F/ '{print $1"/"$2"/"}' | grep -v testing)
	cd "${dir}" || exit
	asp update "${P}"
	git pull
	echo 'true' >"${builddir}"/Fetch || error_message
    elif
	[[ ${repo} == AUR ]] ; then
	auracle clone "${P}"
	cd "${builddir}" || exit
	echo 'true' >"${builddir}"/Fetch || error_message

fi
}
#----------------------------------------------------------------------
### Build with makepkg wrapped in devtools script ###

build (){

	Fetch=$(cat "${builddir}/Fetch" 2>/dev/null)

if	[[ ${Fetch} != true ]]; then
	echo "Need to fetch build requirements before building."
	echo "Printing help page..."
	bcc -h
	 exit
fi
if	[[ ${Fetch} == true ]]; then

	if	[[ ${repo} == OFFICIAL ]] ; then
		cd "${builddir}" || exit
		dir=$(find repos/*/PKGBUILD | awk -F/ '{print $1"/"$2"/"}' | grep -v testing| head -1)
		cd "${dir}" || exit
		extra-x86_64-build |& tee >(grep 'right now' > right.now 2>&1)  | grep -v 'right now'
		awk '{$2="NOTE:" ; print $1,$2,$3,$4,$5,$6,$7,$8" same as the "$9,$10,$11,$12"s "$13,$14}' right.now
		pkg=$(find  -type f -name "*.pkg.tar.*" | grep -v '.log' | tail -1)
		echo " sudo may be required if timed out, to move built package from chroot to /tmp/bcc/<package>."
		sudo mv "${pkg}" "${builddir}"
	    elif
		[[ ${repo} == AUR ]] ; then
		buildachroot
		arch-nspawn "${CHROOT}"/root pacman -Syu
		cd "${builddir}" || exit
		makechrootpkg -c -r "${CHROOT}"
	fi
fi
}
#----------------------------------------------------------------------
### Error message ###

error_message () {
echo "
:: Something has gone horribly wrong!
"
}
#----------------------------------------------------------------------
### Run help on no arguments ###
 
	[[ -z $1 ]] && help
#======================================================================
while	:; do
	case "${1}" in
	  -f)	setrepo ; fetch				;;
	  -b)	setrepo ; build	|| error_message	;;
	  -h)	help		 			;;
	  *)	break					;;
	esac
    shift
done
