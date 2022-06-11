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

ffmpeg -loop 1 -i video-background.png -i Necro-killed-by-sorc.mp3 -shortest video.mp4


#PUT IN BASH HISTORY TO take list of URL links and wget them into 001.txt .. 999.txt
#n=0; while read myLine; do (( n++ )); wget_out_file=$(printf '%03d.txt' $n); echo "$wget_out_file $myLine" >> wget-MAP.txt; echo "wget -O $wget_out_file '$myLine'" >> wget-TODO.sh; done <<< "$(cat novaks-LINKs.txt)"


export TZ=America/Los_Angeles
export WINEPREFIX=/media/user/DEB_STUFF/dot-wine
wow='/media/user/DEB_STUFF/dot-wine/drive_c/Program Files (x86)/Battle.net'
www='/home/user/Documents/localhost-httpd/www'
export todo='/home/user/Documents/TODO/todo.txt'
alias ..='cd ..'
alias vi='/usr/bin/vim'
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
  sudo iw dev wlp1s0 scan | egrep 'SSID: [^ ]+' | awk '-F: ' '{print $2}' | sort 
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
#!/bin/bash

[ -f ~/README.txt ] && exit 2

rm -v README.txt
echo 'cat *.txt > README.txt'
cat *.txt > ~/README.txt
mv -v ~/README.txt ./
#!/bin/bash


#PUT ME IN /usr/bin
perl -ne 'if(/[^[:ascii:]]/){print "found some non ascii";exit 1;}' "$1" && exit 0
#!/bin/bash


#PUT ME IN /usr/bin/
perl -pi~ -e 's/[^[:ascii:]]+//g' "$1"
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


