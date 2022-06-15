#!/usr/bin/perl


use strict;
use warnings;
#use Data::Dumper;
#$Data::Dumper::Maxdepth = 1;
use AptPkg::Cache;
use autodie;
use List::Util qw/uniq/;


#<config>
my $package_name = $ARGV[0] || 'kate';
my $target_arch = 'amd64';
#</config>

my $cache = AptPkg::Cache->new();

sub get_pkgname_depends_list{
  my ($_pkgname) = @_;
  if(!defined($_pkgname)){
    warn "WARNING: package name is undefined........\n";
    return ();
  }
  if(length($_pkgname) == 0){
    warn "WARNING: package name is empty...........\n";
    return ();
  }
  my $version_list = $cache->get($_pkgname)->{'VersionList'};
  if(!defined($version_list)){
    warn "WARNING: Version list for package name $_pkgname is undefined.\n";
    return ();
  }
  if(scalar(@{$version_list}) == 0){
    warn "WARNING: Version list for package name $_pkgname is empty.\n";
    return ();
  }
  my $dependencies = $version_list->[0]->{'DependsList'};
  if(!defined($dependencies)){
    warn "WARNING: Dependencies list for package name $_pkgname is undefined.\n";
    return ();
  }
  if(scalar(@{$dependencies}) == 0){
    warn "WARNING: Dependencies list for package name $_pkgname is empty.\n";
    return ();
  }
  return @{$dependencies};
}


my @dependencies = get_pkgname_depends_list($package_name);
my @package_names = ($package_name);
while(1){
  $_ = shift(@dependencies) or last;
  if($_->{'DepType'} ne 'Depends'){next;}
  my $target_pkg = $_->{'TargetPkg'};
  if($target_pkg->{'Arch'} ne $target_arch){next;}
  if($target_pkg->{'CurrentState'} eq 'Installed'){next;}
  push(@package_names, $target_pkg->{'Name'});
  push(@dependencies, get_pkgname_depends_list($target_pkg->{'Name'}));
}

print 'sudo apt download ', join(' ', uniq(sort(@package_names))), "\n";
#!/bin/bash

#this is a 10%-31% comprehensive debian package management reference.
#see the man packages for apt apt-cache apt-file apt-get apt-mark dpkg dpkg-query 
#and debian-reference-en for further inquiry.

#This program just uses `grep` and `egrep` as the only external commands.
#The rest is pure bash `echo`, `test` 


#run with `bash -n` (lint/safe-run) first if you make any changes
#just to make sure the quotes don't mess up

#somebody better make this a HEREDOC...although it'll make the source -3% easier to read.

if [ -z "$1" ]; then
echo '
apt-safe $command

add-repository   - Add entries to apt sources.list
auto             - Set this installed $package_name to be auto-removed (ie. assuming you went `sudo apt autoremove`) iff the explicitely installed (ie. `sudo apt install $explicitely_installed_package`) is `sudo apt purge $explicitely_installed_package`
autoclean        - Erase downloaded archive files from /var/cache/apt/archives/*.deb basically except for the ones that are identical to the ones in your live repository /etc/apt/sources.list*
autopurge        - Remove packages with their configuration files and automatically remove all unused packages (always good after a good `sudo apt purge $package_name` or `sudo dpkg --remove $package_name`)
autoremove       - Remove automatically all unused packages (always good after a good `sudo apt purge $package_name` or `sudo dpkg --remove $package_name`)
build            - Download source code for a package into current directory, extract, and build it. `sudo apt builddep $package_name` (? or is it source name?) (to get the source-building-dependency packages necessary a priori building otherwise hope for the best)
build-dep        - Configure build-dependencies for source packages; to build source code you will require the necessary packages -- they shall be installed
changelog        - View a packagez changelog
check            - Verify that there are no broken dependencies; same as `sudo apt -f install` (with no other arguments)
clean            - Erase downloaded archive files from /var/cache/apt/archives/*.deb basically
contains         - List packages (that you already have installed) containing a file (eg. `dpkg -S /usr/bin/apt`) (exact filename)
contains-repo    - List packages (that are anywhere in your live repository) containing a file (eg. `apt-file search "/usr/bin/apt"`) (filename substring match)
content          - List filelist contained in currently installed $package_name
deb-install      - Install a .deb package file (does not install dependencies automatically. For that you will need to go `sudo apt -f install` to fix the dependency mess)
depends          - Show Depends/Recommends/Suggests information for a $repo_package_name
dist-upgrade     - Upgrade the system by removing/installing/upgrading packages (I have honestly never used this before)
download         - Download the .deb file for a $package_name into the current working directory
download-all     - Download the .deb file for a package + all packages that are required that you do not have installed (recursive-dependencies)
edit-sources     - Edit /etc/apt/sources.list with your preferred text editor
dselect-upgrade  - Follow dselect selections
fix-broken-dep   - Try to fix currently installed packages including retrying to install packages that were partially installed (especially if you do `sudo dpkg -i package_name_whatever.deb`)
full-upgrade     - Same as "dist-upgrade"
held             - List all held packages
hold             - Prevent this installed package (from package name) from being installed, upgraded, or removed
install          - Install/upgrade packages
list             - List package name/version/architecture based on a package name (can use "*" as a wildcard) that exists in your /etc/apt/sources.list* repositories (same as `apt-cache list`)
listfiles        - List files in a package
listallpkgs      - List all installed packages (with a one-lined description for each package) (pipe to grep to search for a package name)
manual           - Prevent this $installed_package_name from being `sudo apt autoremove` if it was "auto" installed from an explicitely installed package (ie. from `sudo apt get install $explicitely_installed_package && sudo apt purge $explicitely_installed_package`) as dependencies
moo              - An apt/apt-get easter egg; display a cow with a "moo"
pkgurl           - Show the URL (basically) where the $repo_package_name is located (from within the places configured in /etc/apt/sources.list*)
purge            - Remove $installed_package_name and their configuration files
recommends       - List the recommended packages of all "manually" (ie. explicit apt install or dpkg -i) installed packages
rdepends         - Show reverse dependency information for a package "what else would make $this_package_name_in_repo be installed"
reinstall        - Download and (possibly) reinstall a $currently_installed_package
remove           - Remove packages (as a list of one or more package names you have currently installed)
searchforfile    - $Search_for_a_file in all packages in your live repository /etc/apt/sources.list*
show             - Display detailed information about a package that doesnt have to be already installed.
showhold         - Same as "held"
showmanual       - Show packages that you have explicitely `sudo apt install $package_name` OR `sudo dpkg -i $deb_file #package`
showsrc          - Display all the source package records that match the given package name (requires you have a "^deb-src" line in your /etc/apt/sources.list* I am 90% sure..it looks virtually identical to the "^deb" line)
source           - Download source archives to current directory, apply all patches, extracts the source to a directory
sources          - Same as "edit-sources"
unhold           - Unhold a package
update           - Download lists of new/upgradable packages
upgrade          - Perform a safe upgrade
version          - Show the installed version of a package
' | egrep --color '^[a-z\-]+'
else
echo '
`apt-cache` depends rdepends stats list show
`apt-cache policy` pkgurl
`apt-file search` contains-repo
`apt` list show download moo showsrc source
`apt-mark` showhold showmanual
`dpkg -L` content
`dpkg -l` listallpkgs
`# I have a bash "one-liner" that can do this, request as necessary` recommends
`dpkg-query -f "\${Version}" -W` version
`dpkg -S` contains
`sudo apt` autoremove install autoclean build-dep clean dselect-upgrade edit-sources full-upgrade purge remove update upgrade
`sudo apt --build source` build
`sudo apt --purge autoremove` autopurge
`sudo apt edit-sources` sources
`sudo apt -f install` fix-broken-dep
`sudo apt full-upgrade` dist-upgrade
`sudo apt-get` check
`sudo apt install apt-file && apt-file list` listfiles
`sudo apt install apt-file && apt-file search` searchforfile
`sudo apt install aptitude && aptitude` changelog
`sudo apt install aptitude && sudo aptitude` reinstall
`sudo apt install apt-rdepends && man apt-rdepends` download-all
`sudo apt install software-properties-common && man add-apt-repository` add-repository
`sudo apt-mark` hold unhold manual auto
`sudo dpkg -i` deb-install
' | grep --color "$1"
fi
#!/bin/bash

for i in `apt-mark showmanual`; do rec="$(dpkg-query -f '${Recommends}' -W $i)"; [ -n "$rec" ] && echo "$i: $rec"; done
android-usb-debs
apt-container-debs
atool-debs
cloc-debs
epiphany-browser-debs
ffmpeg-libdvdcss2-vlc-debs
fotoxx-debs
go-debs
hexchat-debs
imagemagick-debs
jdk-debs
links-links2-lynx-debs
mono-cs-debs
nmap-debs
php-debs
python3-doc-debs
python3-tk-debs
steam-debs
vim-debs
wine-debs
xchm-debs
essential-debs/ack_3.4.0-1_all.deb
essential-debs/gawk_1%3a5.1.0-1_amd64.deb
essential-debs/gparted_1.2.0-1_amd64.deb
essential-debs/gparted-common_1.2.0-1_all.deb
essential-debs/info_6.7.0.dfsg.2-6_amd64.deb
essential-debs/inotify-tools_3.14-8.1_amd64.deb
essential-debs/install-info_6.7.0.dfsg.2-6_amd64.deb
essential-debs/iotop_0.6-24-g733f3f8-1.1_amd64.deb
essential-debs/jpegoptim_1.4.6-1_amd64.deb
essential-debs/libfile-next-perl_1.18-1_all.deb
essential-debs/libinotifytools0_3.14-8.1_amd64.deb
essential-debs/libjs-underscore_1.9.1~dfsg-3_all.deb
essential-debs/netcat_1.10-46_all.deb
essential-debs/netcat-openbsd_1.217-3_amd64.deb
essential-debs/net-tools_1.60+git20181103.0eebece-1_amd64.deb
essential-debs/optipng_0.7.7-1+b1_amd64.deb
essential-debs/perl-doc_5.32.1-4+deb11u2_all.deb
essential-debs/pinfo_0.6.13-1.1_amd64.deb
essential-debs/toilet_0.3-1.3_amd64.deb
essential-debs/toilet-fonts_0.3-1.3_all.deb
essential-debs/txt2man_1.7.1-1+deb11u1_all.deb
essential-debs/wget_1.21-1+deb11u1_amd64.deb
essential-debs/xclip_0.13-2_amd64.deb
GO THROUGH INSIDE OF ONE-LEVEL SUBDIR AND DO SOMETHING

  for d in */; do ( if cd -- "$d"; then git status >/dev/null 2>&1; if [ $? = 128 ]; then printf '%s\n' "$d isn't a valid git repo"; fi; fi ); done

SED DELETE LINES THAT DO NOT MATCH

  echo 123 | sed '/123/!d'
  echo 123 | grep -v '123'

SHOW ALL IMAGES WITHIN THE CURRENTLY DIRECTORY SUPER FAST
  
  #in XFCE close the windows successfully with Alt+F4
  for i in *.jpg; do tkjpeg $i & done

CONVERT A STRING TO MAYBE SUCCESSFUL FILENAME. AUTO-BACKSLASH BULLSHIT CHARACTERS.

  #the -l chomp(<STDIN>) and auto-newline for the print
  echo -n 'NORMAL_   BACKSLASHED :-!@#$%^&*()~<>?:"{}[];,./+' | perl -l -ne 'print qq(\Q$_\E)'

STRIPPING JPEG (PNG?????) EXIF DATA INFORMATION

  man exiftool #search 'all'
  man jpegoptim

GET ALL OF THE CAMERA STATS AND PHOTO/IMAGE EDITOR STATS OF A JPEG MAYBE A PNG

  curl -s http://a.domain.com/bigfile.jpg | exiftool -

CURL POST FORMS BASICALLY TO DO NOTHING JUST TO SEE IF THERE IS AN ERROR OR NOT IN THE RESPONSE I GUESS

  #--show-error forces to show error EVEN when --silent
  #--fail exits 22 with 0 error messages on SERVER errors (eg. 404 and 403)
  #--header is extra headers. I believe with Expect: you can put a URL
  #--location redirects if you get a 3XX error message from the target web page with a redirect URL in its Header or whatever the fuck
  curl --silent --show-error --location --fail -POST --form get=group --form email=whatever --form tool_ver=whatever --header Expect:whatever
  curl --silent --show-error --location --fail -POST --form hwaddr=whatever --header Expect:whatever

THE BEST WAY TO DETECT IF A COMMAND EXISTS

  command_available() {
    if [ -x "$1" ]; then return 0; fi
    # command -v "$1" >/dev/null 2>&1 # not required by policy, see #747320
    # which "$1" >/dev/null 2>&1 # is in debianutils (essential) but not on non-debian systems
    local OLDIFS="$IFS"
    IFS=:
    for p in $PATH; do
      if [ -x "${p}/${1}" ]; then
        IFS="$OLDIFS"
        return 0
      fi
    done
    IFS="$OLDIFS"
    return 1
  }
  if ! command_available 'wget'; then
    echo 'ERROR: wget is required for a network-based update, but it is not installed' 1>&2
    exit 5
  fi

SUPER HERE-DOC FOR A COMMAND ARGUMENT

  { perl -e "$(</dev/stdin)" kate; } <<\EOF
  print("package: ${ARGV[0]}\n");
  print("Hello world\n");
  EOF

CHOPPING LEFT OF A STRING REFERENCE

  str='123123123123'
  echo "${str#*3}"  #start from the furthest RIGHT. and CHOP LEFT. SUPER GREEDY.
  echo "${str##*3}" #start from the furthest LEFT. and CHOP LEFT. MINIMAL.


-z and -n ALTERNATIVE

  [[ "" ]] || echo "this is false. all empty strings are false"


SOME ARRAY AND STRING PUSH/APPEND SHIT

  str='123'
  arr=(1 2 3)
  str+=4      #1234
  arr+=(4)    #(1 2 3 4)

FILE GLOB FILENAME PATTERN EXPANSION TO ARRAY AND LOOP THROUGH

  touch 1.txt
  touch 2.txt
  touch 3.txt
  touch 4.html
  var=(*.txt)
  echo $var
  echo ${var[@]}
  for i in "${var[@]}"; do echo "$i"; done

BASH LIMITATIONS

  #for CSV/JSON/proper-XML/proper-HTML/proper-XHTML/mySQL/postgresSQL/binary-data-processing/floating-point-numbers/array-sorting/list-sorting
  sudo apt install jq bc sql xslt tidy xmlstarlet perl python gcc sort
  #gcc is for 'C'

MAN2TXT

  man bash | col -bx > bash.1.txt


POSSIBLY USEFUL GIT COMMANDS

  git clone https://github.com/whatever_person/whatever_repo
  git commit -m 'commit message' #3%sure this will work
  git push #7%sure this will work
  git pull #update local files in case the remote updated
  git log #view changes (3%chance will work)
  git config #???


PASSIVE FOR LOOP WITH ARRAY AS AN ARGUMENT

  files_in_pwd=(*)  #this is a file glob.
  printf -- '- %s\n' "${files_in_pwd[@]}"
  #output:
  #- deleteme.txt
  #- Desktop
  #- Documents
  #- Downloads
  #- jason.jpg
  #- jason.v01.jpg
  #- test.sh
  #- windowsxp-wallpaper

ULTIMATE TIME STAMPED FILENAME VARIABLE GENERATOR

  rsync_log_location="$HOME/rsync_$(date +%Y%m%d_%H%M%S).log"

ULTIMATE SINGLE-CHARACTER INPUT HANDLING

  read -n 1 -p '? (Y/n) ' myv
  echo

  case "$myv" in
    [Yy]|'') echo deleting everything.;;
    [Nn]*)   echo everything has been saved.;;
  esac


THIS IS SAFE EVEN IF THE DIRECTORIES IN PWD HAVE WHITESPACE

  n=0
  for myf in */; do
    (( n++ ))
    echo "$n) $myf" 
  done

THIS IS SAFE EVEN IF THE FILES/DIR IN PWD HAVE WHITESPACE

  n=0
  for myf in *; do
    (( n++ ))
    echo "$n) $myf" 
  done


COMMAND ARGUMENT AS MULTI-LINED HERE-DOC

  this_gets_expanded=1
  { python -c "$(</dev/stdin)"; } <<EOF
  print("Hello $this_gets_expanded");
  print("world");
  EOF

  this_is_a_word=2
  { python -c "$(</dev/stdin)"; } <<\EOF
  print("Hello $this_is_a_word");
  print("world");
  EOF



TESTING TO SEE IF YOU'RE IN A TERMINAL

  if test -t 1; then
    echo we are in a TTY/terminal
  fi

USING NETCAT

  #on Debian/Listener:
  nc -l -p 1234 > $(mktemp -p .)
  #on Pop/   Sender:
  nc -w 3 10.42.0.52 1234 < /etc/apt/sources.list
  #Android was @ 10.42.0.177

USING NMAP
  
  for ip_end_number in { 1 .. 255 }; do
    #-O is for Operating System detection. This requires root.
    #-v is for verbose. try -vv to get more.
    sudo nmap -v -O 10.42.0.$ip_end_number
  done

STDIN FROM A FILE

  cat < /etc/apt/sources.list

SLURP A FILE INTO A VARIABLE

  file_get_contents_var=$(<my_file_name.txt)  #the quotes are 0%necessary for simple assignment.

THIS SCRIPTS CONTENTS

  this_scripts_file_contents_string=$(<$0)

STDIN READ-IN LINE BY LINE

  while read myline; do
    echo $myline
  done <<< "$(</etc/apt/sources.list)"

USING MAPFILE TO SLURP A FILE INTO A BASH ARRAY

  echo 123 > hello.txt; mapfile < <(cat hello.txt); echo "${MAPFILE[@]}"

