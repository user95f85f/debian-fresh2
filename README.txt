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
autosourceslist  - Generate an automated sources.list file that selects the fastest mirror to `sudo apt install` from
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
`apt` depends download list moo show showsrc source 
`apt-mark` showhold showmanual
`dpkg -L` content
`dpkg -l` listallpkgs
`# I have a bash "one-liner" that can do this, request as necessary` recommends
`dpkg-query -f "\${Version}" -W` version
`dpkg -S` contains
`sudo apt` autoremove --reinstall install autoclean build-dep clean dselect-upgrade edit-sources full-upgrade purge remove update upgrade
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
`sudo apt install netselect-apt && sudo netselect-apt` autosourceslist
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
BINARY FILE SERVER WEBSITES TO UPLOAD TO EASILY WITH CLI/SHELL/CURL/WGET

  https://transfer.sh #your file is there for 14 days. TODO: filename restrictions? filesize restrictions?
  #using this service is a 0.3%good idea

  #EG. with:
  #curl --progress-bar --upload-file
  #or --form 'file=@filename.tar.gz' --form 'file2=@filename2.tar.gz'
  #or --form 'file=@"filename3.tar.gz";filename="whatever.dat"'
  #or wget --post-file=filename.tar.gz

OPEN CONNECTIONS TWO WAYS OF DOING IT BOTH BENEFICIAL

  sudo netstat -anpl --inet   #IP addresses and your IP address (3%sure)
  sudo netstat -tapdu         #hostnames of the foreign places

EXTERNAL IP ADDRESS RESEARCH

  #(ie. modem/router/ISP-hardware IP)
  curl --silent http://smart-ip.net/myip
  #TODO: try google searching 'my ip' 13%chance you'll get it

DEBIAN SOURCE CODE COMPILATION FUN

  apt --simulate build-dep vim
  sudo apt build-dep vim
  mkdir -pv ~/Downloads/vim-src && cd ~/Downloads/vim-src && {
    apt source --build vim #have fun
  }
  #<OPTIONAL>
  #TODO: figure out a way to undo the build-dep package installation (by purging all of those packages or whatever)
  #</OPTIONAL>