PRINTF MATH HEXADECIMAL N-BASE ARITHMETIC

  hexnum=ee3;decnum=500;printf '0x%08x\n' $(( 16#$hexnum + $decnum )) #0x000010d7

IMAGE MAGICK CONVERT MOGRIFY COMMANDS
  
  #-flip -flop -grayscale -rotate 90

WATCH A DIRECTORY FOR CREATED FILES ALL IN ALL OF ITS SUB-DIRECTORIES

  inotifywait --recursive \
              --monitor \
              --quiet \
              --event create ~/.config | tee ~/config_monitor_log.log

DEBIAN PACKAGE TIP: REMOVING CERTAIN PACKAGES THAT ARE DEPENDENTS OF A META PACKAGE

  sudo dpkg --force-depends --remove xfburn xfce4-dict xfce4-goodies
  sudo apt-mark hold $packages_that_can_be_autoremoved-get_from_apt-finstall_output


PYTHON3 `paste` PYTHON3 EQUIVALENT

  #!/usr/bin/python4.pl


  #zip() is like `paste abc.txt 123.txt zyx.txt`
  print(list(zip('abcde','12345','zyxwv')));

  #output:
  #[('a', '1', 'z'), ('b', '2', 'y'), ('c', '3', 'x'), ('d', '4', 'w'), ('e', '5', 'v')]


PYTHON3 SLURP AND PROCESS A BINARY FILE'S CONTENTS

  #!/usr/bin/python4.pl

  import sys

  #open this script's filename and print out its binary representation as a string
  with open(sys.argv[0], 'rb') as my_file_handle:
  {
    my_file_handle.seek(0, 2) #IE. seek from offset 0 to SEEK_END
    all_data_bytearray = bytearray(my_file_handle.tell())
    my_file_handle.seek(0)
    my_file_handle.readinto(all_data_bytearray)
    print(all_data_bytearray.hex())
  }


GITHUB SHIT

  git clone https://github.com/user95f85f/fresh-debian2
  git clone https://github.com/user95f85f/fresh-windows
  git clone https://github.com/user95f85f/fresh-debian
  git status
  git add bash.1.txt _bashrc.txt
  git rm python-cheat-cheet.txt 
  git commit
  git-token-xclip-cp 
  git push
  #rm *; git stash
  #git whatchanged
  #git pull
  #git restore LICENSE on-first-boot.txt preREADME.txt
  #git config --global user.name 'user95f85f_cli'
  #sudo git config --system user.name 'user95f85f_cli'
  #git config --global core.askPass '/path/to/executable/that/echos/the/password/hehe.sh' 
  #git config --global credential.username 'user95f85f'
  #git config --global user.email 'user9de1d@gmail.com' #--required on the github for contributions to show up on your main page to show your activity on github


JAVASCRIPT USE STRICT LIKE PERL

  'use strict';
  n = 1;        //autodie
  var n2 = 2;   //better

IMGUR UPLOAD

  curl --silent --header 'Authorization: Client-ID 313baf0c7b4d3ff' \
                --header 'Expect: ' \
                --form 'image=@your_image_filename_local.jpg' \
                https://api.imgur.com/3/image.xml > ~/out.txt

  curl --silent --header 'Authorization: Client-ID c9a6efb3d7932fd' \
                --header 'Expect: ' \
                --form 'image=@your_image_filename_local.jpg' \
                https://api.imgur.com/3/image.xml > ~/out2.txt


POPULAR WINE NAME
  
  echo Gewurztraminer
    # very sweet (sometimes/rarely dry)


URBANDICTIONARY JSON (API) REQUEST TO GET DEFINITIONS

  wget -O - 'https://api.urbandictionary.com/v0/define?term=bird' | jq | less -R
  wget -O - 'https://api.urbandictionary.com/v0/define?term=success' | grep -i definition

WORD WRAP STDIN TO STDOUT

  #default width is 75 if not set explicitely
  #--uniform-spacing is 1 space between words
  #                     2 spaces after periods
  bash whatever | fmt --uniform-spacing --width=72 > out.txt

HERE-DOC AS STDIN INPUT TO A COMMAND

  perl -ne 'BEGIN{@map = qw/areaid systemid netid/;} chomp; if ($_ !~ /^ /) { $dict = {router => $_}; push(@list, $dict); $n = 3; } else { $n--; $dict->{$map[$n]} = $_; } END { use Data::Dumper qw/Dumper/; print Dumper @list;}' <<'DATA'
  Paris
   00.0001
   0000.3090.6756
   00.0001.0000.3090.6756.00
  Berlin
   00.0001
   0000.3090.c7df
   00.0001.0000.3090.c7df.00
  London
   00.0001
   0000.0c0a.2c51
   00.0001.0000.0c0a.2c51.00
  Rome
   00.0001
   0000.0c0a.2aa9
   00.0001.0000.0c0a.2aa9.00
  Brussels
   00.0002
   0000.0c76.5b7c
   00.0002.0000.0c76.5b7c.00
  Amsterdam
   00.0002
   0000.0c04.dcc0
   00.0002.0000.0c04.dcc0.00
  DATA

WGET FOR WHEN WE MIGHT BE HITTING A DEAD WEB SERVER

  wget --tries=1 --timeout=8 --output-document=201.dat 'https://nexus-censored.censored.net:8443/repository/apt-censored-releases'

HOLY SHIT

  #!/usr/bin/env bash

  command -v aptitude &>/dev/null || exit 69

  declare -a DEPS MANUALS PKDEPS
  export LANG="C"  ### better performances and no UTF-8 expected


  #note: this list is newline segregated/separated/tokened
  #watch out: this list includes i386 and all AND amd64 architecture packages (assuming you have i386 enabled)
  #watch out: this list may have dulicate listings from mixed i386 AND amd64 packages (assuming you have i386 enabled)
  str_installed_package_list="$(dpkg --list | mawk 'FNR>5{if (/^.i. /){$0=$2; sub(/:.+$/,""); print " "$0" "}}')"

  #%p is package name

  #38000+ packages are virtual?!
  #the `| egrep -v ':'` filters out the :i386 and :any packages. (so all we have are :amd64 virtual packages)
  #note: this stringed-list is newline tokened/segregated/separated
  str_virtual_package_list="$(aptitude --display-format ' %p ' search '?virtual(^)' | grep -v ':')"


  ar_manually_installed_packages=()
  # -r: avoid backslashes from creating escaped characters from the input
  # -d '': slurp the whole input versus just accepting one line
  read -d '' -ra ar_manually_installed_packages < <(apt-mark showmanual)
  ar_recommends_output_mess=()
  #very much some ${binary:Package} have explicit   :i386   or   :amd64  appended to their ${binary:Package} name
  #however, ironically, the list in the ${Recommends} never says if the Recommended_package is :i386 or :amd64
  # -t: chomp()
  readarray -t ar_recommends_output_mess < <(dpkg-query --showformat='${binary:Package}: ${Recommends}\n' --show "${ar_manually_installed_packages[@]}" |\
                          sed 's/ ([^)]\+)//g') #this sed is to strip the required package version in parenthesis that sometimes appears in the Recommended list.

  for PKG in "${PKDEPS[@]}"; do

     [[ "${PKG#* }" ]] || continue
     echo -en "${PKG%% *}  "
     read -d '' -ra DEPS <<<"${PKG#* }"
     for DEP in "${DEPS[@]}"; do

        [[ "$DEP" == "|" ]] && { echo -en "\e[0;33m$DEP\e[0m"; SPACE=""; continue; }
        [[ "$INSTALLEDPKGS" =~ " ${DEP%,} " ]] && { echo -en "$SPACE\e[0;37m$DEP\e[0m"; SPACE=" "; continue; }
        [[ "$VIRTUALS" =~ " ${DEP%,} " ]] && { echo -en "$SPACE\e[38;5;21m$DEP\e[0m"; SPACE=" "; continue; }
        echo -en "$SPACE\e[1m$DEP\e[0m"; SPACE=" "

     done
     echo

  done

IMAGE MAGICK RESEARCH WITH OVERLAPPING TWO IMAGES TO CREATE PIXEL-PARSEABLE OUTPUT

  #!/bin/bash

  #misc screenshot difference through composite automator
  #purpose: seek the image that can be used pixel-by-pixel to seek:

  #- location of cursor (i.e., x=342;y=420)
  #- location of crop (i.e., x=200;y=231;width=140;height=230)

  #<CONFIGURE>
  branches=(
  screenshot_over_blank
  blank_over_screenshot
  )
  colors=255
  modes=(
  exclusion
  difference
  subtract
  minus
  divide
  bumpmap
  )
  #</CONFIGURE>

  for main_dir in ${branches[*]} ; do
    for mode in ${modes[*]} ; do 
      location=$main_dir/$mode
      mkdir -p $location
      n=0
      for i in Screenshot*.jpg ; do 
        let n++
        [ `echo "$n % 2" | bc` -eq 1 ] && {
          last_jpeg=$i
          continue
        }
        if [ $main_dir = screenshot_over_blank ] ; then
          command="composite -compose $mode $i $last_jpeg $location/$n.png"
          echo $command > $location/readme.txt
          eval $command
        elif [ $main_dir = blank_over_screenshot ] ; then
          command="composite -compose $mode $last_jpeg $i $location/$n.png"
          echo $command > $location/readme.txt
          eval $command
        fi
      done
    done
  done

STRIP NON-ASCII CHARACTERS FROM A TEXT FILE

  #strip non-ASCII characters from the filename stored in variable $1
  perl -pi~ -e 's/[^[:ascii:]]+//g' "$1"

  #detect non-ASCII characters from the filename stored in variable $1
  perl -ne 'if(/[^[:ascii:]]/){print "found some non ascii";exit 1;}' "$1" && exit 0

MP3 INTO A VIDEO WITH A STILL IMAGE (400x300)

  ffmpeg -loop 1 -i video-background.png -i Necro-killed-by-sorc.mp3 -shortest video.mp4

/bin/sh VS /bin/bash SHE-BANGS WITH EXECUTABLES IN (/usr)?/bin/*

  grep /bin/sh | wc -l    #215
  grep /bin/bash | wc -l  #33
  file /bin/sh            #dash

WAIT UNTIL A SPECIFIC DAY OF THE MONTH AND ALERT YOURSELF OF THE NEW IMPROVED YOU

  while :; do [[ "$(date +%d)" = '12' ]] && { wall 'hell yeah'; DISPLAY=:0.0 zenity --title="I'm free!" --info --text='We made it' --no-wrap ; break; }; sleep 1h; done

INSTALL A DESKTOP ENVIRONMENT ON YOUR DEBIAN

  tasksel --list-tasks
  #i desktop	Debian desktop environment
  #u gnome-desktop	GNOME
  #i xfce-desktop	Xfce
  #u gnome-flashback-desktop	GNOME Flashback
  #u kde-desktop	KDE Plasma
  #u cinnamon-desktop	Cinnamon
  #u mate-desktop	MATE
  #u lxde-desktop	LXDE
  #u lxqt-desktop	LXQt
  #u web-server	web server
  #u ssh-server	SSH server
  #i laptop	laptop

PERL/TK SHOW-CASE SHOWS YOU WHAT YOU CAN DO WITH PERL/TK!!!!

  widget #show perl/tk show-case that shows you all the cool things perl/Tk can do.

CREATE YOUR FUCKING LIVE-OS YOU DUMB BITCH

  LANGUAGE=C LANG=C LC_ALL=C perl << EOF
  open LOOP, '</dev/loop0' or die $!;
  open DEST, '</tmp/live' or die $!;
  ioctl(LOOP, 0x4C06, fileno(DEST)) or die $!; #0x4C06 on loop.h is: LOOP_CHANGE_FD
  close LOOP;
  close DEST;
  EOF

FFMPEG SLOW AUDIO BY A PERCENTAGE/%

  ffmpeg -i track_11_pitch2.mp3 -filter:a 'atempo=0.86' track_11_pitch2_slowed.mp3

TMUX QUICK REFERENCE GUIDE

  tmux
  tmux attach #ie. restore-pane-(settings-)session
  CTRL+B, "   OPEN(horizontal pane)
  CTRL+B, %   OPEN(vertical pane)
  CTRL+B, ARROW KEYS      SWITCH(active/current pane)
  CTRL+B, x   CLOSE(active/current pane)
  CTRL+D      (same thing as above)
  exit        (same thing as above)
  
  CTRL+B, z   ZOOM(current pane)
  CTRL+B, z   ZOOM-OUT(to all panes)

#PUT IN BASH HISTORY TO take list of URL links and wget them into 001.txt .. 999.txt
#n=0; while read myLine; do (( n++ )); wget_out_file=$(printf '%03d.txt' $n); echo "$wget_out_file $myLine" >> wget-MAP.txt; echo "wget -O $wget_out_file '$myLine'" >> wget-TODO.sh; done <<< "$(cat novaks-LINKs.txt)"

export TZ=America/Los_Angeles
export EDITOR=/usr/bin/vim
export WINEPREFIX=/media/user/DEB_STUFF/dot-wine
wow='/media/user/DEB_STUFF/dot-wine/drive_c/Program Files (x86)/Battle.net'
www='/home/user/Documents/localhost-httpd/www'
export todo='/home/user/Documents/TODO/todo.txt'
export bash1='/home/user/Documents/bash.1.txt'
export perl_cheatsheet='/home/user/Documents/Github-repos/debian-fresh1/perl-cheat-sheet.txt'
export python_cheatsheet='/home/user/Documents/Github-repos/debian-fresh2/python-cheat-sheet.txt'
alias ..='cd ..'
alias vi='/usr/bin/vim'
alias bitchx='/usr/bin/weechat -a -p --plugins irc,logger,alias,exec' #no auto-connect to a server and no plug-ins automatically loaded
alias weechat='echo bitchx'
alias bash++='/usr/bin/perl'
alias suspend='echo systemctl suspend'
alias battery='echo acpitool -b'
alias eject='bash -c "udisksctl unmount --block-device /dev/sdb1; udisksctl power-off --block-device /dev/sdb"'
alias check-network='bash -c "ip route; curl -IL http://nmcheck.gnome.org/check_network_status.txt"'
alias tty-silence='sudo dmesg -n 1'
alias tty-silence-restore='sudo dmesg -n 8'
pm-finder(){
  [ -z "$1" ] && return 3
  find $(perl -e 'for(@INC){print $_, " ";}') -type f -name "*$1*" -printf '%p ' 2>/dev/null
}
vim-pm(){
  [ -z "$1" ] && return 3
  $EDITOR $(find $(perl -e 'for(@INC){print $_, " ";}') -type f -name $1 -printf '%p ' 2>/dev/null )
}
git-push(){ [ -f genREADME_sh.txt -a -n "$(find -mindepth 1 -newer README.txt)" ] && bash genREADME_sh.txt; git push; }
lucky(){ s="$*"; [ -z "$s" ] && return 3; echo $s | perl -ne 'BEGIN{undef $/;}if(m#(https?://[a-zA-Z0-9/.:?_\-]+)#){print $1;}'; }
cd-git(){
  [ -n "$1" ] || return 3
  [[ $1 =~ ^win.* ]] && { cd ~/Documents/Github-repos/windows-fresh; return 0; }
  [[ $1 =~ ^deb1.* ]] && { cd ~/Documents/Github-repos/debian-fresh1; return 0; }
  [[ $1 =~ ^deb2.* ]] && { cd ~/Documents/Github-repos/debian-fresh2; return 0; }
  return 4
}
alias cdgit='cd-git'
goog(){ local s="$*"; links "https://google.com/search?q=${s// /+}"; }
alias sync='echo sync-all'
sync-all(){
  [ -f ~/.bashrc_user ] || return 3
  [ -f /media/user/DEB_STUFF/_bashrc ] || return 4
  [ -f ~/.vimrc ] || return 5
  [ -f /media/user/DEB_STUFF/_vimrc ] || return 6
  [ -f ~/Documents/Github-repos/debian-fresh2/_bashrc.txt ] || return 7
  [ -f ~/Documents/Github-repos/debian-fresh2/_vimrc.txt ] || return 8
  [ -f ~/Documents/bash.1.txt ] || return 9
  [ -f ~/Documents/Github-repos/debian-fresh2/bash.1.txt ] || return 10

  diff ~/.bashrc_user /media/user/DEB_STUFF/_bashrc || \
    cp -vi ~/.bashrc_user /media/user/DEB_STUFF/_bashrc
  diff ~/.vimrc /media/user/DEB_STUFF/_vimrc || \
    cp -vi ~/.vimrc /media/user/DEB_STUFF/_vimrc
  diff ~/.bashrc_user ~/Documents/Github-repos/debian-fresh2/_bashrc.txt || \
    cp -vi ~/.bashrc_user ~/Documents/Github-repos/debian-fresh2/_bashrc.txt
  diff ~/Documents/bash.1.txt ~/Documents/Github-repos/debian-fresh2/bash.1.txt || \
    cp -vi ~/Documents/bash.1.txt ~/Documents/Github-repos/debian-fresh2/bash.1.txt
}
vol-up(){
  local myv
  echo '+5%'
  pactl set-sink-volume alsa_output.pci-0000_00_1f.3.analog-stereo +5%
  while :; do
    read -n 1 -p '+5%? (Y/n) ' myv
    echo

    case "$myv" in
      [Yy]|'') pactl set-sink-volume alsa_output.pci-0000_00_1f.3.analog-stereo +5%;;
      [Nn]*)   break;;
    esac
  done
}
vol-down(){
  local myv
  echo '-5%'
  pactl set-sink-volume alsa_output.pci-0000_00_1f.3.analog-stereo -5%
  while :; do
    read -n 1 -p '-5%? (Y/n) ' myv
    echo

    case "$myv" in
      [Yy]|'') pactl set-sink-volume alsa_output.pci-0000_00_1f.3.analog-stereo -5%;;
      [Nn]*)   break;;
    esac
  done
}
xclip-cp(){
  [ -z "$1" ] && return 4
  [ -f "$1" ] || return 5
  DISPLAY=:0.0 xclip -selection clipboard "$1"
}
watch-dir(){
  [ -z "$1" ] && return 1
  [ -d "$1" ] || return 2
  inotifywait --recursive --monitor --quiet --event create "$1"
}
ffplay(){
  [ -z "$1" ] && return 1
  [ -f "$1" ] || return 2
  local filename="$1"
  local file_extension="${filename##*.}"
  if [[ $file_extension = 'mp3' || $file_extension = 'wav' || $file_extension = 'ogg' || $file_extension = 'm4a' ]]; then
    /usr/bin/ffplay -nodisp -autoexit -hide_banner -v 40 "$filename"  #the -v 40 is a good verbosity I think
  else
    /usr/bin/ffplay -hide_banner -v 40 "$filename"
  fi
}
alias ffplayer=ffplay
# convert spaces in files to underscore
#while read myf; do [ -f "$myf" ] || break; mv "$myf" "${myf// /_}"; done <<< "$( ls *\ * )"
# or just convert it into anonymously named/numbered mp3 files.
#n=1; while read myf; do mv "$myf" "$n.mp3"; (( n++ )); done <<< "$(ls *.mp3)"
mp3-review(){
  [[ "$(find -maxdepth 1 -type f -name '* *' | wc -l)" -ne 0 ]] && return 3 #any spaces in files? just exit. fuck it.
  for i in $(echo *.mp3 | tr ' ' '\n' | shuf | tr '\n' ' '); do ffplay $i; read -n 1 -p 'delete? (y/N) ' yn; [[ $yn = 'y' ]] && { echo; rm -v $i; echo; } ;  done
}
# ( cmd1; cmd2; cmd3; cmd4; ) | zenity --progress --title="fucking wait" --pulsate --autoclose --nocancel
msgbox(){
  [ -z "$1" ] && return 3
  [ -z "$2" ] && return 4
  zenity --title="$1" --info --text="$2" --no-wrap
}
internet-test(){
  echo "curl --output /dev/null --silent --head --location 'http://people.oregonstate.edu/' -w '%{http_code}'" >&2
  curl --output /dev/null --silent --head --location 'http://people.oregonstate.edu/' -w '%{http_code}'
}
man2txt(){
  echo 'zcat `man --where bash` | groff -t -e -mandoc -Tascii - | less -R'
}
wifi-on(){
  #disable auto-connect on auto-connect-enabled connection(s)
  for i in $(nmcli connection show --active | grep wifi | egrep -o '\b[a-f0-9]+-[a-f0-9]+-[^ ]+\b'); do nmcli connection down uuid $i; done

  nmcli radio wifi on
}
wifi-off(){
  #disable auto-connect on auto-connect-enabled connection(s)
  for i in $(nmcli connection show --active | grep wifi | egrep -o '\b[a-f0-9]+-[a-f0-9]+-[^ ]+\b'); do nmcli connection down uuid $i; done

  nmcli radio wifi off
}
#use `sudo iw dev` to list wireless interface names
wifi-scan(){
  #sudo iw dev wlp1s0 scan | egrep 'SSID: [^ ]+' | awk '-F: ' '{print $2}' | sort 
  nmcli device wifi
}
wifi-connect(){
  #[ -z "$1" ] && { echo 'wifi-connect $SSID [$password]'; return 6; }
  [ -z "$1" ] && { echo 'wifi-connect $SSID'; return 6; }
  #local pwopt=''
  #[ -n "$2" ] && pwopt="password $2"
  #nmcli device wifi connect "$1" $pwopt && \
  nmcli --wait 20 --ask device wifi connect "$*"  #I'm 30%sure this is right.
}
date-toilet(){
  date | toilet --font term --gay
}
debian-forum-open(){
  [ -z "$1" ] && return 55
  [ -f "$1" ] || return 56
  local filename="$(basename "$1")"
  [[ $filename =~ ^[0-9]+\.txt$ ]] || return 57
  local topic_id="${filename%%.*}"
  echo google-chrome "http://forums.debian.net/viewtopic.php?t=$topic_id" 
  google-chrome "http://forums.debian.net/viewtopic.php?t=$topic_id" 
}
excuse(){
  echo "Actually, that's a feature
Don't worry, that value is only wrong half of the time
Even though it doesn't work, how does it feel?
Everything looks fine my end
How is that possible?
I broke that deliberately to do some testing
I can have a look but there's a lot of if statements in that code!
I can't make that a priority right now
I can't test everything
I couldn't find any examples of how that can be done anywhere else in the project
I couldn't find any examples of how that can be done anywhere online
I couldn't find any library that can even do that
I did a quick fix last time but it broke when we rebooted
I didn't anticipate that I would make any errors
I didn't create that part of the program
I didn't receive a ticket for it
I forgot to commit the code that fixes that
I had to do the project backwards as people demanded results out of order
I have never seen that before in my life
I haven't been able to reproduce that
I haven't had any experience with that before
I haven't had the chance to run that code yet
I haven't touched that code in weeks
I must not have understood what you were asking for
I thought I finished that
I thought I fixed that
I thought he knew the context of what I was talking about
I thought you signed off on that?
I told you yesterday it would be done by the end of today
I usually get a notification when that happens
I was just fixing that
I'm not familiar with it so I didn't fix it in case I made it worse
I'm not getting any error codes
I'm not sure as I've never had a look at how that works before
I'm still working on that as we speak
I'm surprised it works as well as it does
In the interest of efficiency I only check my email for that on a Friday
It must be a firewall issue
It must be because of a leap second
It probably won't happen again
It was working in my head
It worked yesterday
It works, but it's not been tested
It would have taken twice as long to build it properly
It would take too long to rewrite the code from scratch
It's a browser compatibility issue
It's a character encoding issue
It's a known bug with the programming language
It's a known bug with the server software
It's a remote vendor issue
It's a third party application issue
It's an unexpected emergent behaviour of several last minute abstractions
It's just some unlucky coincidence
It's never done that before
It's never shown unexpected behaviour like this before
It's not a code problem - our users need more training
Maybe somebody forgot to pay our hosting company
My time was split in a way that meant I couldn't do either project properly
No one told me so I was forced to assume which way to do that
Nobody asked me how long it would actually take
Nobody has ever complained about it
Oh, that was just a temporary fix
Oh, that was only supposed to be a placeholder
Oh, you said you DIDN'T want that to happen?
Our code quality is no worse than anyone else in the industry
Our hardware is too slow to cope with demand
Our internet connection must not be working
Our redundant systems must have failed as well
Somebody must have changed my code
That behaviour is in the original specification
That code seemed so simple I didn't think it needed testing
That code was written by the last guy
That error means it was successful
That feature would be outside of the scope
That isn't covered by my job description
That process requires human oversight that nobody was providing
That was literally a one in a million error
That wasn't in the original specification
That worked perfectly when I developed it
That's already fixed it just hasn't taken effect yet
That's interesting, how did you manage to make it do that?
That's not a bug it's a configuration issue
That's the fault of the graphic designer
The WYSIWYG must have produced an invalid output
The client must have been hacked
The client wanted it changed at the last minute
The code is compiling
The download must have been corrupted
The existing design makes it difficult to do the right thing
The marketing department made us put that there
The original specification contained conflicting requirements
The person responsible doesn't work here anymore
The problem seems to be with our legacy software
The program has never collected that information
The project manager said no one would want that feature
The project manager told me to do it that way
The specifications were ambiguous
The third party API is not responding
The third party documentation doesn't exist
The third party documentation is wrong
The unit test doesn't cover that eventuality
The user must not know how to use it
There must be something strange in your data
There were too many developers working on that same thing
There's currently a problem with our hosting company
This code was not supposed to go in to production yet
This is a previously known bug you told me not to work on yet
We didn't have enough time to peer review the final changes
We outsourced that months ago
We should have updated our software years ago
We spent three months debugging it because we only had one month to build it
Well at least we know not to try that again
Well done, you found my easter egg!
Well, at least it displays a very pretty error
Well, that's a first
What did you type in wrong to get it to crash?
Where were you when the program blew up?
You can't use that version on your system
You must be missing some of the dependencies
You must have done something wrong
You must have the wrong version
You're doing it wrong
Your browser must be caching the old content" | shuf -n 1
}








grephtml_0.01_all/DEBIAN/control               (required)
                         md5sums               (almost required)
                         conffiles             (optional)
                  usr/bin
                  usr/share/grephtml/
                            doc/grephtml/README
                            perl5/File/Next.pm

cd ./ && dpkg-deb --build grephtml_0.01_all #generated grephtml_0.01_all.deb ready to install!

meta packages are packages where in the control file all you have is,
  cat my-meta-package_0.01_all/DEBIAN/control #Depends:  vim, lynx



cat grephtml_0.01_all/DEBIAN/conffiles
/etc/pinforc



cat grephtml_0.01_all/DEBIAN/control
Package:  grephtml
Version:  0.01
Architecture:  all
Essential:  no
Priority:  optional
Depends:  perl:any, grep, libfile-next-perl (>= 1.18)
Maintainer:  Superman
Homepage:  https://github.com/yourusername/grephtml/
Installed-Size: 100              (this is in KB)
Description: grep an html.       (super short description w/period [eg. "Gnome partition editor."])
 grep HTML directory with ease.
 .
 next paragraph1.
 .
 next paragraph2.
 .
 line7
 line8
 line9
 line10
 line11
 line12
 line13
 line14
 line15
 line16
 line17
 line18
 line19
 line20
 line21
 line22
 line23
 line24
 line25
 line26
 line27
 line28
 line29
 line30
 line31 (THIS IS MAX LINES FOR A Description:)
Section: doc (eg. perl-doc)
Section: graphics (eg. optipng)
Section: net (eg. net-tools)
Section: perl (eg. libfile-next-perl)
Section: utils (eg. grep [I think])
Section: web (eg. wget)
Recommends: zenity




cat grephtml_0.01_all/DEBIAN/md5sums
$md5sum  usr/share/*
         usr/lib/*
         bin/*
         sbin/*




cat grephtml_0.01_all/usr/share/doc/grephtml/README
Grephtml
========
Current Web home: https://*

paragraph here 71 max char length

paragraph here again

paragraph here again2

*paste LICENSE here*
echo *-debs essential-debs/* | tr ' ' '\n'
apt-container-debs
atool-debs
audacity-debs
cloc-debs
epiphany-browser-debs
essential-debs
ffmpeg-libdvdcss2-vlc-debs
fotoxx-debs
git-debs
gmtp_android-usb-debs
go-debs
hexchat-debs
imagemagick-debs
jdk-debs
links-links2-lynx-debs
mono-cs-debs
nmap-debs
php-debs
python3-doc-debs
python3-tk-debs
steam-debs
vim-debs
w3c-css-validator-debs
weechat-debs
wine-debs
xchm-debs
essential-debs/ack_3.4.0-1_all.deb
essential-debs/acpitool_0.5.1-6_amd64.deb
essential-debs/bind9-dnsutils_1%3a9.16.27-1~deb11u1_amd64.deb
essential-debs/gawk_1%3a5.1.0-1_amd64.deb
essential-debs/gparted_1.2.0-1_amd64.deb
essential-debs/gparted-common_1.2.0-1_all.deb
essential-debs/info_6.7.0.dfsg.2-6_amd64.deb
essential-debs/inotify-tools_3.14-8.1_amd64.deb
essential-debs/install-info_6.7.0.dfsg.2-6_amd64.deb
essential-debs/iotop_0.6-24-g733f3f8-1.1_amd64.deb
essential-debs/jpegoptim_1.4.6-1_amd64.deb
essential-debs/jq_1.6-2.1_amd64.deb
essential-debs/libfile-next-perl_1.18-1_all.deb
essential-debs/libinotifytools0_3.14-8.1_amd64.deb
essential-debs/libjq1_1.6-2.1_amd64.deb
essential-debs/libjs-underscore_1.9.1~dfsg-3_all.deb
essential-debs/libonig5_6.9.6-1.1_amd64.deb
essential-debs/netcat_1.10-46_all.deb
essential-debs/netcat-openbsd_1.217-3_amd64.deb
essential-debs/net-tools_1.60+git20181103.0eebece-1_amd64.deb
essential-debs/optipng_0.7.7-1+b1_amd64.deb
essential-debs/perl-doc_5.32.1-4+deb11u2_all.deb
essential-debs/pinfo_0.6.13-1.1_amd64.deb
essential-debs/strace_5.10-1_amd64.deb
essential-debs/toilet_0.3-1.3_amd64.deb
essential-debs/toilet-fonts_0.3-1.3_all.deb
essential-debs/txt2man_1.7.1-1+deb11u1_all.deb
essential-debs/wget_1.21-1+deb11u1_amd64.deb
essential-debs/xclip_0.13-2_amd64.deb
#!/usr/bin/perl

use warnings;
use strict;

#converts '/whatever/bin/app-name -option1 -option2' into 'appname'
sub exec2appname{
  my ($exec) = @_;
  my $appname = $exec;
  undef $exec;
  $appname =~ s# .*$##; 
  (undef,$appname) = ($appname =~ m{^(.*/)?(.*)}s); #ripped from /usr/share/perl/5.32.1/File/Basename.pm
  return $appname;
}

binmode(STDOUT, ':unix'); #a trick to make STDERR/warnings print in-sync with STDOUT
                          #(copied from `perldoc -q flush`)




my @desktop_files = </usr/share/applications/*.desktop>;
for my $desktop_filename (@desktop_files){
  open(my ${f}, "<$desktop_filename") or die $!;
  my $exec = '';
  my $appname = '';
  my $icon = '';
  while(<$f>){ #read line-by-line
    chomp;
    if(/^Exec=(.*)/){
      $exec = $1;
      if($exec eq ''){
        warn "WARNING: File '$desktop_filename' has an empty Exec=    ...ignoring this desktop file";
        next;
      }
      $appname = exec2appname($exec); 
    }
    if(/^Icon=(.*)/){$icon = $1;}
  } 
  close(${f}) or die $!;
  if($exec eq ''){
    warn "WARNING: File '$desktop_filename' has no Exec=    ...ignoring this desktop file";
    next;
  }
  if($appname eq ''){
    die "CRITICAL: My conversion of '${desktop_filename}'s Exec= to an app name 900\%failed ...critical error";
  }
  if($icon eq ''){
    warn "WARNING: File '$desktop_filename' has no Icon=    ...continuing anyway";
  }
  print qq/$appname, $icon\n/;
}
#!/bin/bash



wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
sudo fdisk -l



Disk /dev/sda: 931.51 GiB, 1000204886016 bytes, 1953525168 sectors
Disk model: ST1000LM035-1RK1
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes
Disklabel type: gpt
Disk identifier: 01EA0B1E-A254-44C5-B3DF-061B4120BD39

Device         Start       End   Sectors  Size Type
/dev/sda1       2048 614402047 614400000  293G Linux filesystem
/dev/sda2  614402048 634882047  20480000  9.8G Linux swap


Disk /dev/loop0: 2.09 GiB, 2240999424 bytes, 4376952 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
#!/bin/bash

[ -f ~/README.txt ] && exit 2

rm -v README.txt
echo 'cat *.txt > README.txt'
cat *.txt > ~/README.txt
mv -v ~/README.txt ./
sudo -E hw-probe -all -show
exit 0


Host Info
=========

System: debian
Arch:   x86_64
Kernel: 5.10.0-13-amd64
Vendor: Dell Inc.
Model:  Inspiron 5566
Year:   2019
Type:   docking station
Hwaddr: 8da44ed9a44619834fdc8fb5bc1bf619


Devices (35)
============

+------+---------------------+------------------+-------------------------------------+--------------+
| Bus  | ID                  | Vendor           | Device                              | Type         |
+------+---------------------+------------------+-------------------------------------+--------------+
| PCI  | 8086-5916-1028-07de | Intel Corpora... | HD Graphics 620                     | graphics ... |
| PCI  | 8086-9d71-1028-07de | Intel Corpora... | Sunrise Point-LP HD Audio           | sound        |
| PCI  | 10ec-8136-1028-07de | Realtek Semic... | RTL810xE PCI Express Fast Ethern... | network      |
| PCI  | 168c-0036-1028-020e | Qualcomm Atheros | QCA9565 / AR9565 Wireless Networ... | network      |
| PCI  | 8086-9d03-1028-07de | Intel Corpora... | Sunrise Point-LP SATA Controller... | storage      |
| PCI  | 8086-5904-1028-07de | Intel Corpora... | Xeon E3-1200 v6/7th Gen Core Pro... | bridge       |
| PCI  | 8086-9d14-1028-07de | Intel Corpora... | Sunrise Point-LP PCI Express Roo... | bridge       |
| PCI  | 8086-9d15-1028-07de | Intel Corpora... | Sunrise Point-LP PCI Express Roo... | bridge       |
| PCI  | 8086-9d58-1028-07de | Intel Corpora... | Sunrise Point-LP LPC Controller     | bridge       |
| PCI  | 8086-9d3a-1028-07de | Intel Corpora... | Sunrise Point-LP CSME HECI #1       | communica... |
| PCI  | 8086-9d21-1028-07de | Intel Corpora... | Sunrise Point-LP PMC                | memory co... |
| PCI  | 8086-9d31-1028-07de | Intel Corpora... | Sunrise Point-LP Thermal subsystem  | signal pr... |
| PCI  | 8086-9d60-1028-07de | Intel Corpora... | Sunrise Point-LP Serial IO I2C C... | signal pr... |
| PCI  | 8086-9d61-1028-07de | Intel Corpora... | Sunrise Point-LP Serial IO I2C C... | signal pr... |
| PCI  | 8086-9d23-1028-07de | Intel Corpora... | Sunrise Point-LP SMBus              | smbus        |
| PCI  | 8086-9d2f-1028-07de | Intel Corpora... | Sunrise Point-LP USB 3.0 xHCI Co... | usb contr... |
| USB  | 0cf3-e005           | Qualcomm Athe... | Qualcomm Atheros Bluetooth Device   | bluetooth    |
| USB  | 0c45-6712           | Microdia         | Integrated Webcam HD                | camera       |
| USB  | 0bda-0129           | Realtek Semic... | RTS5129 Card Reader Controller      | card reader  |
| USB  | 1d6b-0002           | Linux Foundation | 2.0 root hub                        | hub          |
| USB  | 1d6b-0003           | Linux Foundation | 3.0 root hub                        | hub          |
| EISA | au-optronics-auo... | AU Optronics     | LCD Monitor AUO70EC 1366x768 344... | monitor      |
| SYS  | lgc-lgc2-80-dell... | LGC-LGC2.80      | Battery DELL 991XP82 Li-ion 41.4 Wh | battery      |
| SYS  | dell-1-11-0-12-0... | Dell Inc.        | BIOS 1.11.0 12/04/2019              | bios         |
| SYS  | intel-6-142-9-co... | Intel            | Core i7-7500U CPU @ 2.70GHz         | cpu          |
| SYS  | intel-6-142-9-co... | Intel            | Core i7-7500U CPU @ 2.70GHz         | cpu          |
| SYS  | intel-6-142-9-co... | Intel            | Core i7-7500U CPU @ 2.70GHz         | cpu          |
| SYS  | intel-6-142-9-co... | Intel            | Core i7-7500U CPU @ 2.70GHz         | cpu          |
| SYS  | sk-hynix-hma81gs... | SK Hynix         | RAM HMA81GS6AFR8N-UH 8GB SODIMM ... | memory       |
| SYS  | dell-0nwjdc-a00     | Dell Inc.        | Motherboard 0NWJDC A00              | motherboard  |
| PS/2 | ps/2:0001-0001-a... |                  | AT Translated Set 2 keyboard        | keyboard     |
| PS/2 | ps/2:06cb-75c5-d... |                  | DELL07DE:00 06CB:75C5 Mouse         | mouse        |
| PS/2 | ps/2:75c5-75c5-d... | 75C5             | DELL07DE:00 06CB:Touchpad           | touchpad     |
| SCSI | hl-dt-st-dvd-rw-... | HL-DT-ST         | DVD+-RW GU90N                       | cdrom        |
| IDE  | seagate-st1000lm... | Seagate          | ST1000LM035-1RK172 1TB              | disk         |
+------+---------------------+------------------+-------------------------------------+--------------+

sudo lsblk --list --output-all /dev/sda | tr '\t' ' ' | sed 's/ \{2,\}/ /g'


NAME KNAME PATH MAJ:MIN FSAVAIL FSSIZE FSTYPE FSUSED FSUSE% FSVER MOUNTPOINT LABEL UUID PTUUID PTTYPE PARTTYPE PARTTYPENAME PARTLABEL PARTUUID PARTFLAGS RA RO RM HOTPLUG MODEL SERIAL SIZE STATE OWNER GROUP MODE ALIGNMENT MIN-IO OPT-IO PHY-SEC LOG-SEC ROTA SCHED RQ-SIZE TYPE DISC-ALN DISC-GRAN DISC-MAX DISC-ZERO WSAME WWN RAND PKNAME HCTL TRAN SUBSYSTEMS REV VENDOR ZONED DAX
sda sda /dev/sda 8:0 01ea0b1e-a254-44c5-b3df-061b4120bd39 gpt 128 0 0 0 ST1000LM035-1RK172 WL127AV7 931.5G running root disk brw-rw---- 0 4096 0 4096 512 1 mq-deadline 64 disk 0 0B 0B 0 0B 0x5000c500b919327a 1 0:0:0:0 sata block:scsi:pci SDM3 ATA none 0
sda1 sda1 /dev/sda1 8:1 76.6G 287.9G ext4 196.6G 68% 1.0 /media/user/DEB_STUFF DEB_STUFF 15753af0-daad-4cbd-bd97-0ac76cb6dbbe 01ea0b1e-a254-44c5-b3df-061b4120bd39 gpt 0fc63daf-8483-4772-8e79-3d69d8477de4 Linux filesystem DEB_STUFF b239907a-b449-4189-a365-e3cd8bd0ec03 128 0 0 0 293G root disk brw-rw---- 0 4096 0 4096 512 1 mq-deadline 64 part 0 0B 0B 0 0B 0x5000c500b919327a 1 sda block:scsi:pci none 0
sda2 sda2 /dev/sda2 8:2 swap 1 [SWAP] DEB_SWAP 87e82457-cfe9-427e-8d60-e8abc41f2fd4 01ea0b1e-a254-44c5-b3df-061b4120bd39 gpt 0657fd6d-a4ab-43c4-84e5-0933c84b4f4f Linux swap DEB_SWAP c1352856-794d-47ed-8cf9-819133a923c1 128 0 0 0 9.8G root disk brw-rw---- 0 4096 0 4096 512 1 mq-deadline 64 part 0 0B 0B 0 0B 0x5000c500b919327a 1 sda block:scsi:pci none 0
#!/bin/bash


#PUT ME IN /usr/bin
perl -ne 'if(/[^[:ascii:]]/){print "found some non ascii";exit 1;}' "$1" && exit 0
#!/bin/bash


#PUT ME IN /usr/bin/
perl -pi~ -e 's/[^[:ascii:]]+//g' "$1"
'e' at boot menu -->  ^linux .* systemd.unit=multi-user.target
                                nomodeset                       (maybe)
apt -s install nvidia-tesla-460-driver




NOTE: This is only a simulation!
      apt needs root privileges for real execution.
      Keep also in mind that locking is deactivated,
      so don't depend on the relevance to the real current situation!
Reading package lists...
Building dependency tree...
Reading state information...
The following additional packages will be installed:
  glx-alternative-mesa glx-alternative-nvidia glx-diversions libatomic1:i386
  libbsd0:i386 libdrm-amdgpu1:i386 libdrm-intel1:i386 libdrm-nouveau2:i386
  libdrm-radeon1:i386 libdrm2:i386 libedit2:i386 libegl-mesa0:i386
  libegl-nvidia-tesla-460-0 libegl-nvidia-tesla-460-0:i386 libegl1:i386
  libelf1:i386 libexpat1:i386 libffi7:i386 libgbm1:i386 libgl1:i386
  libgl1-mesa-dri:i386 libgl1-nvidia-tesla-460-glvnd-glx
  libgl1-nvidia-tesla-460-glvnd-glx:i386 libglapi-mesa:i386
  libgles-nvidia-tesla-460-1 libgles-nvidia-tesla-460-1:i386
  libgles-nvidia-tesla-460-2 libgles-nvidia-tesla-460-2:i386 libgles1
  libgles1:i386 libgles2:i386 libglvnd0:i386 libglx-mesa0:i386
  libglx-nvidia-tesla-460-0 libglx-nvidia-tesla-460-0:i386 libglx0:i386
  libllvm11:i386 libmd0:i386 libnvidia-tesla-460-cbl libnvidia-tesla-460-cfg1
  libnvidia-tesla-460-cuda1 libnvidia-tesla-460-cuda1:i386
  libnvidia-tesla-460-eglcore libnvidia-tesla-460-eglcore:i386
  libnvidia-tesla-460-encode1 libnvidia-tesla-460-encode1:i386
  libnvidia-tesla-460-glcore libnvidia-tesla-460-glcore:i386
  libnvidia-tesla-460-glvkspirv libnvidia-tesla-460-glvkspirv:i386
  libnvidia-tesla-460-ml1 libnvidia-tesla-460-nvcuvid1
  libnvidia-tesla-460-nvcuvid1:i386 libnvidia-tesla-460-ptxjitcompiler1
  libnvidia-tesla-460-ptxjitcompiler1:i386 libnvidia-tesla-460-rtcore
  libopengl0:i386 libpciaccess0:i386 libsensors5:i386 libstdc++6:i386
  libtinfo6:i386 libvulkan1:i386 libwayland-client0:i386
  libwayland-server0:i386 libx11-6:i386 libx11-xcb1:i386 libxau6:i386
  libxcb-dri2-0:i386 libxcb-dri3-0:i386 libxcb-glx0:i386 libxcb-present0:i386
  libxcb-randr0:i386 libxcb-shm0:i386 libxcb-sync1:i386 libxcb-xfixes0:i386
  libxcb1:i386 libxdamage1:i386 libxdmcp6:i386 libxext6:i386 libxfixes3:i386
  libxshmfence1:i386 libxxf86vm1:i386 libz3-4:i386 libzstd1:i386
  mesa-vulkan-drivers:i386 nvidia-egl-common nvidia-installer-cleanup
  nvidia-kernel-common nvidia-modprobe nvidia-persistenced
  nvidia-settings-tesla-460 nvidia-support nvidia-tesla-460-alternative
  nvidia-tesla-460-driver-bin nvidia-tesla-460-driver-libs
  nvidia-tesla-460-driver-libs:i386 nvidia-tesla-460-egl-icd
  nvidia-tesla-460-egl-icd:i386 nvidia-tesla-460-kernel-dkms
  nvidia-tesla-460-kernel-support nvidia-tesla-460-smi
  nvidia-tesla-460-vdpau-driver nvidia-tesla-460-vulkan-icd
  nvidia-tesla-460-vulkan-icd:i386 nvidia-vulkan-common update-glx
  xserver-xorg-video-nvidia-tesla-460 zlib1g:i386
Suggested packages:
  nvidia-cuda-mps lm-sensors:i386 libegl-nvidia0 | libegl-nvidia-tesla-450-0
  | libegl-nvidia-tesla-440-0 | libegl-nvidia-tesla-418-0
  | libegl-nvidia-legacy-390xx0 vulkan-utils vulkan-utils:i386
  nvidia-vulkan-icd | nvidia-tesla-450-vulkan-icd
  | nvidia-tesla-440-vulkan-icd | nvidia-tesla-418-vulkan-icd
  | nvidia-legacy-390xx-vulkan-icd
The following NEW packages will be installed:
  glx-alternative-mesa glx-alternative-nvidia glx-diversions libatomic1:i386
  libbsd0:i386 libdrm-amdgpu1:i386 libdrm-intel1:i386 libdrm-nouveau2:i386
  libdrm-radeon1:i386 libdrm2:i386 libedit2:i386 libegl-mesa0:i386
  libegl-nvidia-tesla-460-0 libegl-nvidia-tesla-460-0:i386 libegl1:i386
  libelf1:i386 libexpat1:i386 libffi7:i386 libgbm1:i386 libgl1:i386
  libgl1-mesa-dri:i386 libgl1-nvidia-tesla-460-glvnd-glx
  libgl1-nvidia-tesla-460-glvnd-glx:i386 libglapi-mesa:i386
  libgles-nvidia-tesla-460-1 libgles-nvidia-tesla-460-1:i386
  libgles-nvidia-tesla-460-2 libgles-nvidia-tesla-460-2:i386 libgles1
  libgles1:i386 libgles2:i386 libglvnd0:i386 libglx-mesa0:i386
  libglx-nvidia-tesla-460-0 libglx-nvidia-tesla-460-0:i386 libglx0:i386
  libllvm11:i386 libmd0:i386 libnvidia-tesla-460-cbl libnvidia-tesla-460-cfg1
  libnvidia-tesla-460-cuda1 libnvidia-tesla-460-cuda1:i386
  libnvidia-tesla-460-eglcore libnvidia-tesla-460-eglcore:i386
  libnvidia-tesla-460-encode1 libnvidia-tesla-460-encode1:i386
  libnvidia-tesla-460-glcore libnvidia-tesla-460-glcore:i386
  libnvidia-tesla-460-glvkspirv libnvidia-tesla-460-glvkspirv:i386
  libnvidia-tesla-460-ml1 libnvidia-tesla-460-nvcuvid1
  libnvidia-tesla-460-nvcuvid1:i386 libnvidia-tesla-460-ptxjitcompiler1
  libnvidia-tesla-460-ptxjitcompiler1:i386 libnvidia-tesla-460-rtcore
  libopengl0:i386 libpciaccess0:i386 libsensors5:i386 libstdc++6:i386
  libtinfo6:i386 libvulkan1:i386 libwayland-client0:i386
  libwayland-server0:i386 libx11-6:i386 libx11-xcb1:i386 libxau6:i386
  libxcb-dri2-0:i386 libxcb-dri3-0:i386 libxcb-glx0:i386 libxcb-present0:i386
  libxcb-randr0:i386 libxcb-shm0:i386 libxcb-sync1:i386 libxcb-xfixes0:i386
  libxcb1:i386 libxdamage1:i386 libxdmcp6:i386 libxext6:i386 libxfixes3:i386
  libxshmfence1:i386 libxxf86vm1:i386 libz3-4:i386 libzstd1:i386
  mesa-vulkan-drivers:i386 nvidia-egl-common nvidia-installer-cleanup
  nvidia-kernel-common nvidia-modprobe nvidia-persistenced
  nvidia-settings-tesla-460 nvidia-support nvidia-tesla-460-alternative
  nvidia-tesla-460-driver nvidia-tesla-460-driver-bin
  nvidia-tesla-460-driver-libs nvidia-tesla-460-driver-libs:i386
  nvidia-tesla-460-egl-icd nvidia-tesla-460-egl-icd:i386
  nvidia-tesla-460-kernel-dkms nvidia-tesla-460-kernel-support
  nvidia-tesla-460-smi nvidia-tesla-460-vdpau-driver
  nvidia-tesla-460-vulkan-icd nvidia-tesla-460-vulkan-icd:i386
  nvidia-vulkan-common update-glx xserver-xorg-video-nvidia-tesla-460
  zlib1g:i386
0 upgraded, 109 newly installed, 0 to remove and 0 not upgraded.
Inst update-glx (1.2.1~deb11u1 Debian:11.3/stable [amd64])
Inst glx-alternative-mesa (1.2.1~deb11u1 Debian:11.3/stable [amd64])
Inst nvidia-installer-cleanup (20151021+13 Debian:11.3/stable [amd64])
Conf nvidia-installer-cleanup (20151021+13 Debian:11.3/stable [amd64])
Inst glx-diversions (1.2.1~deb11u1 Debian:11.3/stable [amd64])
Inst glx-alternative-nvidia (1.2.1~deb11u1 Debian:11.3/stable [amd64])
Inst nvidia-tesla-460-alternative (460.91.03-1 Debian:11.3/stable [amd64])
Inst libnvidia-tesla-460-cfg1 (460.91.03-1 Debian:11.3/stable [amd64])
Inst nvidia-persistenced (460.32.03-1 Debian:11.3/stable [amd64])
Inst nvidia-settings-tesla-460 (460.91.03-1 Debian:11.3/stable [amd64])
Inst libnvidia-tesla-460-glcore (460.91.03-1 Debian:11.3/stable [amd64])
Inst libglx-nvidia-tesla-460-0 (460.91.03-1 Debian:11.3/stable [amd64])
Inst libgl1-nvidia-tesla-460-glvnd-glx (460.91.03-1 Debian:11.3/stable [amd64])
Inst nvidia-egl-common (460.91.03-1 Debian:11.3/stable [amd64])
Inst libnvidia-tesla-460-eglcore (460.91.03-1 Debian:11.3/stable [amd64])
Inst libegl-nvidia-tesla-460-0 (460.91.03-1 Debian:11.3/stable [amd64])
Inst nvidia-tesla-460-egl-icd (460.91.03-1 Debian:11.3/stable [amd64])
Inst nvidia-tesla-460-driver-libs (460.91.03-1 Debian:11.3/stable [amd64])
Inst libnvidia-tesla-460-ml1 (460.91.03-1 Debian:11.3/stable [amd64])
Inst nvidia-tesla-460-driver-bin (460.91.03-1 Debian:11.3/stable [amd64])
Inst nvidia-support (20151021+13 Debian:11.3/stable [amd64])
Inst xserver-xorg-video-nvidia-tesla-460 (460.91.03-1 Debian:11.3/stable [amd64])
Inst nvidia-tesla-460-vdpau-driver (460.91.03-1 Debian:11.3/stable [amd64])
Inst nvidia-kernel-common (20151021+13 Debian:11.3/stable [amd64])
Inst nvidia-modprobe (470.103.01-1~deb11u1 Debian:11.3/stable [amd64])
Inst nvidia-tesla-460-kernel-support (460.91.03-1 Debian:11.3/stable [amd64])
Inst nvidia-tesla-460-kernel-dkms (460.91.03-1 Debian:11.3/stable [amd64])
Inst nvidia-tesla-460-driver (460.91.03-1 Debian:11.3/stable [amd64])
Inst libatomic1:i386 (10.2.1-6 Debian:11.3/stable [i386])
Inst libmd0:i386 (1.0.3-3 Debian:11.3/stable [i386])
Inst libbsd0:i386 (0.11.3-1 Debian:11.3/stable [i386])
Inst libdrm2:i386 (2.4.104-1 Debian:11.3/stable [i386])
Inst libdrm-amdgpu1:i386 (2.4.104-1 Debian:11.3/stable [i386])
Inst zlib1g:i386 (1:1.2.11.dfsg-2 Debian:11.3/stable [i386])
Inst libpciaccess0:i386 (0.16-1 Debian:11.3/stable [i386])
Inst libdrm-intel1:i386 (2.4.104-1 Debian:11.3/stable [i386])
Inst libdrm-nouveau2:i386 (2.4.104-1 Debian:11.3/stable [i386])
Inst libdrm-radeon1:i386 (2.4.104-1 Debian:11.3/stable [i386])
Inst libtinfo6:i386 (6.2+20201114-2 Debian:11.3/stable [i386])
Inst libedit2:i386 (3.1-20191231-2+b1 Debian:11.3/stable [i386])
Inst libexpat1:i386 (2.2.10-2+deb11u3 Debian:11.3/stable [i386])
Inst libffi7:i386 (3.3-6 Debian:11.3/stable [i386])
Inst libwayland-server0:i386 (1.18.0-2~exp1.1 Debian:11.3/stable [i386])
Inst libgbm1:i386 (20.3.5-1 Debian:11.3/stable [i386])
Inst libglapi-mesa:i386 (20.3.5-1 Debian:11.3/stable [i386])
Inst libwayland-client0:i386 (1.18.0-2~exp1.1 Debian:11.3/stable [i386])
Inst libxau6:i386 (1:1.0.9-1 Debian:11.3/stable [i386])
Inst libxdmcp6:i386 (1:1.1.2-3 Debian:11.3/stable [i386])
Inst libxcb1:i386 (1.14-3 Debian:11.3/stable [i386])
Inst libx11-6:i386 (2:1.7.2-1 Debian:11.3/stable [i386])
Inst libx11-xcb1:i386 (2:1.7.2-1 Debian:11.3/stable [i386])
Inst libxcb-dri2-0:i386 (1.14-3 Debian:11.3/stable [i386])
Inst libxcb-dri3-0:i386 (1.14-3 Debian:11.3/stable [i386])
Inst libxcb-present0:i386 (1.14-3 Debian:11.3/stable [i386])
Inst libxcb-sync1:i386 (1.14-3 Debian:11.3/stable [i386])
Inst libxcb-xfixes0:i386 (1.14-3 Debian:11.3/stable [i386])
Inst libxshmfence1:i386 (1.3-1 Debian:11.3/stable [i386])
Inst libegl-mesa0:i386 (20.3.5-1 Debian:11.3/stable [i386])
Inst libnvidia-tesla-460-eglcore:i386 (460.91.03-1 Debian:11.3/stable [i386])
Inst libegl-nvidia-tesla-460-0:i386 (460.91.03-1 Debian:11.3/stable [i386])
Inst libelf1:i386 (0.183-1 Debian:11.3/stable [i386])
Inst libstdc++6:i386 (10.2.1-6 Debian:11.3/stable [i386])
Inst libz3-4:i386 (4.8.10-1 Debian:11.3/stable [i386])
Inst libllvm11:i386 (1:11.0.1-2 Debian:11.3/stable [i386])
Inst libsensors5:i386 (1:3.6.0-7 Debian:11.3/stable [i386])
Inst libvulkan1:i386 (1.2.162.0-1 Debian:11.3/stable [i386])
Inst libzstd1:i386 (1.4.8+dfsg-2.1 Debian:11.3/stable [i386])
Inst libgl1-mesa-dri:i386 (20.3.5-1 Debian:11.3/stable [i386])
Inst libglvnd0:i386 (1.3.2-1 Debian:11.3/stable [i386])
Inst libxcb-glx0:i386 (1.14-3 Debian:11.3/stable [i386])
Inst libxcb-shm0:i386 (1.14-3 Debian:11.3/stable [i386])
Inst libxdamage1:i386 (1:1.1.5-2 Debian:11.3/stable [i386])
Inst libxext6:i386 (2:1.3.3-1.1 Debian:11.3/stable [i386])
Inst libxfixes3:i386 (1:5.0.3-2 Debian:11.3/stable [i386])
Inst libxxf86vm1:i386 (1:1.1.4-1+b2 Debian:11.3/stable [i386])
Inst libglx-mesa0:i386 (20.3.5-1 Debian:11.3/stable [i386])
Inst libglx0:i386 (1.3.2-1 Debian:11.3/stable [i386])
Inst libgl1:i386 (1.3.2-1 Debian:11.3/stable [i386])
Inst libnvidia-tesla-460-glcore:i386 (460.91.03-1 Debian:11.3/stable [i386])
Inst libglx-nvidia-tesla-460-0:i386 (460.91.03-1 Debian:11.3/stable [i386])
Inst libgl1-nvidia-tesla-460-glvnd-glx:i386 (460.91.03-1 Debian:11.3/stable [i386])
Inst libgles1:i386 (1.3.2-1 Debian:11.3/stable [i386])
Inst libgles-nvidia-tesla-460-1:i386 (460.91.03-1 Debian:11.3/stable [i386])
Inst libgles1 (1.3.2-1 Debian:11.3/stable [amd64])
Inst libgles-nvidia-tesla-460-1 (460.91.03-1 Debian:11.3/stable [amd64])
Inst libgles-nvidia-tesla-460-2 (460.91.03-1 Debian:11.3/stable [amd64])
Inst libgles2:i386 (1.3.2-1 Debian:11.3/stable [i386])
Inst libgles-nvidia-tesla-460-2:i386 (460.91.03-1 Debian:11.3/stable [i386])
Inst libnvidia-tesla-460-cbl (460.91.03-1 Debian:11.3/stable [amd64])
Inst libnvidia-tesla-460-ptxjitcompiler1:i386 (460.91.03-1 Debian:11.3/stable [i386])
Inst libnvidia-tesla-460-cuda1:i386 (460.91.03-1 Debian:11.3/stable [i386])
Inst libnvidia-tesla-460-ptxjitcompiler1 (460.91.03-1 Debian:11.3/stable [amd64])
Inst libnvidia-tesla-460-cuda1 (460.91.03-1 Debian:11.3/stable [amd64])
Inst libnvidia-tesla-460-nvcuvid1 (460.91.03-1 Debian:11.3/stable [amd64])
Inst libnvidia-tesla-460-encode1 (460.91.03-1 Debian:11.3/stable [amd64])
Inst libnvidia-tesla-460-nvcuvid1:i386 (460.91.03-1 Debian:11.3/stable [i386])
Inst libnvidia-tesla-460-encode1:i386 (460.91.03-1 Debian:11.3/stable [i386])
Inst libnvidia-tesla-460-glvkspirv:i386 (460.91.03-1 Debian:11.3/stable [i386])
Inst libnvidia-tesla-460-glvkspirv (460.91.03-1 Debian:11.3/stable [amd64])
Inst libnvidia-tesla-460-rtcore (460.91.03-1 Debian:11.3/stable [amd64])
Inst libxcb-randr0:i386 (1.14-3 Debian:11.3/stable [i386])
Inst mesa-vulkan-drivers:i386 (20.3.5-1 Debian:11.3/stable [i386])
Inst libegl1:i386 (1.3.2-1 Debian:11.3/stable [i386])
Inst nvidia-tesla-460-egl-icd:i386 (460.91.03-1 Debian:11.3/stable [i386])
Inst nvidia-tesla-460-driver-libs:i386 (460.91.03-1 Debian:11.3/stable [i386])
Inst nvidia-tesla-460-smi (460.91.03-1 Debian:11.3/stable [amd64])
Inst nvidia-vulkan-common (460.91.03-1 Debian:11.3/stable [amd64])
Inst nvidia-tesla-460-vulkan-icd:i386 (460.91.03-1 Debian:11.3/stable [i386])
Inst nvidia-tesla-460-vulkan-icd (460.91.03-1 Debian:11.3/stable [amd64])
Inst libopengl0:i386 (1.3.2-1 Debian:11.3/stable [i386])
Conf update-glx (1.2.1~deb11u1 Debian:11.3/stable [amd64])
Conf glx-alternative-mesa (1.2.1~deb11u1 Debian:11.3/stable [amd64])
Conf glx-diversions (1.2.1~deb11u1 Debian:11.3/stable [amd64])
Conf glx-alternative-nvidia (1.2.1~deb11u1 Debian:11.3/stable [amd64])
Conf nvidia-tesla-460-alternative (460.91.03-1 Debian:11.3/stable [amd64])
Conf libnvidia-tesla-460-cfg1 (460.91.03-1 Debian:11.3/stable [amd64])
Conf nvidia-persistenced (460.32.03-1 Debian:11.3/stable [amd64])
Conf nvidia-settings-tesla-460 (460.91.03-1 Debian:11.3/stable [amd64])
Conf libnvidia-tesla-460-glcore (460.91.03-1 Debian:11.3/stable [amd64])
Conf libglx-nvidia-tesla-460-0 (460.91.03-1 Debian:11.3/stable [amd64])
Conf libgl1-nvidia-tesla-460-glvnd-glx (460.91.03-1 Debian:11.3/stable [amd64])
Conf nvidia-egl-common (460.91.03-1 Debian:11.3/stable [amd64])
Conf libnvidia-tesla-460-eglcore (460.91.03-1 Debian:11.3/stable [amd64])
Conf libegl-nvidia-tesla-460-0 (460.91.03-1 Debian:11.3/stable [amd64])
Conf nvidia-tesla-460-egl-icd (460.91.03-1 Debian:11.3/stable [amd64])
Conf nvidia-tesla-460-driver-libs (460.91.03-1 Debian:11.3/stable [amd64])
Conf libnvidia-tesla-460-ml1 (460.91.03-1 Debian:11.3/stable [amd64])
Conf nvidia-tesla-460-driver-bin (460.91.03-1 Debian:11.3/stable [amd64])
Conf nvidia-support (20151021+13 Debian:11.3/stable [amd64])
Conf xserver-xorg-video-nvidia-tesla-460 (460.91.03-1 Debian:11.3/stable [amd64])
Conf nvidia-tesla-460-vdpau-driver (460.91.03-1 Debian:11.3/stable [amd64])
Conf nvidia-kernel-common (20151021+13 Debian:11.3/stable [amd64])
Conf nvidia-modprobe (470.103.01-1~deb11u1 Debian:11.3/stable [amd64])
Conf nvidia-tesla-460-kernel-support (460.91.03-1 Debian:11.3/stable [amd64])
Conf nvidia-tesla-460-kernel-dkms (460.91.03-1 Debian:11.3/stable [amd64])
Conf nvidia-tesla-460-driver (460.91.03-1 Debian:11.3/stable [amd64])
Conf libatomic1:i386 (10.2.1-6 Debian:11.3/stable [i386])
Conf libmd0:i386 (1.0.3-3 Debian:11.3/stable [i386])
Conf libbsd0:i386 (0.11.3-1 Debian:11.3/stable [i386])
Conf libdrm2:i386 (2.4.104-1 Debian:11.3/stable [i386])
Conf libdrm-amdgpu1:i386 (2.4.104-1 Debian:11.3/stable [i386])
Conf zlib1g:i386 (1:1.2.11.dfsg-2 Debian:11.3/stable [i386])
Conf libpciaccess0:i386 (0.16-1 Debian:11.3/stable [i386])
Conf libdrm-intel1:i386 (2.4.104-1 Debian:11.3/stable [i386])
Conf libdrm-nouveau2:i386 (2.4.104-1 Debian:11.3/stable [i386])
Conf libdrm-radeon1:i386 (2.4.104-1 Debian:11.3/stable [i386])
Conf libtinfo6:i386 (6.2+20201114-2 Debian:11.3/stable [i386])
Conf libedit2:i386 (3.1-20191231-2+b1 Debian:11.3/stable [i386])
Conf libexpat1:i386 (2.2.10-2+deb11u3 Debian:11.3/stable [i386])
Conf libffi7:i386 (3.3-6 Debian:11.3/stable [i386])
Conf libwayland-server0:i386 (1.18.0-2~exp1.1 Debian:11.3/stable [i386])
Conf libgbm1:i386 (20.3.5-1 Debian:11.3/stable [i386])
Conf libglapi-mesa:i386 (20.3.5-1 Debian:11.3/stable [i386])
Conf libwayland-client0:i386 (1.18.0-2~exp1.1 Debian:11.3/stable [i386])
Conf libxau6:i386 (1:1.0.9-1 Debian:11.3/stable [i386])
Conf libxdmcp6:i386 (1:1.1.2-3 Debian:11.3/stable [i386])
Conf libxcb1:i386 (1.14-3 Debian:11.3/stable [i386])
Conf libx11-6:i386 (2:1.7.2-1 Debian:11.3/stable [i386])
Conf libx11-xcb1:i386 (2:1.7.2-1 Debian:11.3/stable [i386])
Conf libxcb-dri2-0:i386 (1.14-3 Debian:11.3/stable [i386])
Conf libxcb-dri3-0:i386 (1.14-3 Debian:11.3/stable [i386])
Conf libxcb-present0:i386 (1.14-3 Debian:11.3/stable [i386])
Conf libxcb-sync1:i386 (1.14-3 Debian:11.3/stable [i386])
Conf libxcb-xfixes0:i386 (1.14-3 Debian:11.3/stable [i386])
Conf libxshmfence1:i386 (1.3-1 Debian:11.3/stable [i386])
Conf libegl-mesa0:i386 (20.3.5-1 Debian:11.3/stable [i386])
Conf libnvidia-tesla-460-eglcore:i386 (460.91.03-1 Debian:11.3/stable [i386])
Conf libegl-nvidia-tesla-460-0:i386 (460.91.03-1 Debian:11.3/stable [i386])
Conf libelf1:i386 (0.183-1 Debian:11.3/stable [i386])
Conf libstdc++6:i386 (10.2.1-6 Debian:11.3/stable [i386])
Conf libz3-4:i386 (4.8.10-1 Debian:11.3/stable [i386])
Conf libllvm11:i386 (1:11.0.1-2 Debian:11.3/stable [i386])
Conf libsensors5:i386 (1:3.6.0-7 Debian:11.3/stable [i386])
Conf libvulkan1:i386 (1.2.162.0-1 Debian:11.3/stable [i386])
Conf libzstd1:i386 (1.4.8+dfsg-2.1 Debian:11.3/stable [i386])
Conf libgl1-mesa-dri:i386 (20.3.5-1 Debian:11.3/stable [i386])
Conf libglvnd0:i386 (1.3.2-1 Debian:11.3/stable [i386])
Conf libxcb-glx0:i386 (1.14-3 Debian:11.3/stable [i386])
Conf libxcb-shm0:i386 (1.14-3 Debian:11.3/stable [i386])
Conf libxdamage1:i386 (1:1.1.5-2 Debian:11.3/stable [i386])
Conf libxext6:i386 (2:1.3.3-1.1 Debian:11.3/stable [i386])
Conf libxfixes3:i386 (1:5.0.3-2 Debian:11.3/stable [i386])
Conf libxxf86vm1:i386 (1:1.1.4-1+b2 Debian:11.3/stable [i386])
Conf libglx-mesa0:i386 (20.3.5-1 Debian:11.3/stable [i386])
Conf libglx0:i386 (1.3.2-1 Debian:11.3/stable [i386])
Conf libgl1:i386 (1.3.2-1 Debian:11.3/stable [i386])
Conf libnvidia-tesla-460-glcore:i386 (460.91.03-1 Debian:11.3/stable [i386])
Conf libglx-nvidia-tesla-460-0:i386 (460.91.03-1 Debian:11.3/stable [i386])
Conf libgl1-nvidia-tesla-460-glvnd-glx:i386 (460.91.03-1 Debian:11.3/stable [i386])
Conf libgles1:i386 (1.3.2-1 Debian:11.3/stable [i386])
Conf libgles-nvidia-tesla-460-1:i386 (460.91.03-1 Debian:11.3/stable [i386])
Conf libgles1 (1.3.2-1 Debian:11.3/stable [amd64])
Conf libgles-nvidia-tesla-460-1 (460.91.03-1 Debian:11.3/stable [amd64])
Conf libgles-nvidia-tesla-460-2 (460.91.03-1 Debian:11.3/stable [amd64])
Conf libgles2:i386 (1.3.2-1 Debian:11.3/stable [i386])
Conf libgles-nvidia-tesla-460-2:i386 (460.91.03-1 Debian:11.3/stable [i386])
Conf libnvidia-tesla-460-cbl (460.91.03-1 Debian:11.3/stable [amd64])
Conf libnvidia-tesla-460-ptxjitcompiler1:i386 (460.91.03-1 Debian:11.3/stable [i386])
Conf libnvidia-tesla-460-cuda1:i386 (460.91.03-1 Debian:11.3/stable [i386])
Conf libnvidia-tesla-460-ptxjitcompiler1 (460.91.03-1 Debian:11.3/stable [amd64])
Conf libnvidia-tesla-460-cuda1 (460.91.03-1 Debian:11.3/stable [amd64])
Conf libnvidia-tesla-460-nvcuvid1 (460.91.03-1 Debian:11.3/stable [amd64])
Conf libnvidia-tesla-460-encode1 (460.91.03-1 Debian:11.3/stable [amd64])
Conf libnvidia-tesla-460-nvcuvid1:i386 (460.91.03-1 Debian:11.3/stable [i386])
Conf libnvidia-tesla-460-encode1:i386 (460.91.03-1 Debian:11.3/stable [i386])
Conf libnvidia-tesla-460-glvkspirv:i386 (460.91.03-1 Debian:11.3/stable [i386])
Conf libnvidia-tesla-460-glvkspirv (460.91.03-1 Debian:11.3/stable [amd64])
Conf libnvidia-tesla-460-rtcore (460.91.03-1 Debian:11.3/stable [amd64])
Conf libxcb-randr0:i386 (1.14-3 Debian:11.3/stable [i386])
Conf mesa-vulkan-drivers:i386 (20.3.5-1 Debian:11.3/stable [i386])
Conf libegl1:i386 (1.3.2-1 Debian:11.3/stable [i386])
Conf nvidia-tesla-460-egl-icd:i386 (460.91.03-1 Debian:11.3/stable [i386])
Conf nvidia-tesla-460-driver-libs:i386 (460.91.03-1 Debian:11.3/stable [i386])
Conf nvidia-tesla-460-smi (460.91.03-1 Debian:11.3/stable [amd64])
Conf nvidia-vulkan-common (460.91.03-1 Debian:11.3/stable [amd64])
Conf nvidia-tesla-460-vulkan-icd:i386 (460.91.03-1 Debian:11.3/stable [i386])
Conf nvidia-tesla-460-vulkan-icd (460.91.03-1 Debian:11.3/stable [amd64])
Conf libopengl0:i386 (1.3.2-1 Debian:11.3/stable [i386])
NOTE: This is only a simulation!
      apt needs root privileges for real execution.
      Keep also in mind that locking is deactivated,
      so don't depend on the relevance to the real current situation!
Reading package lists...
Building dependency tree...
Reading state information...
The following additional packages will be installed:
  glx-alternative-mesa glx-alternative-nvidia glx-diversions libatomic1:i386
  libbsd0:i386 libdrm-amdgpu1:i386 libdrm-intel1:i386 libdrm-nouveau2:i386
  libdrm-radeon1:i386 libdrm2:i386 libedit2:i386 libegl-mesa0:i386
  libegl-nvidia-tesla-460-0 libegl-nvidia-tesla-460-0:i386 libegl1:i386
  libelf1:i386 libexpat1:i386 libffi7:i386 libgbm1:i386 libgl1:i386
  libgl1-mesa-dri:i386 libgl1-nvidia-tesla-460-glvnd-glx
  libgl1-nvidia-tesla-460-glvnd-glx:i386 libglapi-mesa:i386
  libgles-nvidia-tesla-460-1 libgles-nvidia-tesla-460-1:i386
  libgles-nvidia-tesla-460-2 libgles-nvidia-tesla-460-2:i386 libgles1
  libgles1:i386 libgles2:i386 libglvnd0:i386 libglx-mesa0:i386
  libglx-nvidia-tesla-460-0 libglx-nvidia-tesla-460-0:i386 libglx0:i386
  libllvm11:i386 libmd0:i386 libnvidia-tesla-460-cbl libnvidia-tesla-460-cfg1
  libnvidia-tesla-460-cuda1 libnvidia-tesla-460-cuda1:i386
  libnvidia-tesla-460-eglcore libnvidia-tesla-460-eglcore:i386
  libnvidia-tesla-460-encode1 libnvidia-tesla-460-encode1:i386
  libnvidia-tesla-460-glcore libnvidia-tesla-460-glcore:i386
  libnvidia-tesla-460-glvkspirv libnvidia-tesla-460-glvkspirv:i386
  libnvidia-tesla-460-ml1 libnvidia-tesla-460-nvcuvid1
  libnvidia-tesla-460-nvcuvid1:i386 libnvidia-tesla-460-ptxjitcompiler1
  libnvidia-tesla-460-ptxjitcompiler1:i386 libnvidia-tesla-460-rtcore
  libopengl0:i386 libpciaccess0:i386 libsensors5:i386 libstdc++6:i386
  libtinfo6:i386 libvulkan1:i386 libwayland-client0:i386
  libwayland-server0:i386 libx11-6:i386 libx11-xcb1:i386 libxau6:i386
  libxcb-dri2-0:i386 libxcb-dri3-0:i386 libxcb-glx0:i386 libxcb-present0:i386
  libxcb-randr0:i386 libxcb-shm0:i386 libxcb-sync1:i386 libxcb-xfixes0:i386
  libxcb1:i386 libxdamage1:i386 libxdmcp6:i386 libxext6:i386 libxfixes3:i386
  libxshmfence1:i386 libxxf86vm1:i386 libz3-4:i386 libzstd1:i386
  mesa-vulkan-drivers:i386 nvidia-egl-common nvidia-installer-cleanup
  nvidia-kernel-common nvidia-modprobe nvidia-persistenced
  nvidia-settings-tesla-460 nvidia-support nvidia-tesla-460-alternative
  nvidia-tesla-460-driver-bin nvidia-tesla-460-driver-libs
  nvidia-tesla-460-driver-libs:i386 nvidia-tesla-460-egl-icd
  nvidia-tesla-460-egl-icd:i386 nvidia-tesla-460-kernel-dkms
  nvidia-tesla-460-kernel-support nvidia-tesla-460-smi
  nvidia-tesla-460-vdpau-driver nvidia-tesla-460-vulkan-icd
  nvidia-tesla-460-vulkan-icd:i386 nvidia-vulkan-common update-glx
  xserver-xorg-video-nvidia-tesla-460 zlib1g:i386
Suggested packages:
  nvidia-cuda-mps lm-sensors:i386 libegl-nvidia0 | libegl-nvidia-tesla-450-0
  | libegl-nvidia-tesla-440-0 | libegl-nvidia-tesla-418-0
  | libegl-nvidia-legacy-390xx0 vulkan-utils vulkan-utils:i386
  nvidia-vulkan-icd | nvidia-tesla-450-vulkan-icd
  | nvidia-tesla-440-vulkan-icd | nvidia-tesla-418-vulkan-icd
  | nvidia-legacy-390xx-vulkan-icd
The following NEW packages will be installed:
  glx-alternative-mesa glx-alternative-nvidia glx-diversions libatomic1:i386
  libbsd0:i386 libdrm-amdgpu1:i386 libdrm-intel1:i386 libdrm-nouveau2:i386
  libdrm-radeon1:i386 libdrm2:i386 libedit2:i386 libegl-mesa0:i386
  libegl-nvidia-tesla-460-0 libegl-nvidia-tesla-460-0:i386 libegl1:i386
  libelf1:i386 libexpat1:i386 libffi7:i386 libgbm1:i386 libgl1:i386
  libgl1-mesa-dri:i386 libgl1-nvidia-tesla-460-glvnd-glx
  libgl1-nvidia-tesla-460-glvnd-glx:i386 libglapi-mesa:i386
  libgles-nvidia-tesla-460-1 libgles-nvidia-tesla-460-1:i386
  libgles-nvidia-tesla-460-2 libgles-nvidia-tesla-460-2:i386 libgles1
  libgles1:i386 libgles2:i386 libglvnd0:i386 libglx-mesa0:i386
  libglx-nvidia-tesla-460-0 libglx-nvidia-tesla-460-0:i386 libglx0:i386
  libllvm11:i386 libmd0:i386 libnvidia-tesla-460-cbl libnvidia-tesla-460-cfg1
  libnvidia-tesla-460-cuda1 libnvidia-tesla-460-cuda1:i386
  libnvidia-tesla-460-eglcore libnvidia-tesla-460-eglcore:i386
  libnvidia-tesla-460-encode1 libnvidia-tesla-460-encode1:i386
  libnvidia-tesla-460-glcore libnvidia-tesla-460-glcore:i386
  libnvidia-tesla-460-glvkspirv libnvidia-tesla-460-glvkspirv:i386
  libnvidia-tesla-460-ml1 libnvidia-tesla-460-nvcuvid1
  libnvidia-tesla-460-nvcuvid1:i386 libnvidia-tesla-460-ptxjitcompiler1
  libnvidia-tesla-460-ptxjitcompiler1:i386 libnvidia-tesla-460-rtcore
  libopengl0:i386 libpciaccess0:i386 libsensors5:i386 libstdc++6:i386
  libtinfo6:i386 libvulkan1:i386 libwayland-client0:i386
  libwayland-server0:i386 libx11-6:i386 libx11-xcb1:i386 libxau6:i386
  libxcb-dri2-0:i386 libxcb-dri3-0:i386 libxcb-glx0:i386 libxcb-present0:i386
  libxcb-randr0:i386 libxcb-shm0:i386 libxcb-sync1:i386 libxcb-xfixes0:i386
  libxcb1:i386 libxdamage1:i386 libxdmcp6:i386 libxext6:i386 libxfixes3:i386
  libxshmfence1:i386 libxxf86vm1:i386 libz3-4:i386 libzstd1:i386
  mesa-vulkan-drivers:i386 nvidia-egl-common nvidia-installer-cleanup
  nvidia-kernel-common nvidia-modprobe nvidia-persistenced
  nvidia-settings-tesla-460 nvidia-support nvidia-tesla-460-alternative
  nvidia-tesla-460-driver nvidia-tesla-460-driver-bin
  nvidia-tesla-460-driver-libs nvidia-tesla-460-driver-libs:i386
  nvidia-tesla-460-egl-icd nvidia-tesla-460-egl-icd:i386
  nvidia-tesla-460-kernel-dkms nvidia-tesla-460-kernel-support
  nvidia-tesla-460-smi nvidia-tesla-460-vdpau-driver
  nvidia-tesla-460-vulkan-icd nvidia-tesla-460-vulkan-icd:i386
  nvidia-vulkan-common update-glx xserver-xorg-video-nvidia-tesla-460
  zlib1g:i386
0 upgraded, 109 newly installed, 0 to remove and 0 not upgraded.
Inst update-glx (1.2.1~deb11u1 Debian:11.3/stable [amd64])
Inst glx-alternative-mesa (1.2.1~deb11u1 Debian:11.3/stable [amd64])
Inst nvidia-installer-cleanup (20151021+13 Debian:11.3/stable [amd64])
Conf nvidia-installer-cleanup (20151021+13 Debian:11.3/stable [amd64])
Inst glx-diversions (1.2.1~deb11u1 Debian:11.3/stable [amd64])
Inst glx-alternative-nvidia (1.2.1~deb11u1 Debian:11.3/stable [amd64])
Inst nvidia-tesla-460-alternative (460.91.03-1 Debian:11.3/stable [amd64])
Inst libnvidia-tesla-460-cfg1 (460.91.03-1 Debian:11.3/stable [amd64])
Inst nvidia-persistenced (460.32.03-1 Debian:11.3/stable [amd64])
Inst nvidia-settings-tesla-460 (460.91.03-1 Debian:11.3/stable [amd64])
Inst libnvidia-tesla-460-glcore (460.91.03-1 Debian:11.3/stable [amd64])
Inst libglx-nvidia-tesla-460-0 (460.91.03-1 Debian:11.3/stable [amd64])
Inst libgl1-nvidia-tesla-460-glvnd-glx (460.91.03-1 Debian:11.3/stable [amd64])
Inst nvidia-egl-common (460.91.03-1 Debian:11.3/stable [amd64])
Inst libnvidia-tesla-460-eglcore (460.91.03-1 Debian:11.3/stable [amd64])
Inst libegl-nvidia-tesla-460-0 (460.91.03-1 Debian:11.3/stable [amd64])
Inst nvidia-tesla-460-egl-icd (460.91.03-1 Debian:11.3/stable [amd64])
Inst nvidia-tesla-460-driver-libs (460.91.03-1 Debian:11.3/stable [amd64])
Inst libnvidia-tesla-460-ml1 (460.91.03-1 Debian:11.3/stable [amd64])
Inst nvidia-tesla-460-driver-bin (460.91.03-1 Debian:11.3/stable [amd64])
Inst nvidia-support (20151021+13 Debian:11.3/stable [amd64])
Inst xserver-xorg-video-nvidia-tesla-460 (460.91.03-1 Debian:11.3/stable [amd64])
Inst nvidia-tesla-460-vdpau-driver (460.91.03-1 Debian:11.3/stable [amd64])
Inst nvidia-kernel-common (20151021+13 Debian:11.3/stable [amd64])
Inst nvidia-modprobe (470.103.01-1~deb11u1 Debian:11.3/stable [amd64])
Inst nvidia-tesla-460-kernel-support (460.91.03-1 Debian:11.3/stable [amd64])
Inst nvidia-tesla-460-kernel-dkms (460.91.03-1 Debian:11.3/stable [amd64])
Inst nvidia-tesla-460-driver (460.91.03-1 Debian:11.3/stable [amd64])
Inst libatomic1:i386 (10.2.1-6 Debian:11.3/stable [i386])
Inst libmd0:i386 (1.0.3-3 Debian:11.3/stable [i386])
Inst libbsd0:i386 (0.11.3-1 Debian:11.3/stable [i386])
Inst libdrm2:i386 (2.4.104-1 Debian:11.3/stable [i386])
Inst libdrm-amdgpu1:i386 (2.4.104-1 Debian:11.3/stable [i386])
Inst zlib1g:i386 (1:1.2.11.dfsg-2 Debian:11.3/stable [i386])
Inst libpciaccess0:i386 (0.16-1 Debian:11.3/stable [i386])
Inst libdrm-intel1:i386 (2.4.104-1 Debian:11.3/stable [i386])
Inst libdrm-nouveau2:i386 (2.4.104-1 Debian:11.3/stable [i386])
Inst libdrm-radeon1:i386 (2.4.104-1 Debian:11.3/stable [i386])
Inst libtinfo6:i386 (6.2+20201114-2 Debian:11.3/stable [i386])
Inst libedit2:i386 (3.1-20191231-2+b1 Debian:11.3/stable [i386])
Inst libexpat1:i386 (2.2.10-2+deb11u3 Debian:11.3/stable [i386])
Inst libffi7:i386 (3.3-6 Debian:11.3/stable [i386])
Inst libwayland-server0:i386 (1.18.0-2~exp1.1 Debian:11.3/stable [i386])
Inst libgbm1:i386 (20.3.5-1 Debian:11.3/stable [i386])
Inst libglapi-mesa:i386 (20.3.5-1 Debian:11.3/stable [i386])
Inst libwayland-client0:i386 (1.18.0-2~exp1.1 Debian:11.3/stable [i386])
Inst libxau6:i386 (1:1.0.9-1 Debian:11.3/stable [i386])
Inst libxdmcp6:i386 (1:1.1.2-3 Debian:11.3/stable [i386])
Inst libxcb1:i386 (1.14-3 Debian:11.3/stable [i386])
Inst libx11-6:i386 (2:1.7.2-1 Debian:11.3/stable [i386])
Inst libx11-xcb1:i386 (2:1.7.2-1 Debian:11.3/stable [i386])
Inst libxcb-dri2-0:i386 (1.14-3 Debian:11.3/stable [i386])
Inst libxcb-dri3-0:i386 (1.14-3 Debian:11.3/stable [i386])
Inst libxcb-present0:i386 (1.14-3 Debian:11.3/stable [i386])
Inst libxcb-sync1:i386 (1.14-3 Debian:11.3/stable [i386])
Inst libxcb-xfixes0:i386 (1.14-3 Debian:11.3/stable [i386])
Inst libxshmfence1:i386 (1.3-1 Debian:11.3/stable [i386])
Inst libegl-mesa0:i386 (20.3.5-1 Debian:11.3/stable [i386])
Inst libnvidia-tesla-460-eglcore:i386 (460.91.03-1 Debian:11.3/stable [i386])
Inst libegl-nvidia-tesla-460-0:i386 (460.91.03-1 Debian:11.3/stable [i386])
Inst libelf1:i386 (0.183-1 Debian:11.3/stable [i386])
Inst libstdc++6:i386 (10.2.1-6 Debian:11.3/stable [i386])
Inst libz3-4:i386 (4.8.10-1 Debian:11.3/stable [i386])
Inst libllvm11:i386 (1:11.0.1-2 Debian:11.3/stable [i386])
Inst libsensors5:i386 (1:3.6.0-7 Debian:11.3/stable [i386])
Inst libvulkan1:i386 (1.2.162.0-1 Debian:11.3/stable [i386])
Inst libzstd1:i386 (1.4.8+dfsg-2.1 Debian:11.3/stable [i386])
Inst libgl1-mesa-dri:i386 (20.3.5-1 Debian:11.3/stable [i386])
Inst libglvnd0:i386 (1.3.2-1 Debian:11.3/stable [i386])
Inst libxcb-glx0:i386 (1.14-3 Debian:11.3/stable [i386])
Inst libxcb-shm0:i386 (1.14-3 Debian:11.3/stable [i386])
Inst libxdamage1:i386 (1:1.1.5-2 Debian:11.3/stable [i386])
Inst libxext6:i386 (2:1.3.3-1.1 Debian:11.3/stable [i386])
Inst libxfixes3:i386 (1:5.0.3-2 Debian:11.3/stable [i386])
Inst libxxf86vm1:i386 (1:1.1.4-1+b2 Debian:11.3/stable [i386])
Inst libglx-mesa0:i386 (20.3.5-1 Debian:11.3/stable [i386])
Inst libglx0:i386 (1.3.2-1 Debian:11.3/stable [i386])
Inst libgl1:i386 (1.3.2-1 Debian:11.3/stable [i386])
Inst libnvidia-tesla-460-glcore:i386 (460.91.03-1 Debian:11.3/stable [i386])
Inst libglx-nvidia-tesla-460-0:i386 (460.91.03-1 Debian:11.3/stable [i386])
Inst libgl1-nvidia-tesla-460-glvnd-glx:i386 (460.91.03-1 Debian:11.3/stable [i386])
Inst libgles1:i386 (1.3.2-1 Debian:11.3/stable [i386])
Inst libgles-nvidia-tesla-460-1:i386 (460.91.03-1 Debian:11.3/stable [i386])
Inst libgles1 (1.3.2-1 Debian:11.3/stable [amd64])
Inst libgles-nvidia-tesla-460-1 (460.91.03-1 Debian:11.3/stable [amd64])
Inst libgles-nvidia-tesla-460-2 (460.91.03-1 Debian:11.3/stable [amd64])
Inst libgles2:i386 (1.3.2-1 Debian:11.3/stable [i386])
Inst libgles-nvidia-tesla-460-2:i386 (460.91.03-1 Debian:11.3/stable [i386])
Inst libnvidia-tesla-460-cbl (460.91.03-1 Debian:11.3/stable [amd64])
Inst libnvidia-tesla-460-ptxjitcompiler1:i386 (460.91.03-1 Debian:11.3/stable [i386])
Inst libnvidia-tesla-460-cuda1:i386 (460.91.03-1 Debian:11.3/stable [i386])
Inst libnvidia-tesla-460-ptxjitcompiler1 (460.91.03-1 Debian:11.3/stable [amd64])
Inst libnvidia-tesla-460-cuda1 (460.91.03-1 Debian:11.3/stable [amd64])
Inst libnvidia-tesla-460-nvcuvid1 (460.91.03-1 Debian:11.3/stable [amd64])
Inst libnvidia-tesla-460-encode1 (460.91.03-1 Debian:11.3/stable [amd64])
Inst libnvidia-tesla-460-nvcuvid1:i386 (460.91.03-1 Debian:11.3/stable [i386])
Inst libnvidia-tesla-460-encode1:i386 (460.91.03-1 Debian:11.3/stable [i386])
Inst libnvidia-tesla-460-glvkspirv:i386 (460.91.03-1 Debian:11.3/stable [i386])
Inst libnvidia-tesla-460-glvkspirv (460.91.03-1 Debian:11.3/stable [amd64])
Inst libnvidia-tesla-460-rtcore (460.91.03-1 Debian:11.3/stable [amd64])
Inst libxcb-randr0:i386 (1.14-3 Debian:11.3/stable [i386])
Inst mesa-vulkan-drivers:i386 (20.3.5-1 Debian:11.3/stable [i386])
Inst libegl1:i386 (1.3.2-1 Debian:11.3/stable [i386])
Inst nvidia-tesla-460-egl-icd:i386 (460.91.03-1 Debian:11.3/stable [i386])
Inst nvidia-tesla-460-driver-libs:i386 (460.91.03-1 Debian:11.3/stable [i386])
Inst nvidia-tesla-460-smi (460.91.03-1 Debian:11.3/stable [amd64])
Inst nvidia-vulkan-common (460.91.03-1 Debian:11.3/stable [amd64])
Inst nvidia-tesla-460-vulkan-icd:i386 (460.91.03-1 Debian:11.3/stable [i386])
Inst nvidia-tesla-460-vulkan-icd (460.91.03-1 Debian:11.3/stable [amd64])
Inst libopengl0:i386 (1.3.2-1 Debian:11.3/stable [i386])
Conf update-glx (1.2.1~deb11u1 Debian:11.3/stable [amd64])
Conf glx-alternative-mesa (1.2.1~deb11u1 Debian:11.3/stable [amd64])
Conf glx-diversions (1.2.1~deb11u1 Debian:11.3/stable [amd64])
Conf glx-alternative-nvidia (1.2.1~deb11u1 Debian:11.3/stable [amd64])
Conf nvidia-tesla-460-alternative (460.91.03-1 Debian:11.3/stable [amd64])
Conf libnvidia-tesla-460-cfg1 (460.91.03-1 Debian:11.3/stable [amd64])
Conf nvidia-persistenced (460.32.03-1 Debian:11.3/stable [amd64])
Conf nvidia-settings-tesla-460 (460.91.03-1 Debian:11.3/stable [amd64])
Conf libnvidia-tesla-460-glcore (460.91.03-1 Debian:11.3/stable [amd64])
Conf libglx-nvidia-tesla-460-0 (460.91.03-1 Debian:11.3/stable [amd64])
Conf libgl1-nvidia-tesla-460-glvnd-glx (460.91.03-1 Debian:11.3/stable [amd64])
Conf nvidia-egl-common (460.91.03-1 Debian:11.3/stable [amd64])
Conf libnvidia-tesla-460-eglcore (460.91.03-1 Debian:11.3/stable [amd64])
Conf libegl-nvidia-tesla-460-0 (460.91.03-1 Debian:11.3/stable [amd64])
Conf nvidia-tesla-460-egl-icd (460.91.03-1 Debian:11.3/stable [amd64])
Conf nvidia-tesla-460-driver-libs (460.91.03-1 Debian:11.3/stable [amd64])
Conf libnvidia-tesla-460-ml1 (460.91.03-1 Debian:11.3/stable [amd64])
Conf nvidia-tesla-460-driver-bin (460.91.03-1 Debian:11.3/stable [amd64])
Conf nvidia-support (20151021+13 Debian:11.3/stable [amd64])
Conf xserver-xorg-video-nvidia-tesla-460 (460.91.03-1 Debian:11.3/stable [amd64])
Conf nvidia-tesla-460-vdpau-driver (460.91.03-1 Debian:11.3/stable [amd64])
Conf nvidia-kernel-common (20151021+13 Debian:11.3/stable [amd64])
Conf nvidia-modprobe (470.103.01-1~deb11u1 Debian:11.3/stable [amd64])
Conf nvidia-tesla-460-kernel-support (460.91.03-1 Debian:11.3/stable [amd64])
Conf nvidia-tesla-460-kernel-dkms (460.91.03-1 Debian:11.3/stable [amd64])
Conf nvidia-tesla-460-driver (460.91.03-1 Debian:11.3/stable [amd64])
Conf libatomic1:i386 (10.2.1-6 Debian:11.3/stable [i386])
Conf libmd0:i386 (1.0.3-3 Debian:11.3/stable [i386])
Conf libbsd0:i386 (0.11.3-1 Debian:11.3/stable [i386])
Conf libdrm2:i386 (2.4.104-1 Debian:11.3/stable [i386])
Conf libdrm-amdgpu1:i386 (2.4.104-1 Debian:11.3/stable [i386])
Conf zlib1g:i386 (1:1.2.11.dfsg-2 Debian:11.3/stable [i386])
Conf libpciaccess0:i386 (0.16-1 Debian:11.3/stable [i386])
Conf libdrm-intel1:i386 (2.4.104-1 Debian:11.3/stable [i386])
Conf libdrm-nouveau2:i386 (2.4.104-1 Debian:11.3/stable [i386])
Conf libdrm-radeon1:i386 (2.4.104-1 Debian:11.3/stable [i386])
Conf libtinfo6:i386 (6.2+20201114-2 Debian:11.3/stable [i386])
Conf libedit2:i386 (3.1-20191231-2+b1 Debian:11.3/stable [i386])
Conf libexpat1:i386 (2.2.10-2+deb11u3 Debian:11.3/stable [i386])
Conf libffi7:i386 (3.3-6 Debian:11.3/stable [i386])
Conf libwayland-server0:i386 (1.18.0-2~exp1.1 Debian:11.3/stable [i386])
Conf libgbm1:i386 (20.3.5-1 Debian:11.3/stable [i386])
Conf libglapi-mesa:i386 (20.3.5-1 Debian:11.3/stable [i386])
Conf libwayland-client0:i386 (1.18.0-2~exp1.1 Debian:11.3/stable [i386])
Conf libxau6:i386 (1:1.0.9-1 Debian:11.3/stable [i386])
Conf libxdmcp6:i386 (1:1.1.2-3 Debian:11.3/stable [i386])
Conf libxcb1:i386 (1.14-3 Debian:11.3/stable [i386])
Conf libx11-6:i386 (2:1.7.2-1 Debian:11.3/stable [i386])
Conf libx11-xcb1:i386 (2:1.7.2-1 Debian:11.3/stable [i386])
Conf libxcb-dri2-0:i386 (1.14-3 Debian:11.3/stable [i386])
Conf libxcb-dri3-0:i386 (1.14-3 Debian:11.3/stable [i386])
Conf libxcb-present0:i386 (1.14-3 Debian:11.3/stable [i386])
Conf libxcb-sync1:i386 (1.14-3 Debian:11.3/stable [i386])
Conf libxcb-xfixes0:i386 (1.14-3 Debian:11.3/stable [i386])
Conf libxshmfence1:i386 (1.3-1 Debian:11.3/stable [i386])
Conf libegl-mesa0:i386 (20.3.5-1 Debian:11.3/stable [i386])
Conf libnvidia-tesla-460-eglcore:i386 (460.91.03-1 Debian:11.3/stable [i386])
Conf libegl-nvidia-tesla-460-0:i386 (460.91.03-1 Debian:11.3/stable [i386])
Conf libelf1:i386 (0.183-1 Debian:11.3/stable [i386])
Conf libstdc++6:i386 (10.2.1-6 Debian:11.3/stable [i386])
Conf libz3-4:i386 (4.8.10-1 Debian:11.3/stable [i386])
Conf libllvm11:i386 (1:11.0.1-2 Debian:11.3/stable [i386])
Conf libsensors5:i386 (1:3.6.0-7 Debian:11.3/stable [i386])
Conf libvulkan1:i386 (1.2.162.0-1 Debian:11.3/stable [i386])
Conf libzstd1:i386 (1.4.8+dfsg-2.1 Debian:11.3/stable [i386])
Conf libgl1-mesa-dri:i386 (20.3.5-1 Debian:11.3/stable [i386])
Conf libglvnd0:i386 (1.3.2-1 Debian:11.3/stable [i386])
Conf libxcb-glx0:i386 (1.14-3 Debian:11.3/stable [i386])
Conf libxcb-shm0:i386 (1.14-3 Debian:11.3/stable [i386])
Conf libxdamage1:i386 (1:1.1.5-2 Debian:11.3/stable [i386])
Conf libxext6:i386 (2:1.3.3-1.1 Debian:11.3/stable [i386])
Conf libxfixes3:i386 (1:5.0.3-2 Debian:11.3/stable [i386])
Conf libxxf86vm1:i386 (1:1.1.4-1+b2 Debian:11.3/stable [i386])
Conf libglx-mesa0:i386 (20.3.5-1 Debian:11.3/stable [i386])
Conf libglx0:i386 (1.3.2-1 Debian:11.3/stable [i386])
Conf libgl1:i386 (1.3.2-1 Debian:11.3/stable [i386])
Conf libnvidia-tesla-460-glcore:i386 (460.91.03-1 Debian:11.3/stable [i386])
Conf libglx-nvidia-tesla-460-0:i386 (460.91.03-1 Debian:11.3/stable [i386])
Conf libgl1-nvidia-tesla-460-glvnd-glx:i386 (460.91.03-1 Debian:11.3/stable [i386])
Conf libgles1:i386 (1.3.2-1 Debian:11.3/stable [i386])
Conf libgles-nvidia-tesla-460-1:i386 (460.91.03-1 Debian:11.3/stable [i386])
Conf libgles1 (1.3.2-1 Debian:11.3/stable [amd64])
Conf libgles-nvidia-tesla-460-1 (460.91.03-1 Debian:11.3/stable [amd64])
Conf libgles-nvidia-tesla-460-2 (460.91.03-1 Debian:11.3/stable [amd64])
Conf libgles2:i386 (1.3.2-1 Debian:11.3/stable [i386])
Conf libgles-nvidia-tesla-460-2:i386 (460.91.03-1 Debian:11.3/stable [i386])
Conf libnvidia-tesla-460-cbl (460.91.03-1 Debian:11.3/stable [amd64])
Conf libnvidia-tesla-460-ptxjitcompiler1:i386 (460.91.03-1 Debian:11.3/stable [i386])
Conf libnvidia-tesla-460-cuda1:i386 (460.91.03-1 Debian:11.3/stable [i386])
Conf libnvidia-tesla-460-ptxjitcompiler1 (460.91.03-1 Debian:11.3/stable [amd64])
Conf libnvidia-tesla-460-cuda1 (460.91.03-1 Debian:11.3/stable [amd64])
Conf libnvidia-tesla-460-nvcuvid1 (460.91.03-1 Debian:11.3/stable [amd64])
Conf libnvidia-tesla-460-encode1 (460.91.03-1 Debian:11.3/stable [amd64])
Conf libnvidia-tesla-460-nvcuvid1:i386 (460.91.03-1 Debian:11.3/stable [i386])
Conf libnvidia-tesla-460-encode1:i386 (460.91.03-1 Debian:11.3/stable [i386])
Conf libnvidia-tesla-460-glvkspirv:i386 (460.91.03-1 Debian:11.3/stable [i386])
Conf libnvidia-tesla-460-glvkspirv (460.91.03-1 Debian:11.3/stable [amd64])
Conf libnvidia-tesla-460-rtcore (460.91.03-1 Debian:11.3/stable [amd64])
Conf libxcb-randr0:i386 (1.14-3 Debian:11.3/stable [i386])
Conf mesa-vulkan-drivers:i386 (20.3.5-1 Debian:11.3/stable [i386])
Conf libegl1:i386 (1.3.2-1 Debian:11.3/stable [i386])
Conf nvidia-tesla-460-egl-icd:i386 (460.91.03-1 Debian:11.3/stable [i386])
Conf nvidia-tesla-460-driver-libs:i386 (460.91.03-1 Debian:11.3/stable [i386])
Conf nvidia-tesla-460-smi (460.91.03-1 Debian:11.3/stable [amd64])
Conf nvidia-vulkan-common (460.91.03-1 Debian:11.3/stable [amd64])
Conf nvidia-tesla-460-vulkan-icd:i386 (460.91.03-1 Debian:11.3/stable [i386])
Conf nvidia-tesla-460-vulkan-icd (460.91.03-1 Debian:11.3/stable [amd64])
Conf libopengl0:i386 (1.3.2-1 Debian:11.3/stable [i386])



read -p 'please set Timezone and hit ENTER' mytmp
cat /media/user/DEB_STUFF/_bashrc >> ~/.bashrc
cp /media/user/DEB_STUFF/_vimrc >> ~/.vimrc
sudo cp /media/user/DEB_STUFF/python4.pl /usr/bin/python4.pl
ln -s /media/user/DEB_STUFF/dot_steam ~/.steam
ln -s /media/user/DEB_STUFF/dot_local_share_Steam ~/.local/share/Steam
ln -s /media/user/DEB_STUFF/dot_cache_mesa_shader_cache ~/.cache/mesa_shader_cache
ln -s /media/user/DEB_STUFF/dot_local_share_volition ~/.local/share/volition
sudo dpkg --add-architecture i386
sudo systemctl stop cups exim4 cups-browsed avahi-daemon avahi-daemon.socket cron anacron.timer

sudo /usr/sbin/swapon /dev/sda2
sudo apt purge $(dpkg -l | egrep -i 'mozi|fox' | awk '{print $2}' | tr '\n' ' ') epiphany-browser konqueror chromium

rmdir ~/Documents
ln -s /media/user/DEB_STUFF/Documents ~/Documents
echo 'now go to /media/user/DEB_STUFF and install all of the software you want'
echo 'oh yeah, change your wallpaper, remove Desktop icons, and get rid of some ~/ directories'

w3c-linkchecker w3c-markup-validator #requires apache2, ugh.
translate-shell #google-translate-cli
What I installed in Steam:
  Team Fortress 2 (FREE)
  Left 4 Dead 2
  Torchlight 2
  Saints Row 2 (actually runs native!)
What I installed in WINE:
  Battle.net app and then through that got World of Warcraft working
  I plan on installing Diablo II && Diablo II: LoD
#!/usr/bin/perl


if(@ARGV == 0){exit 1;}
#print 'python4 script: ', $ARGV[0];
my $python4_script = $ARGV[0];

my $python3_script = $python4_script;
$python3_script =~ s/(.*)\.py/$1.py.py/ or exit 2;

open my $FH, "<$python4_script" or die $!;
open my $FH2, ">$python3_script" or die $!;
while(<$FH>){
  my $line = $_;
  $line =~ s/^[{}]$//;
  printf $FH2 "%s", $line;
}

close $FH;
close $FH2; 
my @args = (@ARGV);
shift @args;
for(@args){s#'#'\\''#g;s/^/'/;s/$/'/;}
my $args = join ' ', @args;
@args = ();
system("python3 $python3_script $args");
1.6M	dash-0.5.11+git20200708+dd9ef66/
39M	bash-5.1/
109M	perl-5.32.1/


" :syntax on    to enable syntax highlighting supposidly
:se ts=2
:se sw=2
:se expandtab

"I love the arrow keys.
noremap <right> :bn<CR>
noremap <left> :bp<CR>
noremap <up> <PageUp>
noremap <down> <PageDown>

"searching in your file should be case insensitive
:se ignorecase

dmesg | grep iwlwifi #show possible wireless driver-related debugging information (or `iwevent` to watch)

lshw -C network
lspci | vim - #look up iwlwifi to see if it's loaded by kernel
sudo iw dev #list wireless interfaces
lsmod | vim - #look up iwlwifi to see if it's loaded by kernel
sudo rm /etc/NetworkManager/system-connections/* #remove wireless AP stored information
sudo ifconfig wlp1s0 #bring up stats
sudo iw reg get #840 is USA, 00 probably works
sudo nano /etc/defaults/crda #REGDOMAIN=US

#try disabling IPv6 somehow (lol)

sudo modprobe -r ath9k; sleep 3.2s; sudo modprobe ath9k
sudo modprobe -r iwlwifi; sleep 3.2s; sudo modprobe iwlwifi 11n_disable=8
sudo modprobe -r iwlwifi; sleep 3.2s; sudo modprobe iwlwifi 11n_disable=1

sudo systemctl restart networking NetworkManager wpa_supplicant systemd-modules-load
http://16s.us/blackhash/
http://3proxy.ru/
http://aconole.brad-x.com/programs/sfuzz.html
http://airpwn.sourceforge.net
http://alexthepuffin.googlepages.com/
http://anontwi.sourceforge.net/
http://aphopper.sourceforge.net/
http://arpon.sourceforge.net/
http://bastard.sourceforge.net/libdisasm.html
http://bastianborn.de/radio-clock-hack/
http://beefproject.com/
http://binary.ninja/demo.html
http://bitbucket.org/haypo/fusil/wiki/Home
http://bittwist.sourceforge.net/
http://blindelephant.sourceforge.net/
http://blog.didierstevens.com/programs/oledump-py/
http://blog.didierstevens.com/programs/pdf-tools/
http://blog.didierstevens.com/programs/xorsearch/
http://blog.fefe.de/?ts=b6cea88d
http://blog.sipvicious.org/
http://blog.taddong.com/2011/05/tlssled-v10.html
http://blog.techstacks.com/cryptonark.html
http://bluediving.sourceforge.net/
http://bluetooth-pentest.narod.ru/
http://braces.shmoo.com/
http://btb.banquise.net/
http://bvi.sourceforge.net/
http://c3rb3r.openwall.net/mdcrack/
http://chaosreader.sourceforge.net/
http://chaptersinwebsecurity.blogspot.de/2008/11/multiinjector-v03-released.html
http://chdir.org/~nico/ilty/
http://citp.princeton.edu/memory/code/
http://code.activestate.com/recipes/491264/
http://code.google.com/p/bsqlbf-v2/
http://code.google.com/p/davtest/
http://code.google.com/p/dex2jar
http://code.google.com/p/dnsbf
http://code.google.com/p/fern-wifi-cracker/
http://code.google.com/p/fimap/
http://code.google.com/p/flunym0us/
http://code.google.com/p/geoipgen/
http://code.google.com/p/ghost-phisher
http://code.google.com/p/grokevt/
http://code.google.com/p/iaxscan/
http://code.google.com/p/keimpx/
http://code.google.com/p/kismet2earth/
http://code.google.com/p/littleblackbox/wiki/FAQ
http://code.google.com/p/mfcuk/
http://code.google.com/p/middler/
http://code.google.com/p/miranda-upnp/
http://code.google.com/p/nfc-tools
http://code.google.com/p/ratproxy/
http://code.google.com/p/rebind/
http://code.google.com/p/rfcat
http://code.google.com/p/secscan-py/
http://code.google.com/p/skipfish/
http://code.google.com/p/swfintruder/
http://code.google.com/p/tcpjunk
http://code.google.com/p/truecrack/
http://code.google.com/p/ua-tester/
http://code.google.com/p/udptunnel/
http://code.google.com/p/wepbuster/
http://code.google.com/p/wol-e/
http://codemonkey.org.uk/projects/trinity/
http://complemento.sourceforge.net
http://cppcheck.sourceforge.net/
http://cuckoosandbox.org/
http://cutycapt.sourceforge.net/
http://cve-search.github.io/cve-search
http://cymothoa.sourceforge.net/
http://darkside.com.au/snow/index.html
http://deployingradius.com/pscan/
http://derevenets.com/
http://dirb.sourceforge.net/
http://dnscurve.org/nsec3walker.html
http://dnsdumpster.com/
http://dnsmap.googlecode.com
http://domnit.org/stepic/doc/
http://dotdotpwn.blogspot.com
http://dradisframework.org/
http://dumpsterventures.com/jason/httpry/
http-enum
http://epinna.github.io/Weevely/
http://eternal-todo.com/category/bruteforce
http://eternal-todo.com/tools/malybuzz-network-fuzzer
http://eternal-todo.com/tools/peepdf-pdf-analysis-tool
http://etherape.sourceforge.net/
http://f00l.de/pcapfix/
http://foremost.sourceforge.net/
http://freecode.com/projects/chaosmap
http://freeworld.thc.org/thc-orakelcrackert11g/
http://freshmeat.net/projects/magicrescue/
http://freshmeat.net/projects/t-bear
http://ftp.linux.org.uk/pub/linux/Networking/netkit
http://gamelinux.github.io/prads/
http://ge.mine.nu/code/lbd
http://git.infradead.org/users/dedekind/bmap-tools.git
http://gnutls.org/
http://goo-dork.blogspot.com/
http://gpredict.oz9aec.net/
http://gqrx.dk/
http://gspoof.sourceforge.net/
http://guymager.sourceforge.net/
http://ha.ckers.org/slowloris/
http://halberd.superadditive.com/
http://hamster.erratasec.com/
http://handlers.sans.org/jclausing/
http://hashcat.net/oclhashcat/
http://hashcat.net/wiki/doku.php?id=maskprocessor
http://hashcat.net/wiki/doku.php?id=statsprocessor
http://hathawaymix.org/Software/TCPWatch
http://hex2bin.sourceforge.net/
http://hexinject.sourceforge.net
http://hookanalyser.blogspot.de/
http://hooked-on-mnemonics.blogspot.com.es/p/iheartxor.html
http://ikecrack.sourceforge.net/
http://inguma.sourceforge.net
http://intrace.googlecode.com
http://inundator.sourceforge.net/
http://ipaudit.sourceforge.net
http://ipmitool.sourceforge.net
http://isecpartners.github.io/Scout2/
http://johnny.ihackstuff.com/
http://johnny.ihackstuff.com/downloads/task,doc_details&Itemid=/gid,28/
http://jon.oberheide.org/0trace/
http://joomscan.sourceforge.net/
http://kernel.embedromix.ro/us/
http://labs.portcullis.co.uk/application/enum4linux/
http://labs.portcullis.co.uk/application/onesixtyone/
http://labs.portcullis.co.uk/application/sucrack
http://labs.portcullis.co.uk/tools/acccheck/
http://labs.portcullis.co.uk/tools/finding-all-the-vhosts/
http://laudanum.inguardians.com/#
http://lcamtuf.coredump.cx/
http://lcamtuf.coredump.cx/p0f3/
http://loop-aes.sourceforge.net/aespipe/
http://magikh0e.xyz/
http://malwareanalyser.blogspot.de/2011/10/malware-analyser.html
http://mark0.net/soft-trid-e.html
http://matahari.sourceforge.net/
http://mazzoo.de/
http://mboxgrep.sourceforge.net
http://memberwebs.com/stef/software/scrounge/
http://metacoretex.sourceforge.net/
http://michael.toren.net/code/tcptraceroute/
http://michenriksen.com/blog/gitrob-putting-the-open-source-in-osint/
http://midnightresearch.com/projects/depant/
http://midnightresearch.com/projects/wicrawl/
http://missidentify.sourceforge.net/
http://mkbrutusproject.github.io/MKBRUTUS/
http://ms-sys.sourceforge.net/
http://mulliner.org/bluetooth/hidattack.php
http://nardcore.org/ctunnel
http://netactview.sourceforge.net/index.html
http://netmap.sourceforge.net/
http://netsec.rs/70/tools.html
http://netsniff-ng.org/
http://net-square.com/httprint
http://nfc-tools.org/
http://nield.sourceforge.net/
http://nixgeneration.com/~jaime/netdiscover/
http://nmbscan.gbarbier.org/
http://nologin.org/
http://nologin.org/main.pl?action=codeList&
http://ntsecurity.nu/toolbox/browselist/
http://ntsecurity.nu/toolbox/dumpusers/
http://ntsecurity.nu/toolbox/etherchange/
http://ntsecurity.nu/toolbox/etherflood/
http://ntsecurity.nu/toolbox/gplist/
http://ntsecurity.nu/toolbox/grabitall/
http://ntsecurity.nu/toolbox/gsd/
http://ntsecurity.nu/toolbox/inzider/
http://ntsecurity.nu/toolbox/kerbcrack/
http://ntsecurity.nu/toolbox/klogger/
http://ntsecurity.nu/toolbox/memimager/
http://ntsecurity.nu/toolbox/periscope/
http://ntsecurity.nu/toolbox/pmdump/
http://ntsecurity.nu/toolbox/promisdetect/
http://ntsecurity.nu/toolbox/setowner/
http://ntsecurity.nu/toolbox/snitch/
http://ntsecurity.nu/toolbox/sqldict/
http://ntsecurity.nu/toolbox/winrelay/
http://ntsecurity.nu/toolbox/wpsweep/
http://ntsecurity.nu/toolbox/wups/
http://nullsecurity.net/tools/automation.html
http://nullsecurity.net/tools/backdoor.html
http://nullsecurity.net/tools/binary.html
http://nullsecurity.net/tools/cracker.html
http://nullsecurity.net/tools/dos.html
http://nullsecurity.net/tools/fuzzer.html
http://nullsecurity.net/tools/scanner
http://nullsecurity.net/tools/scanner.html
http://oldhome.schmorp.de/marc/fcrackzip.html
http://opensource.srlabs.de/projects/a51-decrypt
http://openwall.info/wiki/john/johnny
http://ophcrack.sourceforge.net
http://osandamalith.github.io/ChromeFreak/
http://osandamalith.github.io/SkypeFreak/
http://packages.qa.debian.org/n/netkit-rwho.html
http://packages.qa.debian.org/n/netmask.html
http://packages.qa.debian.org/u/urlview.html
http://packetfactory.openwall.net/projects/dnsa/index.html
http://packetfactory.openwall.net/projects/firewalk/
http://packeth.sourceforge.net/
http://packetstormsecurity.com/files/10080/ADMid-pkg.tgz.html
http://packetstormsecurity.com/files/102848/LFI-Scanner.0.html
http://packetstormsecurity.com/files/104314/QuickRecon.3.2.html
http://packetstormsecurity.com/files/104315/FHTTP-Attack-Tool.3.html
http://packetstormsecurity.com/files/104724/Post-Memory-Corruption-Memory-Analyzer.00.html
http://packetstormsecurity.com/files/104872/Short-Fuzzy-Rat-Scanner.html
http://packetstormsecurity.com/files/104927/infIP.1-Blacklist-Checker.html
http://packetstormsecurity.com/files/106450/FStealer-Filesystem-Mirroring-Tool.html
http://packetstormsecurity.com/files/106912/LFI-Fuzzploit-Tool.1.html
http://packetstormsecurity.com/files/107167/Traceroute-Like-HTTP-Scanner.html
http://packetstormsecurity.com/files/107301/sipArmyKnife_11232011.pl.txt
http://packetstormsecurity.com/files/109010/MagicHash-Collision-Testing-Tool.html
http://packetstormsecurity.com/files/110603/Vanguard-Pentesting-Scanner.html
http://packetstormsecurity.com/files/111510/darkBing-SQL-Scanner.1.html
http://packetstormsecurity.com/files/112855/Admin-Page-Finder-Script.html
http://packetstormsecurity.com/files/115852/Storm-Ringing-PABX-Test-Tool.html
http://packetstormsecurity.com/files/117403/Dark-D0rk3r.0.html
http://packetstormsecurity.com/files/117572/EAZY-Web-Scanner.html
http://packetstormsecurity.com/files/117773/Directory-Scanner-Tool.html
http://packetstormsecurity.com/files/118036/DNS-GeoIP.html
http://packetstormsecurity.com/files/118459/PDFResurrect-PDF-Analyzer.12.html
http://packetstormsecurity.com/files/118851/Control-Panel-Finder-Script.html
http://packetstormsecurity.com/files/119132/Mptcp-Packet-Manipulator.9.0.html
http://packetstormsecurity.com/files/119146/tcgetkey.1.html
http://packetstormsecurity.com/files/119153/Chrome-Web-Browser-Decoder.html
http://packetstormsecurity.com/files/119154/Google-Talk-Decoder.html
http://packetstormsecurity.com/files/119155/Skype-Hash-Dumper.0.html
http://packetstormsecurity.com/files/119156/Zykeys-Wireless-Tool.html
http://packetstormsecurity.com/files/119462/OCS-Cisco-Scanner.2.html
http://packetstormsecurity.com/files/119726/PLC-Device-Scanner.html
http://packetstormsecurity.com/files/119904/Encode-Shellcode.1b.html
http://packetstormsecurity.com/files/120634/Common-Name-Grabber-Script.html
http://packetstormsecurity.com/files/120802/MF-Sniffer-TN3270-Password-Grabber.html
http://packetstormsecurity.com/files/121390/Janissaries-Joomla-Fingerprint-Tool.html
http://packetstormsecurity.com/files/121543/NTDS-Hash-Decoder.b.html
http://packetstormsecurity.com/files/121590/Bing-LFI-RFI-Scanner.html
http://packetstormsecurity.com/files/122064/Web-Soul-Scanner.html
http://packetstormsecurity.com/files/124299/pytacle-alpha2.tar.gz
http://packetstormsecurity.com/files/124332/LFI-Exploiter.1.html
http://packetstormsecurity.com/files/125569/Netscan-Port-Scanner.0.html
http://packetstormsecurity.com/files/125972/Coloured-ELF-File-Dissector.html
http://packetstormsecurity.com/files/126068/hb_honeypot.pl.txt
http://packetstormsecurity.com/files/126621/Config-Push-snmpset-Utility.html
http://packetstormsecurity.com/files/127924/Melkor-ELF-Fuzzer.0.html
http://packetstormsecurity.com/files/127994/SSDP-Amplification-Scanner.html
http://packetstormsecurity.com/files/128065/Paranoic-Scan.7.html
http://packetstormsecurity.com/files/129871/LFI-Image-Helper.8.html
http://packetstormsecurity.com/files/25381/smbbf.9.1.tar.gz.html
http://packetstormsecurity.com/files/31102/smtpscan.5.tar.gz.html
http://packetstormsecurity.com/files/31864/redfang.2.5.tar.gz.html
http://packetstormsecurity.com/files/35120/ftp-spider.pl.html
http://packetstormsecurity.com/files/39627/notSPIKEfile.tgz.html
http://packetstormsecurity.com/files/50718/AtStakeTools.zip.html
http://packetstormsecurity.com/files/51127/airflood.1.tar.gz.html
http://packetstormsecurity.com/files/52514/katsnoop.tbz2.html
http://packetstormsecurity.com/files/54024/bluebugger.1.tar.gz.html
http://packetstormsecurity.com/files/55250/lotophagi.rar.html
http://packetstormsecurity.com/files/61864/proxycheck.pl.txt.html
http://packetstormsecurity.com/files/62047/gwcheck.c.html
http://packetstormsecurity.com/files/62126/joomlascan.2.py.txt.html
http://packetstormsecurity.com/files/62977/PExtractor_v0.18b_binary_and_src.rar.html
http://packetstormsecurity.com/files/64722/cms_few.py.txt.html
http://packetstormsecurity.com/files/66683/check_weak_dh_ssh.pl.bz2.html
http://packetstormsecurity.com/files/69778/proxyScan.3.tgz.html
http://packetstormsecurity.com/files/69802/synner.c.html
http://packetstormsecurity.com/files/76432/MP3nema-Forensic-Analysis-Tool.html
http://packetstormsecurity.com/files/79021/Remote-Web-Workplace-Attack-Tool.html
http://packetstormsecurity.com/files/81368/Hackers-Hideaway-ARP-Attack-Tool.html
http://packetstormsecurity.com/files/84468/Web-Application-Finger-Printer.01-26c3.html
http://packetstormsecurity.com/files/86076/NetReconn-Scanning-Tool-Collection.76.html
http://packetstormsecurity.com/files/87003/Known-Host-Cracker.2.html
http://packetstormsecurity.com/files/93062/Athena-SSL-Cipher-Scanner.html
http://packetstormsecurity.com/files/93518/Malware-Check-Tool.2.html
http://packetstormsecurity.com/files/96056/Simple-Local-File-Inclusion-Exploiter.0.html
http://packetstormsecurity.com/files/97353/Simple-LAN-Scanner.0.html
http://packetstormsecurity.com/files/97464/Andr01d-Magic-Dumper.1.html
http://packetstormsecurity.com/files/97465/Witchxtool-Port-LFI-SQL-Scanner-And-MD5-Bruteforcing-Tool.1.html
http://packetstormsecurity.com/files/98109/HTTPForge.02.01.html
http://packetstormsecurity.com/files/98359/yInjector-MySQL-Injection-Tool.html
http://packetstormsecurity.com/files/98546/yCrawler-Web-Crawling-Utility.html
http://packetstormsecurity.com/files/99823/Monocle-Host-Discovery-Tool.0.html
http://packetstormsecurity.com/search/?q=tsh
http://packetstormsecurity.org/cisco/enabler.c
http://packit.sourceforge.net/
http://pcapsipdump.sourceforge.net/
http://pdfcrack.sourceforge.net/
http://peachfuzzer.com/
http://pentester.fr/resources/tools/techno/VNC/VNC_bypauth/
http://pentestmonkey.net/tools/audit/unix-privesc-check
http://pentestmonkey.net/tools/user-enumeration/smtp-user-enum
http://pev.sourceforge.net/
http://phenoelit-us.org/irpas
http://phenoelit-us.org/vncrack
http://portspoof.org/
http://project-rainbowcrack.com/
http://projects.sentinelchicken.org/reglookup
http://pwhois.org/lft/
http://pyrasite.com/
http://pytbull.sourceforge.net/
http://qosient.com/argus/
http://rafale.org/~mattoufoutu/darkc0de.com/c0de/c/
http://rarcrack.sourceforge.net/
http://rcracki.sourceforge.net/
http://reedarvin.thearvins.com/
http://retirejs.github.io/retire.js/
http://rfidiot.org/
http://rgaucher.info/beta/grabber/
http://rkhunter.sourceforge.net/
https://0xacab.org/jvoisin/mat2
http://safecopy.sourceforge.net/
https://aflplus.plus/
http://samy.pl/chownat/
http://samy.pl/pwnat/
https://anonymous-proxy-servers.net/
https://aspj.aircrack-ng.org/
http://savannah.nongnu.org/projects/mobiusft
https://bechtsoudis.com/webacoo/
https://bitbucket.org/al14s/rawr/wiki/Home
https://bitbucket.org/alone/mutator/
https://bitbucket.org/camp0/aiengine/downloads/
https://bitbucket.org/decalage/balbuzard/
https://bitbucket.org/LaNMaSteR53/peepingtom
https://bitbucket.org/mihaila/bindead
https://bitbucket.org/mrabault/msvpwn
https://blog.didierstevens.com/2019/04/28/update-jpegdump-py-version-0-7/
https://blog.didierstevens.com/my-software/#AnalyzePESig
https://blog.didierstevens.com/my-software/#base64dump
https://blog.didierstevens.com/my-software/#disitool
https://blog.didierstevens.com/my-software/#emldump
https://blog.didierstevens.com/my-software/#NAFT
https://blog.didierstevens.com/my-software/#zipdump
https://brakemanscanner.org/
https://cheat.sh
https://cisofy.com/lynis/
https://codeberg.org/nanoory/gg_images
https://code.google.com/archive/p/findmyhash/
https://code.google.com/p/bletchley/
https://code.google.com/p/bunny-the-fuzzer/
https://code.google.com/p/fuzztalk
https://code.google.com/p/goofile/
https://code.google.com/p/hexorbase/
https://code.google.com/p/honggfuzz/
https://code.google.com/p/honssh/
https://code.google.com/p/host-extract/
https://code.google.com/p/inurlbr/
https://code.google.com/p/javasnoop/
https://code.google.com/p/libre-tools/
https://code.google.com/p/lusas/
https://code.google.com/p/malwasm/
https://code.google.com/p/modscan/
https://code.google.com/p/nfex/
https://code.google.com/p/nishang/
https://code.google.com/p/pyew/
https://code.google.com/p/replayproxy/
https://code.google.com/p/simple-ducky-payload-generator
https://code.google.com/p/spiderpig-pdffuzzer/
https://code.google.com/p/termineter/
https://code.google.com/p/ubitack/
https://code.google.com/p/urldigger/
https://code.google.com/p/webslayer/
https://code.kryo.se/iodine
https://codisec.com/veles/
https://cookiecadger.com/
https://cpan.metacpan.org/authors/id/G/GO/GOMOR/
httpscreenshot
https://crosstool-ng.github.io/
http://scw.us/iPhone/plutil/
https://defense.ballastsecurity.net/wiki/index.php/Zarp
https://developer.android.com/sdk/ndk/index.html
https://developer.android.com/studio/releases/sdk-tools.html
https://developer.shodan.io
https://dl.packetstormsecurity.net/trojans/Subseven.2.2.zip
https://dmde.com/download.html
https://dnsdiag.org/
https://dwheeler.com/flawfinder/
http://seclists.org/basics/2006/Sep/128
https://embeddedsw.net/OpenPuff_Steganography_Home.html
https://eraser.heidi.ie/download/
https://firmware.ardupilot.org/Tools/MissionPlanner/
https://freeradius.org/
https://ghidra-sre.org/
https://gitlab.com/akihe/radamsa
https://gitlab.com/dee-see/graphql-path-enum
https://gitlab.com/fredericopissarra/t50/tags
https://gitlab.com/GasparVardanyan/gcrypt
https://gitlab.com/initstring/evil-ssdp
https://gitlab.com/yawning/obfs4
https://gitorious.org/speedpwn/
https://gitweb.torproject.org/torsocks.git/
https://gnuradio.org
https://gobag.googlecode.com/svn-history/r2/trunk/ldap/ldapenum/
https://grax.info/
https://halcyon-ide.org/
https://hashcat.net/hashcat
https://i2pd.website/
https://ibotpeaches.github.io/Apktool/
http://silicone.homelinux.org/projects/netsed/
http://sipp.sourceforge.net/
https://ivre.rocks/
https://jetmore.org/john/code/swaks/
https://labs.f-secure.com/tools/rdp-cipher-checker/
https://labs.portcullis.co.uk/downloads/
https://launchpad.net/~wagungs/+archive/kali-linux/+build/4386635
https://lldb.llvm.org/
https://mat.boum.org/
https://mh-nexus.de/en/hxd/
https://mitmproxy.org
https://ngrok.com/
httpsniff
http://sniff.su/download.html
https://nmap.org/
https://nmap.org/ncrack/
https://noraj.github.io/ctf-party/
https://noraj.github.io/flask-session-cookie-manager/
https://noraj.github.io/haiti/
https://noraj.github.io/pass-station/
https://noraj.github.io/rabid/
https://noraj.github.io/tls-map/
https://noraj.github.io/vbsmin/
https://nullsecurity.net/tools/cracker.html
https://nullsecurity.net/tools/misc.html
https://nullsecurity.net/tools/scanner.html
http://sock-raw.org/projects.html
https://osmocom.org/projects/libosmocore/wiki/Libosmocore
https://osmocom.org/projects/simtrace2/wiki
http://sourceforge.net/apps/mediawiki/xprobe/index.php?title=Main_Page
http://sourceforge.net/projects/bfbtester/
http://sourceforge.net/projects/crunch-wordlist/
http://sourceforge.net/projects/darkjumper/
http://sourceforge.net/projects/dc3dd
http://sourceforge.net/projects/dnmap/
http://sourceforge.net/projects/dnswalk/
http://sourceforge.net/projects/domainanalyzer/
http://sourceforge.net/projects/enumiax/
http://sourceforge.net/projects/epicwebhoneypot/
http://sourceforge.net/projects/fakemail/
http://sourceforge.net/projects/hcraft/
http://sourceforge.net/projects/httpbog/
http://sourceforge.net/projects/httpdirscan/
http://sourceforge.net/projects/hyenae/
http://sourceforge.net/projects/ikecrack/
http://sourceforge.net/projects/jbrofuzz/
http://sourceforge.net/projects/jbrute/
http://sourceforge.net/projects/katanas/
http://sourceforge.net/projects/linux-decnet/files/latd/1.31/
http://sourceforge.net/projects/malmon/
http://sourceforge.net/projects/multimac/
http://sourceforge.net/projects/ophcrack
http://sourceforge.net/projects/ophcrack/files/samdump2/
http://sourceforge.net/projects/pdfbook/
http://sourceforge.net/projects/proxyp/
http://sourceforge.net/projects/sambascan2/
http://sourceforge.net/projects/themole/
http://sourceforge.net/projects/torshammer/
http://sourceforge.net/projects/tpcat/
http://sourceforge.net/projects/uniscan/
http://sourceforge.net/projects/webspa/
http://sourceforge.net/projects/winexe/
http://sourceforge.net/projects/zulu-wireless/
https://packages.debian.org/jessie/cadaver
https://packages.debian.org/sid/electric-fence
https://packages.debian.org/source/sid/net/netkit-rusers
https://packages.debian.org/unstable/main/sockstat
https://packetstormsecurity.com/files/10320/nb16_p04.zip.html
https://packetstormsecurity.com/files/11372/grabbb.0.7.tar.gz.html
https://packetstormsecurity.com/files/123534/IP-Phone-Scanning-Made-Easy.12.html
https://packetstormsecurity.com/files/132438/AESshell.7.html
https://packetstormsecurity.com/files/132449/Find-DNS-Scanner.html
https://packetstormsecurity.com/files/134230/Portmanteau-Unix-Driver-IOCTL-Security-Tool.html
https://packetstormsecurity.com/files/31879/rpctools.0.zip.html
https://packetstormsecurity.com/files/36781/cachedump.1.zip.html
https://packetstormsecurity.com/files/82897/PPScan-Portscanner.3.html
https://packetstormsecurity.com/files/83892/Maketh-Packet-Generator.2.0.html
https://packetstormsecurity.com/fuzzer/sshfuzz.txt
http://sparta.secforce.com/
http://sparty.secniche.org/
https://phpvulnhunter.codeplex.com/
https://pogostick.net/~pnh/ntpasswd/
https://portswigger.net/burp/releases
https://pypi.org/project/angr/#files
https://pypi.org/project/angr-management/#files
https://pypi.org/project/bandicoot/#files
https://pypi.org/project/canari/#files
https://pypi.org/project/Conpot/
https://pypi.org/project/cymruwhois/#files
https://pypi.org/project/frida/#files
https://pypi.org/project/frida-tools/#files
https://pypi.org/project/google-streetview/#files
https://pypi.org/project/ldapdomaindump/#files
https://pypi.org/project/minidump/#files
https://pypi.org/project/njsscan/#files
https://pypi.org/project/objection/#files
https://pypi.org/project/oletools/
https://pypi.org/project/pcodedmp/#files
https://pypi.org/project/regrippy/#files
https://pypi.org/project/ropgadget/#files
https://pypi.org/project/search-engine-parser/#files
https://pypi.org/project/shodan/#files
https://pypi.org/project/social-analyzer/
https://pypi.org/project/ssh-mitm/#files
https://pypi.org/project/uncompyle6/#files
https://pypi.org/project/usnparser/#files
https://pypi.org/project/webtech/#files
https://pypi.org/project/witnessme/#files
https://pypi.org/project/yara-python/#files
https://pypi.python.org/pypi/darts.util.lru
https://pypi.python.org/pypi/tcpextract/
http://sqid.rubyforge.org/
http://sqlmap.org
http://sqlninja.sourceforge.net/
http://sqlsus.sourceforge.net/
https://quadrantsec.com/sagan_log_analysis_engine/
https://radare.org
https://raw.githubusercontent.com/galkan/tools/master/mail-crawl/
https://repo.or.cz/splint-patched.git
https://retdec.com/
https://rubygems.org/gems/DrupalScan/
https://rubygems.org/gems/idb
https://scrapy.org
https://search.cpan.org/perldoc?exiftool
https://shodan.readthedocs.io/
https://sites.google.com/site/forensicnote/ntfs-log-tracker
https://sites.google.com/site/simplepacketsender/
https://software.intel.com/en-us/articles/pin-a-binary-instrumentation-tool-downloads
https://sourceforge.net/projects/air-imager/
https://sourceforge.net/projects/atftp/
https://sourceforge.net/projects/chromensics/
https://sourceforge.net/projects/innounp/files/innounp/
https://sourceforge.net/projects/jnetmap/files/jNetMap%200.5.5-RC2/
https://sourceforge.net/projects/ldapscripts
https://sourceforge.net/projects/unhide/
https://sourceware.org/elfutils/
https://spnsupport.trendmicro.com/
https://ssdeep-project.github.io/ssdeep/
https://strace.io/
https://suricata.io/download/
https://tcpreplay.appneta.com
http://s-tech.elsat.net.pl/braa/
http://steghide.sourceforge.net/
https://tinyproxy.github.io/
https://tools.netsa.cert.org/silk/download.html
https://tools.netsa.cert.org/yaf/download.html
http://stridsmanit.wordpress.com/2012/12/02/brute-forcing-passwords-with-hostbox-ssh-1-1/
https://trifinite.org/trifinite_stuff_blueprinting.html
https://unix4lyfe.org/darkstat/
https://varaneckas.com/jad
https://w1.fi/hostapd/
https://web2ldap.de/
https://wpscan.org
https://www.aircrack-ng.org
https://www.arachni-scanner.com
https://www.bitwizard.nl/mtr/
https://www.blackarch.org/
https://www.capstone-engine.org/index.html
https://www.cert.at/en/downloads/software/software-densityscout
https://www.cgsecurity.org/index.html?testdisk.html
https://www.computec.ch/projekte/vulscan/
https://www.cs.uit.no/~daniels/PingTunnel
https://www.daemonology.net/bsdiff/
https://www.digitalloft.org/init/plugin_wiki/page/codetective
https://www.dionach.com/blog/cmsmap-%E2%80%93-a-simple-cms-vulnerability-scanner
https://www.ee.oulu.fi/research/ouspg/PROTOS_Test-Suite_c07-sip
https://www.ee.oulu.fi/research/ouspg/tcpcontrol-fuzzer
https://www.encripto.no/nb/downloads/tools/
https://www.exploit-db.com/
https://www.five-ten-sg.com/libpst/
https://www.fping.org/
https://www.gaijin.at/dlregreport.php
https://www.gaijin.at/en/dlregview.php
https://www.gaijin.at/en/dlstreamfind.php
https://www.gnu.org/software/ddrescue/ddrescue.html
https://www.gnu.org/software/freeipmi
https://www.gnu.org/software/gdb/
https://www.gnu.org/software/macchanger
https://www.gsocket.io/
https://www.hopperapp.com/download.html?
https://www.httrack.com/
https://www.isc.org/software/bind/
https://www.isecpartners.com/vnak.html
https://www.jetbrains.com/decompiler/
https://www.joedog.org/siege-home/
https://www.kismetwireless.net/
https://www.ltrace.org/
https://www.mavetju.org/unix/dnstracer.php
https://www.maxmind.com/app/c
https://www.metasploit.com/
https://www.monkey.org/~dugsong/dsniff/
https://www.muppetlabs.com/~breadbox/software/elfkickers.html
https://www.mzrst.com/
https://www.nirsoft.net
https://www.nirsoft.net/utils/wifi_channel_monitor.html
https://www.nzyme.org/download
https://www.open-iscsi.com/
https://www.open-scap.org/
https://www.open-scap.org/security-policies/scap-security-guide/
https://www.open-scap.org/tools/scap-workbench/
https://www.openwall.com/john
https://www.owasp.org/index.php/Category:OWASP_WSFuzzer_Project
https://www.owasp.org/index.php/O-Saft
https://www.owasp.org/index.php/OWASP_OWTF
https://www.owasp.org/index.php/Projects/OWASP_iOSForensic
https://www.owasp.org/index.php/ZAP
https://www.pingcastle.com
https://www.pinguin.lu/fred
https://www.pnfsoftware.com/jeb/android
https://www.pnfsoftware.com/jeb/arm
https://www.pnfsoftware.com/jeb/intel
https://www.pnfsoftware.com/jeb/mips
https://www.pnfsoftware.com/jeb/#wasm
https://www.rutschle.net/tech/sslh/README.html
https://www.saleae.com/downloads
https://www.salecker.org/software/kismon.html
https://www.shadowexplorer.com/downloads.html
https://www.shellcheck.net
https://www.shellterproject.com/download/
https://www.sleuthkit.org/sleuthkit
https://www.soapui.org/downloads/soapui/source-forge.html
https://www.stig-viewer.com
https://www.stunnel.org/
https://www.sysdig.com/
https://www.tcpdump.org/
https://www.thc.org/releases.php
https://www.thc.org/thc-smartbrute/
https://www.thexero.co.uk/tools/http-enum/
https://www.tinc-vpn.org/
https://www.titania-security.com/
https://www.torproject.org/download/tor/
https://www.torproject.org/projects/torbrowser.html.en
https://www.trustedsec.com/downloads/artillery/
https://www.trustedsec.com/september/owning-dell-drac-awesome-hack/
https://www.unix-ag.uni-kl.de/~conrad/krypto/pkcrack/download1.html
https://www.vanheusden.com/httping/
https://www.veracrypt.fr/
https://www.wireshark.org/
https://www.x-ways.net/winhex/
https://xsser.03c8.net/
https://yatebts.com/
http://sysinternals.com/
https://zmap.io/
http://taof.sf.net/
http://tcpick.sourceforge.net/
http://tcptrace.org/
http://tcpxtract.sourceforge.net
http://tgcd.sourceforge.net/
http://theinvisiblethings.blogspot.com
http://thesprawl.org/projects/dnschef/
http://thesprawl.org/projects/latest/
http://thesprawl.org/projects/pack/
http://thesprawl.org/projects/search-engine-assessment-tool/
http://thesprawl.org/projects/syn-flooder/
http://thomer.com/icmptx/
http://thre.at/kalibrate/
http://traceroute.sourceforge.net/
http://trifinite.org/trifinite_stuff_carwhisperer.html
http://uberwall.org/bin/download/42/UW_offish.1.tar.gz
http://uberwall.org/bin/download/43/UWzone.tgz
http://uberwall.org/bin/download/44/UWudpscan.tar.gz
http://uberwall.org/bin/download/45/UWloveimap.tgz
http://ucsniff.sourceforge.net/ace.html
http://ucsniff.sourceforge.net/videosnarf.html
http://udis86.sourceforge.net/
http://uncaptcha.cs.umd.edu/
http://utidylib.berlios.de
http://valgrind.org/
http://vinetto.sourceforge.net
http://viproy.com/
http://visi.kenshoto.com/
http://vladz.devzero.fr/fsnoop.php
http://vladz.devzero.fr/pwd-hash.php
http://voiper.sourceforge.net/
http://voiphopper.sourceforge.net/
http://vsecurity.com/resources/tool
http://vu1tur.eu.org/tools/
http://wayreth.eu.org/old_page/
http://wcoserver.googlecode.com/files/
http://websecurity.com.ua/davoset/
http://wi-feye.za1d.com/download.php
http://wiki.skullsecurity.org/Nbtool
http://ws-attacker.sourceforge.net/
http://www.0x90.se/
http://www.0xrage.com/
http://www2.packetstormsecurity.org/cgi-bin/search/search.cgi?searchvalue=sbd
http://www.afflib.org
http://www.aldeid.com/wiki/Bed
http://www.alighieri.org/project.html
http://www.angio.net/security/
http://www.angusj.com/resourcehacker/
http://www.arhont.com/en/category/resources/tools-utilities/
http://www.arpalert.org/
http://www.arpoison.net
http://www.backerstreet.com/rec/rec.htm
http://www.beeswarm-ids.org/
http://www.beholderwireless.org/
http://www.beneaththewaves.net/Software/On_The_Outside_Reaching_In.html
http://www.benf.org/other/cfr/
http://www.betaversion.net/btdsd/download/
http://www.bindshell.net/tools/rfidtool.html
http://www.bindshell.net/tools/sslcat
http://www.blackalchemy.to/project/fakeap/
http://www.blackangels.it
http://www.blackhatlibrary.net/Dripper
http://www.blackhatlibrary.net/Halcyon
http://www.blackhatlibrary.net/Jynx2
http://www.blackhatlibrary.net/Kolkata
http://www.blackhatlibrary.net/Lfi_autopwn.pl
http://www.blackhatlibrary.net/Shellcodecs
http://www.boutell.com/rinetd
http://www.breaknenter.org/projects/inception/
http://www.c0decafe.de/
http://www.caida.org/tools/measurement/scamper/
http://www.cgsecurity.org/wiki/CmosPwd
http://www.chkrootkit.org/
http://www.cirt.dk
http://www.cirt.dk/
http://www.computec.ch/projekte/httprecon/?s=download
http://www.cpan.org/authors/id/R/RA/RAYNERLUC
http://www.cqure.net/wp/dbpwaudit/
http://www.cqure.net/wp/getsids/
http://www.cqure.net/wp/lodowep/
http://www.cqure.net/wp/mbenum/
http://www.cqure.net/wp/mssqlscan/
http://www.cqure.net/wp/oscanner/
http://www.cqure.net/wp/sqlpat/
http://www.cqure.net/wp/test/
http://www.cqure.net/wp/tools/database/sidguesser/
http://www.cqure.net/wp/upnpscan/
http://www.crypticbit.com/zen/products/iphoneanalyzer
http://www.cryptohaze.com/multiforcer.php
http://www.cultdeadcow.com/tools/bo.html
http://www.cultdeadcow.com/tools/rasenum.html
http://www.curesec.com/
http://www.curesec.com/en/publications/tools.html
http://www.cycom.se/dl/rrs
http://www.darkc0de.com/others/goog-mail.py
http://www.darknet.org.uk/2015/01/bluescan-bluetooth-device-scanner/
http://www.darkoperator.com/tools-and-scripts/
http://www.delirandom.net/sniffjoke/
http://www.dest-unreach.org/socat/
http://www.digifail.com/software/bluelog.shtml
http://www.digininja.org/projects/cewl.php
http://www.digininja.org/projects/pipal.php
http://www.digininja.org/projects/twofi.php
http://www.digininja.org/projects/wifi_honey.php
http://www.digitalforensicssolutions.com/Scalpel/
http://www.digit-labs.org/files/tools/synscan/
http://www.doxpara.com/paketto
http://www.dumpzilla.org/
http://www.edge-security.com/edge-soft.php
http://www.edge-security.com/metagoofil.php
http://www.edge-security.com/pblind.php
http://www.edge-security.com/subdomainer.php
http://www.edge-security.com/theHarvester.php
http://www.emanuelegentili.eu/
http://www.encripto.no/tools/
http://www.enderunix.org/voipong/
http://www.enye-sec.org/programas.html
http://www.ernw.de/download/ikeprobe.zip
http://www.ex-parrot.com/~chris/driftnet/
http://www.exploit-db.com
http://www.fantaghost.com/fgscanner
http://www.faradaysec.com/
http://www.fastandeasyhacking.com/
http://www.filip.waeytens.easynet.be/
http://www.flamerobin.org/
http://www.foofus.net/jmk/medusa/medusa.html
http://www.foundstone.com/us/resources/proddesc/fport.htm
http://www.foundstone.com/us/resources/proddesc/superscan.htm
http://www.fourmilab.ch/random
http://www.freshports.org/security/xspy/
http://www.fulgursecurity.com/en/content/fs-nyarl
http://www.fuzzing.org/
http://www.gdssecurity.com/l/t/d.php?k=GwtEnum
http://www.gdssecurity.com/l/t.php
http://www.giskismet.org
http://www.gnu.org/software/cflow/
http://www.gremwell.com
http://www.guay-leroux.com/projects.html
http://www.hackersh.org/
http://www.hackfromacave.com/projects/blueranger.html
http://www.hackfromacave.com/projects/spooftooph.html
http://www.hackingciscoexposed.com/?link=tools
http://www.hackingexposedcisco.com/
http://www.hackingexposedvoip.com/
http://www.hackingvoip.com/sec_tools.html
http://www.hellfiresecurity.com/tools.htm
http://www.highhacksociety.com/
http://www.homelab.it/index.php/2014/11/03/wordpress-brute-force-multithreading/
http://www.hoobie.net/brutus/
http://www.hoobie.net/mingsweeper/
http://www.hoobie.net/pafish/
http://www.hoobie.net/wce/
http://www.hping.org
http://www.hsc.fr/ressources/outils/dislocker
http://www.hsc.fr/ressources/outils/dns2tcp/index.html.en
http://www.hsc.fr/ressources/outils/idswakeup/index.html.en
http://www.hsc.fr/ressources/outils/passe-partout/index.html.en
http://www.immunitysec.com/resources-freesoftware.shtml
http://www.inetsim.org/
http://www.infobyte.com.ar/
http://www.inversepath.com/ftester.html
http://www.ipbackupanalyzer.com/
http://www.irongeek.com/downloads/grepforrfi.txt
http://www.jammed.com/~jwa/hacks/security/tnscmd/
http://www.jeffbryner.com/code/pdgmail
http://www.johannes-bauer.com/linux/luksipc
http://www.jonesdykstra.com/
http://www.justinclarke.com/archives/2006/03/sqlbrute.html
http://www.kismetwireless.net/spectools/
http://www.krakowlabs.com/dev.html
http://www.leidecker.info/projects/phrasendrescher/
http://www.libpng.org/pub/png/apps/pngcheck.html
http://www.loicp.eu/ipdecap#dependances
http://www.lysator.liu.se/~pen/pnscan/
http://www.mathstat.dal.ca/~selinger/md5collision/
http://www.mcafee.com/uk/downloads/free-tools/snscan.aspx
http://www.mcgrewsecurity.com/tools/nbnspoof/
http://www.mh-sec.de/downloads.html.en
http://www.mibble.org/
http://www.mkit.com.ar/labs/htexploit/
http://www.mlsec.org/malheur/
http://www.monkey.org/~provos/scanssh/
http://www.morningstarsecurity.com/research
http://www.morningstarsecurity.com/research/bing-ip2hosts
http://www.morningstarsecurity.com/research/gggooglescan
http://www.morningstarsecurity.com/research/urlcrazy
http://www.morningstarsecurity.com/research/username-anarchy
http://www.morningstarsecurity.com/research/whatweb
http://www.mor-pah.net/
http://www.morxploit.com/
http://www.morxploit.com/morxbrute/
http://www.morxploit.com/morxcrack/
http://www.morxploit.com/morxtool
http://www.morxploit.com/tools/
http://www.netpatch.ru/dhcdrop.html
http://www.netresec.com/
http://www.net-square.com/httprint.html
http://www.netstumbler.com/downloads/
http://www.netzob.org/
http://www.nongnu.org/tiger/
http://www.noorg.org/ifchk/
http://www.nothink.org/perl/snmpcheck/
http://www.nothink.org/perl/snmpscan/index.php
http://www.novell.com/
http://www.nowrap.de/flare.html
http://www.nowrap.de/flasm.html
http://www.nruns.com/_en/security_tools_btcrack.php
http://www.nsoq.org/
http://www.nta-monitor.com/tools/ike-scan/
http://www.nth-dimension.org.uk/downloads.php?id=34
http://www.ntsecurity.nu/toolbox/pstoreview/
http://www.ntsecurity.nu/toolbox/winfo/
http://www.nullsecurity.net/
http://www.nullsecurity.net/tools/automation.html
http://www.nullsecurity.net/tools/backdoor.html
http://www.nullsecurity.net/tools/scanner.html
http://www.oldskoolphreak.com/tfiles/ghettotooth.txt
http://www.ollydbg.de
http://www.omriher.com/2015/01/captipper-malicious-http-traffic.html
http://www.open-labs.org/
http://www.open-mesh.net/
http://www.open-mesh.org/
http://www.openwall.com/php_mt_seed/
http://www.outguess.org/
http://www.owasp.org/index.php/Category:OWASP_CSRFTester_Project
http://www.owasp.org/index.php/Category:OWASP_DirBuster_Project
http://www.owasp.org/index.php/Category:OWASP_WebScarab_Project
http://www.p1sec.com/
http://www.packet-o-matic.org/
http://www.packetstormsecurity.org/
http://www.paramecium.org/~leendert/
http://www.parosproxy.org
http://www.paterva.com/web5
http://www.paterva.com/web6/products/casefile.php
http://www.pentbox.net
http://www.pentest.co.uk
http://www.perihel.at/sec/mz/
http://www.phenoelit.org/fr/tools.html
http://www.porcupine.org/forensics/tct.html
http://www.powerfuzzer.com
http://www.projectiwear.org/~plasmahh/software.html
http://www.ps-auxw.de/
http://www.pyinstaller.org/downloads.html
http://www.question-defense.com/2013/01/11/ocs-version-2-release-ocs-cisco-router-default-password-scanner
http://www.randomstorm.com/rsmangler-security-tool.php
http://www.rdesktop.org/
http://www.redlevel.org
http://www.remote-exploit.org/codes_sipcrack.html
http://www.remote-exploit.org/?page_id=418
http://www.rfc1149.net/devel/recoverjpeg
http://www.rfdump.org
http://www.roe.ch/FakeIKEd
http://www.rtfm.com/ssldump/
http://www.sable.mcgill.ca/soot
http://www.sakis3g.org/
http://www.salvatorefresta.net/tools/
http://www.scovetta.com/yasca.html
http://www.scrt.ch/en/attack/downloads/mini-mysqlat0r
http://www.scrt.ch/en/attack/downloads/webshag
http://www.scrypt.net
http://www.secdev.org/projects/scapy/
http://www.secfu.net/tools-scripts/
http://www.secpoint.com/netbios-share-scanner.html
http://www.secuobs.com/news/15022006-bss_0_8.shtml
http://www.securepla.net/
http://www.security-projects.com/?Anti_Ransom___Download
http://www.security-projects.com/?Brute12
http://www.security-projects.com/?RadioGraPhy
http://www.security-projects.com/?SigSpotter
http://www.selectiveintellect.com/hotpatch.html
http://www.sentex.net/~mwandel/jhead/
http://www.shell-storm.org/project/ROPgadget
http://www.si6networks.com/tools/ipv6toolkit/
http://www.skbuff.net/iputils/
http://www.sleuthkit.org/mac-robber/download.php
http://www.sniperx.net/
http://www.snort.org/
http://www.sqlpowerinjector.com/download.htm
http://www.sqlsecurity.com/downloads
http://www.sump.org/projects/httpsniff/
http://www.sven.de/xsss/
http://www.swftools.org/
http://www.synacklabs.net/projects/lsrtunnel/
http://www.systemtools.com/cgi-bin/download.pl?DumpAcl
http://www.tarasco.org/security/handle/index.html
http://www.tarasco.org/security/pwdump_7/index.html
http://www.tarasco.org/security/smbrelay/
http://www.tarsnap.com/spiped.html
http://www.thc.org
http://www.thc.org/
http://www.thc.org/thc-ssl-dos/
http://www.thedrahos.net/jcrack/
http://www.thesprawl.org/projects/tor-autocircuit/
http://www.thoughtcrime.org/software/sslsniff/
http://www.toolswatch.org/dpe/
http://www.toolswatch.org/vfeed
http://www.truecrypt.org/
http://www.ufo-wardriving.com/
http://www.umitproject.org/
http://www.unicornscan.org/
http://www.vaibhavpandey.com/apkstudio/
http://www.vicenteaguileradiaz.com/tools/
http://www.virustotal.com
http://www.vnsecurity.net/2010/08/ropeme-rop-exploit-made-easy/
http://www.wendzel.de/dr.org/files/Projects/vstt/
http://www.whoppix.net/
http://www.willhackforsushi.com/Asleap.html
http://www.willhackforsushi.com/?page_id=67
http://www.willhackforsushi.com/?page_id=79
http://www.williballenthin.com/forensics/mft/indxparse/
http://www.winstonsmith.info/julia/elettra/
http://www.xfocus.org/
http://www.xplico.org/
http://www.yersinia.net/
http://www.zynamics.com/bindiff.html
http://wxhexeditor.sourceforge.net/
http://xenion.antifork.org/rtpbreak/
http://yasat.sourceforge.net/
http://zerowine.sf.net/
http://zssh.sourceforge.net/