VERIZON AIRSPEED JETPACK

  Verizon Airspeed Jetpack $60 (Prepaid version) (4G) (2.4Ghz/5Ghz) (requires no credit check)
    (aka. Prepaid - Airspeed 4G Mobile Hotspot)
    (ie. Airspeed mobile [wifi] hotspot)
    (battery powered and/or AC + AC battery charged)
    (battery lasts about 4 hours)
    (the Jetpack is about 2x the size of a wallet)
    (comes with battery, SIM-card-pre-installed, and USB-based AC charger)
  $30-$60 25GB of data from Verizon for 1 month
    When you stop paying the service transparently gets dropped.
    Can pay with 100%cash forever at the Verizon Store.
    Don't need to pay anything in advance (unlike AT&T for some of their data-only pre-paid plans)
      (except of course the Jetpack + the 1 month of pre-paid data-only "plan" [ie. "Prepaid Plan" [aka?? "Prepaid Internet"]])
  There is a $15 fee for "Set Up And Go Service"
  Verizon Store asks for this information as a new Verizon customer:
  $name, $phone, $email, $physical_address (ie. mailing or home 90%sure), $4Digit_PIN_number

  Extra information:
    - at least with 2.4GHz the Wi-Fi of the Verizon Airspeed Jetpack
      it can do: 802.11b 802.11g or 802.11n
      (or it just chooses something automatically)
    - it does IPv6 + IPv4
    - it has a default IPv4 DNS and IPv6 DNS
    - you can use the charger's head (ie. the power plug-in part) and take
      it off to have a USB 2.0 [?] connection to plug-in to a
      laptop/computer/PC to connect to the Internet without
      wifi/802.*/2.4GHz/5GHz/SSID/password
        (to enable this feature login to the Jetpack's website & go
        System->Power Saving->USB Mode
        to activate this setting (because the default is to use the plug-in
        as a Charger, not an Internet providing "service")
  
  Setup: insert battery. The thing 90%sure will just auto-turn-on...I guess?
         Otherwise hold power button on the side for 2 seconds
         wait a little while for activation. 

  http://192.168.1.1     OR
  http://my.mobilehotspot
  {
    default username = admin
    default password = (same as the wifi password)
    default SSID     = Verizon-RC400L-*
  }

  MODEL:  VZW-ASMHS01PP
  MODEL2:     ASMHS01PP
  MODEL3:     ASMHS01  
  SKU:          6482753
  IMEI:   ******************************************** (hehe)
  JETPACK HARDWARE-SOFTWARE VERSION:  airspeed_0.01.51
  JETPACK HARDWARE-SOFTWARE VERSION2: airspeed_0.01.52
                                      (after Software Update released on June 2nd, 2022 38%sure)
  
  goto 2.4GHz WiFi info screen:
    extract(password) #it's located next to the lock icon

  change_credentials(): #eg. username/password
    Settings -> Management page
             -> System Admin
    (then log out)

  My Verizon Login: (TODO: requires testing)
    https://www.verizonwireless.com/b2c/myverizonlp/
    (some cool stuff: Account -> My Devices (see account plan)
    https://vzw.com/prepaid

  turn_off_data_on_jetpack():
    Advanced Settings -> Network Settings

  power_off_jetpack():
    *hold 3 seconds power button*

  power_on_jetpack():
    *hold power button until display turns on*

  reset_jetpack_device_physically(): #resets SSID-name/wifi-username/wifi-password
                                     #TODO: what else does the reset reset??? the software updates too?
    *remove battery cover*
    with device still powered on:
      *paper-clip reset hole ~6 seconds*

  if(jetpack_responsive == FALSE):
    reboot() #*hold power button for 8 seconds*
    restore_factory_settings() #90%sure this is the reset_jetpack_device_physically()

  wifi-on():
    *hold power for 3 seconds* #I'm 90%sure this is 0%right.....


VERIZON USB/DONGLE WIRELESS INTERNET (OBSOLETE/DEPRACATED HARDWARE)

  Verizon 4G LTE USB Modem UML295

  Needs a SIM card that's activated.
  access via:
    http://192.168.32.2:4330
    http://mbb.vzw.com

  Let the Verizon Store setup a valid SIM card & put it in the USB modem.  
  
  

DEBIAN NOTES

  echo Debian has 51,000 amd64/pre-compiled packages

DEBIAN TTY/VIRTUAL-CONSOLE THINGS TO TRY IN LIVEOS/LIVEISO/LIVEUSB

  sudo $EDITOR /etc/systemd/logind.conf
  #set NAutoVTs=8
  sudo systemctl restart logind #or find out how to do a daemon-reload `man systemctl`

GNU/LINUX LOG FILES LOOKING AT BULLS***

  #look at the log file of when your computer first boots:
  #all of those kernel messages like:
  #your BIOS version (eg. BIOS 1.11.0 12/04/2019),
  #your PC's model number (eg. Dell Inc. Inspiron 5566/0NWJDC),
  #your boot image (eg. /boot/vmlinuz-5.10.0-13-amd64),
  #your boot image's options (like toram and pcie_aspm=off)
  #the company that created your EFI in BIOS (eg. American Megatrends)
  #your processor's speed: (eg. 2900 MHz)
  #your kernel build date (eg. January 10th, 2021)
  man journalctl | grep -- --boot

  #know when you last booted
  uptime --since; uptime --pretty; sudo journalctl --list-boots | cat -

  #watch for new journalctl messages:
  sudo journalctl -n30 --follow

  #make/free more free? space on you DISK/SWAP:
  sudo journalctl --disk-usage | tr ' ' '\n' | egrep '^[0-9]'
  #224.0M  (for example)
  sudo journalctl --vacuum-size=100M

GNU/LINUX USER/GROUP MANAGMENT BULLS***

  #change a user's username/login name
  man usermod | grep -i login

  #add/append valid users to a group
  man usermod | grep -i -- '-g|append'

  #lock a user account
  man usermod | grep -i lock

  #move home directory for a user
  man usermod | grep -i home

  #add a new group to the system
  man groupadd | grep groupadd

DIFFERENCE PWD and CWD
  
  CWD (ie. CURRENT WORKING DIRECTORY) can change (ie. is dynamic) in/within the process that is ran.
  PWD (ie. PRINT WORK DIRECTORY) is constant.

  "Changes" Working Directory: CWD
  "Permanent" Working Directory: PWD

MOUNT A PARTITION WITHIN A FILESYSTEM BUT YOU CAN'T REMEMBER IF IT'S EXT4 or FAT32/VFAT???

  mount -t auto /dev/sdb1 /media/whatever11111111111111111111111

CLEAR/RESET/EMPTY A FILE

  #this is like:    echo -n '' > ~/hello.txt
  :>~/hello.txt

GO THROUGH INSIDE OF ONE-LEVEL SUBDIR AND DO SOMETHING

  for d in */; do ( if cd -- "$d"; then git status >/dev/null 2>&1; if [ $? = 128 ]; then printf '%s\n' "$d isn't a valid git repo"; fi; fi ); done

SED DELETE LINES THAT DO NOT MATCH

  echo 123 | sed '/123/!d'
  echo 123 | grep -v '123'

SHOW ALL IMAGES WITHIN THE CURRENTLY DIRECTORY SUPER FAST
  
  #in XFCE close the windows successfully with Alt+F4
  for i in *.jpg; do tkjpeg $i & done

CONVERT A STRING TO MAYBE SUCCESSFUL FILENAME. AUTO-BACKSLASH BULLS*** CHARACTERS.

  #the -l chomp(<STDIN>) and auto-newline for the print
  echo -n 'NORMAL_   BACKSLASHED :-!@#$%^&*()~<>?:"{}[];,./+' | perl -l -ne 'print qq(\Q$_\E)'

STRIPPING JPEG (PNG?????) EXIF DATA INFORMATION

  man exiftool #search 'all'
  man jpegoptim

CURL _VS_ WGET EQUIVALENCY COMPARISON

  #curl: --max-time SECONDS IN DECIMAL BEFORE BAIL.
  #wget: --timeout

  #curl: --disable    IGNORE all of the curl.conf files everywhere...

  #curl: --silent     DISABLE progress bar BS, informational sh**, and error sh**
  #curl: --show-error SHOW ERROR message iff --silent is given AND there is a 404/403/whatever.  (TODO: STDERR?)
  #TODO: is the progress bar screwy sent to STDERR to a file????
  #curl: --fail       INSTEAD of 404/403 HTML output, give nothing
  #TODO: is -POST necessary if I have "--form" options? Shouldn't POST be defaulted?
  #wget: '--post-data=name1=value1&name2=value2&name3=value3&name4=value4'

  #wget: --server-response print HTTP header (TODO: to stderr or stdout??)
  #curl: --head            "" (stdout 90%sure)

  curl --max-time 2.3 --disable --silent --show-error --fail -POST --form name1=value1 --form name2=value2 --form name3=value3 --form name4=value4

  #wget: --quiet          TODO all STDERR gone???? yup.
  #wget: --timestamping   TODO set the mod file date/time to whatever it is on the server???????
  wget --quiet --timestamping --output-document=tmp_output_file.dat

CURL VS WGET SUPER EQUIVALENCY EXAMPLES

  #the wget is +30% (more) verbose
  wget --output-document=- http://localhost:8080/wget-curl-me.php | grep Hello
  curl --output - http://localhost:8080/wget-curl-me.php | grep Hello

  wget --quiet  --output-document=- http://localhost:8080/wget-curl-me.php | grep Hello
  curl --silent --output - http://localhost:8080/wget-curl-me.php | grep Hello

  wget --quiet  --output-document=- http://localhost:8080/DNE.php | grep Hello
  echo $? #1
  curl --silent --output - http://localhost:8080/DNE.php | grep Hello
  echo $? #1

GET ALL OF THE CAMERA STATS AND PHOTO/IMAGE EDITOR STATS OF A JPEG MAYBE A PNG

  curl -s http://a.domain.com/bigfile.jpg | exiftool -
  #TODO how do you delete a certain "Comment" or entry or whatever
  #in your JPEG file EXIF data? man exiftool | grep -i remove
  #doesn't even give an example! (3%sure)

CURL POST FORMS BASICALLY TO DO NOTHING JUST TO SEE IF THERE IS AN ERROR OR NOT IN THE RESPONSE I GUESS

  #--show-error forces to show error EVEN when --silent
  #--fail exits 22 with 0 error messages on SERVER errors (eg. 404 and 403)
  #--header is extra headers. I believe with Expect: you can put a URL
  #--location redirects if you get a 3XX error message from the target web page with a redirect URL in its Header or whatever the f***
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


SOME ARRAY AND STRING PUSH/APPEND SH**

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

MAN2TXT RESEARCH

  #I guess any of these would work *shrugs*
  man bash | col -bx > bash.1.txt
  man bash > bash.1.txt #TODO: need to test
  zcat `man --where bash` | groff -t -e -mandoc -Tascii - | less -R #has special escapes in the text *shrugs*

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

RSYNC RESEARCH

  rsync -avxKSH root@oldsystem:/home/ /home/

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

TESTING TO SEE IF YOU'RE IN A TERMINAL RESEARCH

  if test -t 1; then
    echo we are in a TTY/terminal
  fi
  if ! tty -s; then #external command
    echo we are receiving STDIN through this function-call
  fi

CREATING A TEMPORARY FILE

  tmpfilename=$( mktemp -t whateverXXX )  #e.g. /tmp/whatever0S8; empty file.
  #-t is for "TEMPLATE" filename. It is a deprecated feature xD

USING NETCAT

  #on Debian/Listener:
  nc -l -p 1234 > $(mktemp --tmpdir=.)  #ie. ./tmp.05guYTU4jR; empty file.
    #if --tmpdir is set with no directory exported $TMPDIR is used, else /tmp/
    #however, without a TEMPLATE set (as per default, and expressed here-in with this command),
    #--tmpdir is default IMPLIED. Therefore in this case if '.' was not specified as the
    #temporary-target-directory (via i.e. --tmpdir=.) then $TMPDIR (unlikely) then
    #/tmp/ would be used.
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


GITHUB SH**

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

  #your global git config settings:
  cat <<EOF >~/.gitconfig
[user]
	name = ${GITHUB_USERNAME}_cli
	email = ${GITHUB_REGISTERED_EMAIL_FOR_CONTRIBUTION_ON_GITHUB_TRACKING}
[core]
	askPass = ${CHMOD_X_STDOUT_YOUR_TOKEN}
[credential]
	username = ${GITHUB_USERNAME}
EOF


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

  wget -O - 'https://api.urbandictionary.com/v0/define?term=bird' | jq -C | less -R
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

HOLY SH**

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

SHOW A PROGRESS BAR WHILE COMMANDS EXECUTE IN A GUI

  ( cmd1; cmd2; cmd3; cmd4; ) | zenity --progress --title="f***ing wait" --pulsate --autoclose --nocancel

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

CREATE YOUR F***ING LIVE-OS YOU DUMB BI***

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
  CTRL+B, d   DDDetach() from session
  CTRL+B, "   OPEN(horizontal pane)
  CTRL+B, %   OPEN(vertical pane)
  CTRL+B, ARROW KEYS      SWITCH(active/current pane)
  CTRL+B, x   CLOSE(active/current pane)
  CTRL+D      (same thing as above)
  exit        (same thing as above)

  `top`, `tail -f /var/log/syslog`, `watch-home`, `watch-logs-global-journalctl`

RUST CARGO "CRATE" BULLSH**

  #assuming the source code you want to compile is in ~/source-code.c
  cat ~/source-code.c | xclip -selection clipboard
  epiphany https://rust2c.com   #CTRL+V code in here
                                #put code ~/source-code.rs
  cd ~ && {
    sed -i '/register_tool/d;/feature(main/d;/^#\[main\]$/d' source-code.rs && \
    cargo new temp1111 && \
    mv -iv ~/source-code.rs temp1111/src/main.rs && \
    cd temp1111 && echo -e "\nlibc = \"0.2\"" >> Cargo.toml && cargo fetch && cargo build && cargo run
  }

MASS RENAME FILES RESEARCH

  #TODO: use this way instead of `ls *` to get directory/file-glob-based file listings.
  a=(dir/*)
  for file in "${a[@]}"; do echo $file; done
  dir/0 0 0 0.txt
  dir/1 1 1 1.txt
  dir/2 2 2 2.txt
  dir/3 3 3 3.txt

  # convert spaces in files to underscore
  while read myf; do [ -f "$myf" ] || break; mv "$myf" "${myf// /_}"; done <<< "$( ls *\ * )"
  # or just convert it into anonymously named/numbered mp3 files.
  n=1; while read myf; do mv "$myf" "$n.mp3"; (( n++ )); done <<< "$(ls *.mp3)"

PWD ASSESSMENT RESEARCH

  du -S
  492	./data/global/excel
  4	./data/global
  4	./data
  600	.

  du
  492	./data/global/excel
  496	./data/global
  500	./data
  1100	.

  du -h --max-depth=1
  500K	./data
  1.1M	.

CHROOT INTO A GNU/LINUX DISTRIBUTION THAT IS INSTALLED AT AN EXT4 PARTITION ON AN HDD/SSD/USB-HDD AND
  RUN A GOOD/SOLID RECOVERY BASH SHELL IN THERE

  #TODO make sure we're running this as root..
  MOUNT_POINT=/root/mounted_ext4_partition_to_recover_here
  chroot-recoveryshell-automount-inherit-current-system-state(){
    mkdir -v $MOUNT_POINT || return 4
    #TODO mount the actual ext4 here...
    for i in dev{,pts} proc sys; do
      mount --bind /$i $MOUNT_POINT/$i
    done
    chroot $MOUNT_POINT #TODO: OR:  chroot $MOUNT_POINT /bin/su -
    echo REMEMBER to call chroot-recoveryshell-dismantle
    echo "OR you'll be sooooooooooorry"
  }
  chroot-recoveryshell-dismantle(){
    for i in dev{,pts} proc sys; do
      umount $MOUNT_POINT/$i
    done
  }

WICKED FILE MANAGEMENT OBSERVATION WITHIN DIRECTORIES!! RESEARCH

  tree -CAhF --dirsfirst
  ├── [4.0K]  data/
  │   └── [4.0K]  global/
  │       └── [4.0K]  excel/
  │           ├── [ 74K]  armor.txt
  │           ├── [ 56K]  MagicPrefix.txt
  │           ├── [ 64K]  MagicSuffix.txt
  │           ├── [ 54K]  misc.txt
  │           ├── [ 20K]  Runes.txt
  │           ├── [ 86K]  TreasureClassEx.txt
  │           └── [115K]  weapons.txt
  ├── [100K]  All-Diablo2-patch-history.txt
  ├── [ 882]  CMod-README.txt
  ├── [267K]  d2s_save_example2_JSON_colored.txt
  ├── [147K]  d2s_save_example_JSON_colored.txt
  ├── [ 245]  diablo-2-rings-x5-mod-creator.pl
  └── [ 67K]  Necro-killed-by-sorc.mp3
  3 directories, 13 files

  tree -CAFd
  └── data
      └── global
          └── excel
  3 directories

PASTEBIN / FILE UPLOAD RESEARCH

  curl --data-binary @file.txt https://paste.rs/
  curl -F 'f:1=@file.ext' ix.io
  #'zippyshare' use this to download:
  #https://github.com/mansuf/zippyshare-downloader
  #TODO: how to upload? xD

CURL RESEARCH

  #outputs: 000
  curl --output /dev/null --silent --head --location 'http://people.oregonstate.edu/' -w '%{http_code}'

FUNNY

  #python3 C compiler: (lol)
  echo -e "#include <stdio.h>\nint main(void){puts(\"Ran from python3 BABY\");return 0;}" > C.c; python3 -c 'import os;os.system("gcc C.c && ./a.out")'

USING FILENAMES TO OPEN YOUR WEB BROWSER RESEARCH

  debian-forum-open(){
    [ -z "$1" ] && return 55
    [ -f "$1" ] || return 56
    local filename="$(basename "$1")"
    [[ $filename =~ ^[0-9]+\.txt$ ]] || return 57
    local topic_id="${filename%%.*}"
    echo google-chrome "http://forums.debian.net/viewtopic.php?t=$topic_id" 
    google-chrome "http://forums.debian.net/viewtopic.php?t=$topic_id" 
  }

URL FETCHING RESEARCH RESEARCH

  #take a list of a URLs in a text file line-by-line and put them into 001.txt .. 999.txt
  n=0
  while read myLine; do
    (( n++ ))
    wget_out_file=$(printf '%03d.txt' $n)
    echo "$wget_out_file $myLine" >> wget-MAP.txt
    echo "wget -O $wget_out_file '$myLine'" >> wget-TODO.sh
  done <<< "$(<novaks-LINKs.txt)"

SOUNDCARD IMPORTANT INFORMATION

  pactl list sinks | egrep 'Name:|Headphones|Speakers|driver_name'
    Name: alsa_output.pci-0000_00_1f.3.analog-stereo
      alsa.driver_name = "snd_hda_intel"
      analog-output-speaker: Speakers (type: Speaker, priority: 10000, availability unknown)
      analog-output-headphones: Headphones (type: Headphones, priority: 9900, availability group: Legacy 2, availability unknown)

  man pactl | fgrep sink | fgrep port
         set-sink-port SINK PORT
                Set the specified sink (identified by its symbolic name or numerical index) to the specified port (identified by its symbolic name).
                Set  the supported formats of the specified sink (identified by its numerical index) if supported by the sink. FORMATS is specified as a semi-colon (;)

  Sometimes when the soundcard stops playing sound..it's because you have Headphones (ie. the one of two available ports) plugged in,
    but for some reason pulse-audio switched the active port for your sink (ie. your soundcard output) to Speakers.

  So you need to toggle it:  pactl set-sink-port $SINK_NAME $PORT_NAME
  Or in this case:           pactl set-sink-port alsa_output.pci-0000_00_1f.3.analog-stereo analog-output-headphones
  (Or if that doesn't work): pactl set-sink-port alsa_output.pci-0000_00_1f.3.analog-stereo analog-output-speaker

ENCRYPTED FILESYSTEM WITHIN A FILE USED WITH A "LOOP DEVICE" TO MAINTAIN ITS INTEGRITY AS ENCRYPTED

  #TODO: why sh** needs sudo and what doesn't????????????
  #there's a 3% chance this is all gonna work *sobs*

  #setup
  head -c 100M /dev/zero > ~/Documents/shield.img
  sudo losetup /dev/loop4 ~/Documents/shield.img #TODO: 0.2%chance need to create a fresh/ready loop device: losetup --find
  sudo cryptsetup luksFormat /dev/loop4 ~/Documents/shield-passphrase-key-whatever.txt  #be 900%sure this shield-key.txt has no newline bulls***.
  sudo cryptsetup isLuks /dev/loop4 && echo yay
  #generates /dev/mapper/shield_map_name as a real cleared partition...
  sudo cryptsetup open --type luks /dev/loop4 shield_map_name --key-file ~/Documents/shield-key.txt  
  sudo mkfs.ext4 /dev/mapper/shield_map_name  #I'm 30%sure this will work..lol
  #OMFG this better work. We should get a /dev/mapper/shield_map_name1 ???????UGH MOTHER F***
  #or wait it already was a partition...just cleared. huh I'm 3%sure anyways
  ls /dev/mapper/
  mkdir ~/Documents/shield_mapper_mount
  sudo mount -t ext4 /dev/mapper/shield_map_name ~/Documents/shield_mapper_mount
  sudo chmod g+rwx ~/Documents/shield_mapper_mount
  sudo chown :user ~/Documents/shield_mapper_mount
  touch ~/Documents/shield_mapper_mount/hello-world.txt
  file ~/Documents/shield_mapper_mount/hello-world.txt
  echo 'OK looks good. setup is done. time to get the f*** out.'
  sudo umount ~/Documents/shield_mapper_mount
  sudo cryptsetup close shield_map_name
  sudo losetup --detach /dev/loop4

  #now and forever
  sudo losetup /dev/loop4 ~/Documents/shield.img
  sudo cryptsetup isLuks /dev/loop4 && echo yay
  sudo cryptsetup open --type luks /dev/loop4 shield_map_name --key-file ~/Documents/shield-key.txt  
  sudo mount -t ext4 /dev/mapper/shield_map_name ~/Documents/shield_mapper_mount
  echo Time to do work now. Then we gtfo.
  sudo umount ~/Documents/shield_mapper_mount
  sudo cryptsetup close shield_map_name
  sudo losetup --detach /dev/loop4


DD RESEARCH
  
  oflags=sync,direct preferred rather than conv=sync

GNU USERLAND RESEARCH

  realpath ~/Documents/Github-repos #resolves even the softlink
  readlink ~/Documents              #resolves this specific symlink/true-path

SECURE / LIGHTWEIGHT LINUX STUFF RESEARCH

  https://musl.libc.org/

WEB BROWSERS RESEARCH

  #SUPER re-inserts passwords into your inputs as-needed:
  google-chrome

  #alts to chrome
  chromium

  #a little bit of a step down:
  epiphany

  #Super basic GUI-based non-JavaScript web browsers:
  dillo
  netsurf

  #TTY/virtual-console/terminal non-JavaScript non-Image-viewing web browsers:
  lynx
  links2
  links

BEST RECURSIVE GREP

  sudo grep -EIrni --extended-regex --binary-files=without-match --recursive --line-number --ignore-case 'libinput|synaptic' /var/log

FFMPEG COMMAND S***

  #400x285 image
  #Necro-killed-by-sorc.mp3: Audio file with ID3 version 2.4.0, contains:MPEG ADTS, layer III, v2,  56 kbps, 22.05 kHz, Monaural
  ffmpeg -loop 1 -i video-background.png -i Necro-killed-by-sorc.mp3 -shortest video.mp4

  #first one is PERFECT
  time ffmpeg -i female_duck_bathing_boyfriend_comes.mp4 -c:v vp9 -b:v 2.2k -c:a libopus out3.webm #>5minutes
  time ffmpeg -i female_duck_bathing_boyfriend_comes.mp4 -c:v vp9 -b:v 2.2k -c:a opus -strict -2 out2.webm
  ffmpeg -i female_duck_bathing_boyfriend_comes.mp4 -s 640x360 -r 15  resized.mp4
  du -h *.mp4 *.webm
  #108M	female_duck_bathing_boyfriend_comes.mp4
  #5.7M	out3.webm
  #6.0M	out2.webm
  #27M	female_duck_bathing_boyfriend_comes_resized.mp4
  #125M	out.webm

  #experimental s***: (haven't tried)
  time ffmpeg -hwaccel vaapi -vaapi_device /dev/dri/renderD128 -i female_duck_bathing_boyfriend_comes.mp4 -c:v vp9 -c:a opus -strict -2 out.webm

BASH SECRETS
  
  #these ASCII characters are OK in bash without quotes (ie. single or double):
  echo hello++++++++++++%%%%%%%%%%%%%%%%%%%%

  #this is NOT cool
  echo hello???????????????????

MEGA GCC C COMPILING BU****** && LIBC S***

  gcc -ansi -Wall -Wextra -Wpedantic sh17_code.c
  #could just use regular gcc17/gcc18 and use -pedantic to enforce K&R standards????
  #c89 == c90 == ANSI/ISO C

  #good technique:
  #1) variable declarations @ beginning of function
  #2) initialize ALL variable declarations

  #fopen() && fseek()   the libc way
  #open() && lseek()    the POSIX/UNIX way
  #TODO: which one does Windows 10 use? (lol)

  #man 2 open read close chmod

  pinfo libc #brilliant.

INTERESTING BUT STUPID BASH BU******

  timed_connect () { : > /dev/tcp/1.1.1.1/53 & local pid=$! ; (sleep 1 ; kill $pid) & wait $pid ; }

MATH GENIUS STUFF

  Huh. The Integral(From 0->INFI) of  e^(-x) * ln(x) dx   =   ~0.5772156649015328

GNU/LINUX+DEBIAN MALWARE/SECURITY NOTES

  Always fix upstream URL project locations first so all distributions (that package the source code) benefit from the security changes.

  any binary file can be checked for possible malware-s***:
    chkrootkit, rkhunter
    #lesser so:
    binwalk (detect + extract binary magicalized data in an ISO for example)
    string (duh)
    exiftool (duh)

  any binary/jpeg/png/mp3/ogg can hide inforomation with "steganography"
    (sounds 96%rare tho lmao)
    foremost, stegcracker, stegosuite
    +research: 'histogram analysis' and 'steganalysis'
  You can report those findings to debian-security@lists.debian.org
    (like hidden data within those files that are sus)

  jpeg/png can be tested with 'fuzzing'

  Protecting yourself while testing for potential malware information collection:
  Use a 'MAC" + max tight sandbox (limit network access + make $HOME read-only) + minimal/light image-viewer (eg. feh, sxiv)
    + rebind 'image' to read-only + 3%sure use 'sway' + disable dbus +
    bwrap sandbox + kernel protection somehow + could try a VM +
    avoid sandbox escape

  I'm 3%sure ClamAV+extensions[?] would help. I'm 2%sure ClamAV would need a >0%sandbox.
    
BASH S***

  #egrep a variable.hehe
  #DANGER: I really don't know if :alpha: and :alnum: cover MORE than a-zA-Z and a-zA-Z0-9 respectively
  x=ABC_1; LC_ALL=C; [[ $x =~ [[:alpha:]_]+[[:alnum:]_] ]] && printf '%s\n' "$x"; unset LC_ALL

MOAR BASH S*** F***

  while IFS= read -r -d '' file; do something "$file"; done < <(find /foo -print0)

BASH GENIUS MAN PAGE LOOK-UP

  man bash | grep -F -A 2 -B 2 '&>'

DEBIAN RECEIVE / SEND MAIL FROM GMAIL VIA POP3 (RECEIVE) AND SMTP (SEND) RESEARCH

  sudo apt install mpop msmtp #and good f***in' luck

#prevents CTRL+S freezing the tty/virtual-console (ie. until CTRL+Q is hit)
#see:   stty -a | egrep 'start|stop'
stty start undef
stty stop undef

export EDITOR=/usr/bin/vim
export TZ=America/Los_Angeles
export WINEPREFIX=/media/user/DEB_STUFF/dot-wine
apt='apt apt-cache apt-file apt-get apt-mark aptitude dpkg dpkg-query' 
shield_mount_dir='/home/user/Documents/shield_mapper_mount'
urban_url='https://api.urbandictionary.com/v0/define?term='
weechat_logs='/home/user/.weechat/logs'
wow='/media/user/DEB_STUFF/dot-wine/drive_c/Program Files (x86)/Battle.net'
www='/home/user/Documents/Github-repos/localhost-httpd/www'
youtube='/home/user/Documents/youtube-downloads'
export bash1='/home/user/Documents/bash.1.txt'
export git='/home/user/Documents/Github-repos'
export perl_cheatsheet='/home/user/Documents/Github-repos/debian-fresh1/perl-cheat-sheet.txt'
export python_cheatsheet='/home/user/Documents/Github-repos/debian-fresh1/python-cheat-sheet.txt'
export todo='/home/user/Documents/TODO/todo.txt'
alias ..='cd ..'
alias bash++='/usr/bin/perl'
alias battery='echo acpitool -b'
alias bitchx='/usr/bin/weechat -a -p --plugins irc,logger,alias,exec' #no auto-connect to a server and no plug-ins automatically loaded
alias check-network='bash -c "ip route; curl -IL http://nmcheck.gnome.org/check_network_status.txt"'
alias df='df --human-readable --print-type 2>/dev/null'
alias ffmpeg='/usr/bin/ffmpeg -hide_banner'
alias ffprobe='/usr/bin/ffprobe -hide_banner'
alias lightdm-stop='is_desktop_running && sudo systemctl stop lightdm'
alias lightdm-start='is_desktop_running || sudo systemctl start lightdm'
alias myip='bash -c "wget --quiet -O - -4 ifconfig.io; wget --quiet -O - -6 ifconfig.io"'
alias suspend='echo systemctl suspend'
alias tree='/usr/bin/tree -A'
alias tty-exec-desktop-program='echo "DISPLAY=:0.0 google-chrome http://people.oregonstate.edu"'
alias tty-silence-restore='sudo dmesg -n 8'
alias tty-silence='sudo dmesg -n 1'
alias udisks='/usr/bin/udisksctl'
alias vi='/usr/bin/vim'
alias weechat='echo bitchx'
mp3_add(){
  if is_desktop_running; then
    DISPLAY=:0.0 mousepad '/home/user/Documents/youtube-downloads/download.sh' &
  else
    exit 9
  fi
}
bashrc(){
  egrep '^(alias|[a-zA-Z_\-]+\(\){)' ~/.bashrc_user | sort
}
up(){
  local levels="$1"
  [ -z "$levels" ] && levels=2
  [[ "$levels" =~ ^[0-9]$ ]] || return 4
  while [ "$levels" -gt 0 ]; do
    (( levels-- ))
    cd ..
  done
}
str_trim(){
  local args="$*"
  [ -z "$args" ] && return 0
  args="${args#"${args%%[![:space:]]*}"}"
  args="${args%"${args##*[![:space:]]}"}"
  echo -n "$args"
}
str_tolowercase(){
  echo "$*" | tr A-Z a-z
}
str_touppercase(){
  echo "$*" | tr a-z A-Z
}
cd2(){
  [ $# -eq 1 ] || return 4
  if [ -z "$1" ]; then
    cd
  elif [ -d "$1" ]; then
    cd "$1"
  else
    mkdir -pv "$1" && cd "$1"
  fi
}
is_desktop_running(){
  if systemctl status --no-pager lightdm; then return 0; else return 1; fi
}
eject(){
  if [ -d /media/user/MYFAT34 ]; then
    udisksctl unmount --block-device /dev/sdb1
    udisksctl power-off --block-device /dev/sdb
  else
    /usr/bin/eject
  fi
}
Z(){
  cd ~/Documents && unzip Z.zip && shred -uv Z.zip && $EDITOR Z && zip --encrypt Z.zip Z && shred -uv Z
}
apt-non-debian-packages-installed(){
  aptitude search '?narrow(?installed, ?not(?origin(Debian)))'
}
lucky(){ s="$*"; [ -z "$s" ] && return 3; echo $s | perl -ne 'BEGIN{undef $/;}if(m#(https?://[a-zA-Z0-9,%=/.:?_&\-]+)#){print $1;}'; }
goog(){ local s="$*"; links "https://google.com/search?q=${s// /+}"; }
tarhelp(){
  cat <<EOFFFFFFFF
1) gz
2) tar.gz
3) tar.bz2
4) zip
5) tar
6) tar.xz

EOFFFFFFFF
  local myv
  read -n 1 -p '? ' myv
  echo

  case "$myv" in
    1) echo 'gzip 1 #replaces 1 with 1.gz';;
    2) echo tar cvzf create-me.tar.gz 1 2 3 4;;
    3) echo tar cvf create-me.tar.bz2 --bzip2 1 2 3 4;;
    4) echo zip -r create-me.zip 1 2 3 4;;
    5) echo tar cvf create-me.tar 1 2 3 4;;
    6) echo 'tar cvf - 1 2 3 4 | xz -c > create-me.tar.xz';;
  esac

}
vol-low-set(){
  echo pactl set-sink-volume alsa_output.pci-0000_00_1f.3.analog-stereo 20%
  pactl set-sink-volume alsa_output.pci-0000_00_1f.3.analog-stereo 20%
}
vol-up(){
  echo pactl set-sink-volume alsa_output.pci-0000_00_1f.3.analog-stereo +5%
  pactl set-sink-volume alsa_output.pci-0000_00_1f.3.analog-stereo +5%
  while :; do
    read -p 'more? '
    echo pactl set-sink-volume alsa_output.pci-0000_00_1f.3.analog-stereo +5%
    pactl set-sink-volume alsa_output.pci-0000_00_1f.3.analog-stereo +5%
  done
}
vol-down(){
  echo pactl set-sink-volume alsa_output.pci-0000_00_1f.3.analog-stereo -5%
  pactl set-sink-volume alsa_output.pci-0000_00_1f.3.analog-stereo -5%
  while :; do
    read -p 'more? '
    echo pactl set-sink-volume alsa_output.pci-0000_00_1f.3.analog-stereo -5%
    pactl set-sink-volume alsa_output.pci-0000_00_1f.3.analog-stereo -5%
  done
}
xclip-cp(){
  [ -z "$1" ] && return 4
  [ -f "$1" ] || return 5
  DISPLAY=:0.0 xclip -selection clipboard "$1"
}
watch-home(){
  inotifywait --recursive --monitor --quiet --event create ~/.config ~/.local
}
watch-logs-global-journalctl(){
  sudo journalctl -n30 --follow
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
  [[ "$(find -maxdepth 1 -type f -name '* *' | wc -l)" -ne 0 ]] && return 3 #any spaces in files? just exit. f*** it.
  for i in $(echo *.mp3 | tr ' ' '\n' | shuf | tr '\n' ' '); do ffplay $i; read -n 1 -p 'delete? (y/N) ' yn; [[ $yn = 'y' ]] && { echo; rm -v $i; echo; } ;  done
}
alert-me(){
  [ -z "$1" ] && return 3
  wall "$*"
  is_desktop_running && {
    DISPLAY=:0.0 zenity --title="$*" --info --text="$*" --no-wrap
  }
}
remind-me(){
  [ $# -ge 2 ] || return 4
  local sleep_for="$1"
  shift
  echo "sleep $sleep_for && alert-me '$*'"
  sleep "$sleep_for" && alert-me "$*"
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
excuse(){
  echo "Actually, that's a feature; I 0.000000000000%agree
Don't worry, that value is only wrong half of the time; I 98%agree
Even though it doesn't work, how does it feel?; LMAO
Everything looks fine my end; I 0.2%agree
How is that possible?; *silence*
I broke that deliberately to do some testing; *silence*
I can have a look but there's a lot of if statements in that code!; *silence*
I can't make that a priority right now; 17%agreed
I can't test everything; *silence*
I couldn't find any examples of how that can be done anywhere else in the project; 17%true
I couldn't find any examples of how that can be done anywhere online; 19%true
I couldn't find any library that can even do that; lol
I did a quick fix last time but it broke when we rebooted; rofl
I didn't anticipate that I would make any errors; lol
I didn't create that part of the program; what?
I didn't receive a ticket for it; hmm...
I forgot to commit the code that fixes that; lol
I had to do the project backwards as people demanded results out of order; ok?
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
The existing design makes it difficult to do the right thing; I 7% agree
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
We didn't have enough time to peer review the final changes; lol
We outsourced that months ago; lol
We should have updated our software years ago; eh
We spent three months debugging it because we only had one month to build it; eesh
Well at least we know not to try that again; :(
Well done, you found my easter egg!; eegh
Well, at least it displays a very pretty error; lol
Well, that's a first; *silence*
What did you type in wrong to get it to crash?; what to crash?
Where were you when the program blew up?; lol
You can't use that version on your system; for 93% of all men you're average 71%right
You must be missing some of the dependencies; I'm 3%sure that is right
You must have done something wrong; I'm 0%sure of that
You must have the wrong version; ok?
You're doing it wrong; I'm 3%sure you are 900%right
Your browser must be caching the old content; what" | shuf -n 1
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
mpv-debs
nmap-debs
php-debs
python3-doc-debs
python3-tk-debs
reportbug-debs
rustc-debs
steam-debs
vim-debs
w3c-css-validator-debs
weechat-debs
wine-debs
xchm-debs
essential-debs/ack_3.4.0-1_all.deb
essential-debs/acpitool_0.5.1-6_amd64.deb
essential-debs/bind9-dnsutils_1%3a9.16.27-1~deb11u1_amd64.deb
essential-debs/dillo_3.0.5-7_amd64.deb
essential-debs/gawk_1%3a5.1.0-1_amd64.deb
essential-debs/gcc-10-doc_10.2.0-1_all.deb
essential-debs/gcc-doc_5%3a10.1.0-1_amd64.deb
essential-debs/gcc-doc-base_10.1.0-1_all.deb
essential-debs/glibc-doc-reference_2.31-1_all.deb
essential-debs/gparted_1.2.0-1_amd64.deb
essential-debs/gparted-common_1.2.0-1_all.deb
essential-debs/info_6.7.0.dfsg.2-6_amd64.deb
essential-debs/inotify-tools_3.14-8.1_amd64.deb
essential-debs/install-info_6.7.0.dfsg.2-6_amd64.deb
essential-debs/iotop_0.6-24-g733f3f8-1.1_amd64.deb
essential-debs/jpegoptim_1.4.6-1_amd64.deb
essential-debs/jq_1.6-2.1_amd64.deb
essential-debs/libevent-core-2.1-7_2.1.12-stable-1_amd64.deb
essential-debs/libfile-next-perl_1.18-1_all.deb
essential-debs/libfltk1.3_1.3.5-3_amd64.deb
essential-debs/libinotifytools0_3.14-8.1_amd64.deb
essential-debs/libjq1_1.6-2.1_amd64.deb
essential-debs/libjs-underscore_1.9.1~dfsg-3_all.deb
essential-debs/libonig5_6.9.6-1.1_amd64.deb
essential-debs/make-doc_4.3-2_all.deb
essential-debs/manpages-posix_2017a-2_all.deb
essential-debs/ncal_12.1.7+nmu3_amd64.deb
essential-debs/netcat_1.10-46_all.deb
essential-debs/netcat-openbsd_1.217-3_amd64.deb
essential-debs/netselect_0.3.ds1-29_amd64.deb
essential-debs/netselect-apt_0.3.ds1-29_all.deb
essential-debs/netsurf-common_3.10-1_all.deb
essential-debs/netsurf-gtk_3.10-1+b1_amd64.deb
essential-debs/net-tools_1.60+git20181103.0eebece-1_amd64.deb
essential-debs/optipng_0.7.7-1+b1_amd64.deb
essential-debs/perl-doc_5.32.1-4+deb11u2_all.deb
essential-debs/pinfo_0.6.13-1.1_amd64.deb
essential-debs/python3-astral_1.6.1-2_all.deb
essential-debs/python3-tz_2021.1-1_all.deb
essential-debs/strace_5.10-1_amd64.deb
essential-debs/tmux_3.3a-1_bpo11+1_amd64.deb
essential-debs/toilet_0.3-1.3_amd64.deb
essential-debs/toilet-fonts_0.3-1.3_all.deb
essential-debs/tree_1.8.0-1+b1_amd64.deb
essential-debs/txt2man_1.7.1-1+deb11u1_all.deb
essential-debs/weechat-doc_3.0-1+deb11u1_all.deb
essential-debs/wget_1.21-1+deb11u1_amd64.deb
essential-debs/whois_5.5.10_amd64.deb
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


#'e' at boot menu -->  ^linux .* systemd.unit=multi-user.target toram pcie_aspm=off
#the first custom option avoids `sudo systemctl start lightdm` which allows you to set things up before starting the X server

cat /media/user/DEB_STUFF/_bashrc >> ~/.bashrc_user
echo -e "\n\n\n[ -f ~/.bashrc_user ] && . ~/.bashrc_user\n\n\n" >> ~/.bashrc
cp /media/user/DEB_STUFF/_vimrc >> ~/.vimrc
sudo cp /media/user/DEB_STUFF/python4.pl /usr/bin/python4.pl
ln -s /media/user/DEB_STUFF/dot_steam ~/.steam
ln -s /media/user/DEB_STUFF/dot_local_share_Steam ~/.local/share/Steam
ln -s /media/user/DEB_STUFF/dot_cache_mesa_shader_cache ~/.cache/mesa_shader_cache
ln -s /media/user/DEB_STUFF/dot_local_share_volition ~/.local/share/volition
sudo dpkg --add-architecture i386
sudo systemctl stop cups exim4 cups-browsed avahi-daemon avahi-daemon.socket cron anacron.timer
sudo systemctl stop bluetooth && sudo systemctl disable bluetooth
sudo systemctl stop apt-daily-upgrade.timer apt-daily.timer sysstat-summary.timer
sudo systemctl disable apt-daily-upgrade.timer apt-daily.timer sysstat-summary.timer

sudo /usr/sbin/swapon /dev/sda2
sudo apt purge $(dpkg -l | egrep -i 'mozi|fox' | awk '{print $2}' | tr '\n' ' ') epiphany-browser konqueror chromium
sudo apt purge goldendict exfalso parole quodlibet
sudo apt --purge autoremove

#30%sure this is OK
sudo systemctl stop sysstat-collect.timer sysstat-collect
sudo systemctl disable sysstat-collect.timer sysstat-collect

#XFCE4 auto-services are disabled/removed-from-start-of-lightdm-service
for i in calamares-desktop-icon geoclue-demo-agent orca-autostart print-applet xdg-user-dirs xfce4-clipman-plugin-autostart xscreensaver; do sudo rm -iv /etc/xdg/autostart/$i.desktop; done

rmdir ~/Documents
ln -s /media/user/DEB_STUFF/Documents ~/Documents
echo 'now go to /media/user/DEB_STUFF and install all of the software you want'
echo 'oh yeah, change your wallpaper, remove Desktop icons, and get rid of some ~/ directories'
echo
echo
echo 'sudo dpkg --force-depends --remove xfburn xfce4-dict xfce4-goodies'
echo sudo 'apt-mark hold $packages_that_can_be_autoremoved-get_from_apt-finstall_output'
echo
echo you might need to set your timezone in XFCE

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

0 firefox
0 perl5
0 python3
8 golang-1.15
9 php-cli
17 rustc 
26 default-jdk
176 mono-mcs
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
Congratulations python, you're the leader in malware sh**:    9:deb, 218:pm, 1073:perl, 2238:php, 2809:bash, 4860:html, 5694:C++, 12158:C, 13332:go, 28078:java, 39536:py
1.6M	dash-0.5.11+git20200708+dd9ef66/
39M	bash-5.1/
109M	perl-5.32.1/


unbind C-b
set -g prefix C-Space
bind C-Space send-prefix

set -g status "off"
set -g pane-border-status "off"
set -g pane-border-format "hidden"
set -g pane-border-lines "number"
set -g pane-active-border-style "hidden"
set -g pane-border-style "hidden"



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
