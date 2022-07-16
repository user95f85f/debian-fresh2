```#!/bin/bash

for i in `apt-mark showmanual`; do rec="$(dpkg-query -f '${Recommends}' -W $i)"; [ -n "$rec" ] && echo "$i: $rec"; done



#XXX FAQ 001: How can I prevent a Segmentation Fault if I know my program is going to crash?
gdb run-program #OR:
echo exit syscall >> assembly-code.s #OR:
echo 'jmp $' >> assembly-code.s #for/as an infinite loop and then CTRL+C to exit program. OR:
echo 'mov eax, 60; syscall' >> assembly-code.s #OR:
echo 'main function:...ret 0' #and link to libc the program exits! OR:
gdb --set-breakpoint-when-sxx-register-variable-equals=3 run-program #FIXME. OR:
gdb --set-breakpoint-in-code[?????????????????????????????????????????????????????]_line_uuuhh_address_uuuhhhWUT_FIXME=000040 run-program


#NASM >2010 for AMD64 target CPU generation/architecture and you're good to go
# why 64-bit? more registers, more fun.

#nasm standard instructions
AAA        CMC        INT[O]     LOOPNE    POPFQ      SHL[D]
AAD        CMP        IRET[D]    LOOPNZ    PUSH[AD]   SHR[D]
AAM        CMPSB      IRETW      LOOPZ     PUSHAW     STC
AAS        CMPSD      IRETQ      LSS       PUSHF[D]   STD
ADC        CMPSW      JCXZ       MOVSB     PUSHFW     STOSB
ADD        CMPSQ      JECXZ      MOVSD     PUSHFQ     STOSD
AND        CMPXCHG    JMP        MOVSW     RCL        STOSW
BOUND      CMPXCHG8B  LAHF       MOVSX     RCR        STOSQ
BSF        CPUID      LDS        MOVSQ     RETF       SUB
BSR        CWD[E]     LEA        MOVZX     RET[N]     TEST
BSWAP      CQO        LEAVE      MUL       ROL        XADD
BT[C]      DAA        LES        NEG       ROR        XCHG
BTR        DAS        LFS        NOP       SAHF       XLATB
BTS        DEC        LGS        NOT       SAL        XOR
CALL       DIV        LODSB      OR        SAR        LFENCE
CBW        ENTER      LODSD      POPA[D]   SBB        MFENCE
CDQ        IDIV       LODSQ      POPAW     SCASB      SFENCE
CLC        IMUL       LODSW      POPF[D]   SCASD
CLD        INC        LOOP[E]    POPFW     SCASW

#nasm constants
__LINE__         #XXX k
__FILE__         #XXX k
__NASM_VERSION__ #XXX k
__NASM_MAJOR__
__NASM_MINOR__
__TIME__
__BITS__
__FORMAT__
__DATE__        #XXX what format?


#nasm types
FASTCALL
NONE
STDCALL
BYTE
  WORD
  DWORD
  QWORD
  DHWORD [?]
  DQWORD [?]
  HWORD  [?]
  TWORD  [?]
CDECL     [?]
PASCAL    [?]


TODO: NASM VS GCC GAS?
You can see the .c go to the .s with `gcc`!
You can convert your intel-NASM Assembler/Assembly .s code into GCC-gas with:
  apt -s install intel2gas
  # intel2gas #which 0% works so far

TODO: what is GCC PIE? How to get GCC to disable PIE?
TODO: default 'rel' for the assembler (ie. .s to .o) is 0% good?

apt -s install fasm #fast assembler
apt -s install fcml #yet another assembler
apt -s install yasm #?????????????????????????????????????????
apt -s install sasm #IDE for gcc-gas, nasm (ie. intel), and fasm assembler code ???????????????????

`cc` defaults to `gcc` on GNU/Linux Debian 11.4

You cannot write 16-bit x86 Assembly code in an Intel i7 CPU directly.
  However, I'm 98% sure you can tell the Assembly code linker to accept your Assembly code in 16-bit x86 "mode" (TODO: how?)





cat /etc/issue.net
Debian GNU/Linux 11

nasm --version
NASM version 2.15.05

ld --version | head -1
GNU ld (GNU Binutils for Debian) 2.35.2

cat my-first.nasm
  bits 64
  default rel   ; Use RIP relative addressing.

  section .rodata

msg: db `Hello\n`
msg_len equ $ - msg

  section .text

  global writemsg
writemsg:
  mov eax,1    ; EAX, instead of RAX will zero upper 32 bits.
  mov edi,eax  ; RDI=STDOUT_FILENO (upper 32 bits zeroed).
  lea rsi,[msg] ; Need to load RSI (because it is a pointer). LEA because 'msg' is RIP relative.
  mov edx,msg_len ; RDX (upper 32 bits zeroed).
  syscall
  ret

nasm -felf64 my-first.nasm  #to include more '.nasm' assembly files you can offer a -I~/nasm_includes/ command option! (TODO: how do you import though in the code?) (TODO: do the imported assembly files have to have the .asm extension, or is .nasm OK?) (TODO: is gnu error reporting better??? -X gnu)

file my-first.o
my-first.o: ELF 64-bit LSB relocatable, x86-64, version 1 (SYSV), not stripped

ld --output=my-first my-first.o
ld: warning: cannot find entry symbol _start; defaulting to 0000000000401000

file my-first
my-first: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), statically linked, not stripped


objdump -D my-first.o       #no STDERR output
my-first.o:     file format elf64-x86-64

Disassembly of section .rodata:

0000000000000000 <msg>:
   0:	48                   	rex.W
   1:	65 6c                	gs insb (%dx),%es:(%rdi)
   3:	6c                   	insb   (%dx),%es:(%rdi)
   4:	6f                   	outsl  %ds:(%rsi),(%dx)
   5:	0a                   	.byte 0xa

Disassembly of section .text:

0000000000000000 <writemsg>:
   0:	b8 01 00 00 00       	mov    $0x1,%eax
   5:	89 c7                	mov    %eax,%edi
   7:	48 8d 35 00 00 00 00 	lea    0x0(%rip),%rsi        # e <writemsg+0xe>
   e:	ba 06 00 00 00       	mov    $0x6,%edx
  13:	0f 05                	syscall 
  15:	c3                   	retq   


  https://dl.fedoraproject.org/pub/alt/live-respins/


YET ANOTHER BULL**** ISO BURNING TO A LIVEOS DEBIAN-BASED S***

  https://grml.org/features/

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
  #this is like:    printf '' > ~/hello.txt
  #this is like:    rm ~/hello.txt; touch ~/hello.txt
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

PRINT/ECHO TO STDERR

  printf '%s\n' 'error message here' >&2

BASH FAILURE WITHIN A $(exit 1) CAN BE DEALT WITH '||'

  #for example:
  str_output=$(perl -e 'print "Hello";exit 1;') || echo perl failed
  printf '%s\n' "$str_output" #Hello

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


SOME ARRAY AND STRING PUSH/APPEND SH** AND A WEIRD LOOP-PRINT RESEARCH

  str='123'
  arr=(1 2 3)
  str+=4      #1234
  arr+=(4)    #(1 2 3 4)

  ar=(1 2 3 4 5 6 7 8 9)
  printf '<%s>\t' "${ar[@]}"
  #<1>   <2>   <3>   <4>   <5>   <6>   <7>   

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

PRINT OUT ALL DIRECTORIES (ie. DIRECTORY NAMES) IN PWD

  #this is safe even if the directories in pwd have whitespace
  for d in */; do
    printf '%s\n' "$d"
  done

PRINT OUT ALL FILES IN ALL DIRECTORIES IN PWD

  #this is safe even if the directories in pwd have whitespace
  for a_file_or_directory_within in */*; do
    printf '%s\n' "$a_file_or_directory_within"
  done

PRINT OUT ALL FILES AND DIRECTORY NAMES IN PWD

  #this is safe even if any file in PWD has whitespace
  for f in *; do
    printf '%s\n' "$f" 
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

  unset -v tmp_c_file
  unset -v tmp_compiled_file
  tmp_c_file=$(mktemp --tmpdir cfileXXX.c)
  tmp_compiled_file=$(mktemp --tmpdir compiledXXX)
  trap '[[ $tmp_c_file ]] && rm -vf -- "$tmp_c_file";[[ $tmp_compiled_file ]] && rm -vf -- "$tmp_compiled_file";' EXIT

  #<HISTORICAL>
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

  #RESEARCH: (0% chance works)
  nmap -oN $out_file -Pn -n -Su -sS VC --top-ports 1-80 -e $interface_name -v

SLURP A FILE INTO A VARIABLE

  file_get_contents_var=$(<my_file_name.txt)  #the quotes are 0%necessary for simple assignment.

THIS SCRIPTS CONTENTS

  this_scripts_file_contents_string=$(<$0)

REM IN BASH 0 (IS STDIN) 1 (IS STDOUT) 2 (IS STDERR)

STDIN PROCESSING IN BASH + USING BASH ARRAYS

  ar_input=('line 1' 'line 2' 'line 3')   #XXX these two paragraphs need to get cleaned up
  str_input=$(printf '%s\n%s\n%s' "${ar_input[@]}")
  filename_input=~/dlt
  printf '%s' "$str_input" > "$filename_input"
  str_line=''
  ar_lines=()

  ar=('line 1' 'line 2' 'line 3')
  for i in "${ar[@]}"; do  #this does good.
    printf '%s\n' "$i"
  done
  printf '%s\n' "${ar[@]}"   #this does good
  printf '%s\n' "${ar[@]}" | while read myline; do printf '%s\n' "$myline"; done   #this does good too

  # FILE contents As-BASH string 2 line-by-line read string
  while read str_line; do
    printf '%s\n' "$str_line"
  done <<< "$(<"$filename_input")"

  # SLURP a variable's contents INTO a BASH string variable
 	read -d '' str_all <<<$'blah\nblah\nblah'
	echo $str_all
	#blah blah blah
	echo "$str_all"
	#blah
	#blah
	#blah 
	printf '%s' "$str_all" #no newline at end-end-end
	#blah
	#blah
	#blah

  # BASH string As-STDIN 2 BASH array research
  # -r: no backslash-escape-literals executed
  # -a: read the new contents into an array
  ar_lines=()
  read -ra ar_lines <<< "$str_input"

  # BASH command 2/as BASH string 2 BASH string as a line
  read str_line <<< $(printf '%s' "$str_input") #no newline even with echo
  read whatever <<<'const string'

  # FILE contents As-STDIN 2 BASH command STDIN
  pager <"$filename_input" 

USING MAPFILE TO SLURP A FILE INTO A BASH ARRAY

  echo 123 > hello.txt; mapfile < <(cat hello.txt); echo "${MAPFILE[@]}"

BASH MATH CAN SORTA WORK WITH FLOAT-LIKE ARITHMETIC BUT CUTS OFF THE DECIMAL OUTPUT

	echo $(( 1 / 2 ))
	#0
	echo $(( 4 / 2 ))
	#2
	echo $(( 4 / 1 ))
	#4
	echo $(( 4 / 3 ))
	#1

PRINTF MATH HEXADECIMAL N-BASE ARITHMETIC

  hexnum=ee3;decnum=500;printf '0x%08x\n' $(( 16#$hexnum + $decnum )) #0x000010d7

IMAGE MAGICK/IMAGEMAGICK CONVERT/IMPORT COMMANDS + SCREENSHOT PRINT SCREEN STUFF[?]
  
  RECORD_FOR_HOW_LONG_IN_SECONDS=12 #must be a whole number

  echo sleep 3.2
  sleep 3.2

  n=0
  now="$(date +%s)"
  while :; do
    (( n++ ))
    echo import -window root $(printf '%03d' $n).png
    import -window root $(printf '%03d' $n).png
    sleep 0.08
    [ $(( $(date +%s) - $now )) -gt $RECORD_FOR_HOW_LONG_IN_SECONDS ] && break
  done

  #-flip -flop -grayscale -rotate 90
  echo convert -delay 41 *.png -resize 43% animation.gif
  convert -delay 41 *.png -resize 43% animation.gif

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

  #optional zenity options:  --width 640 --height 400

SHOW A PROGRESS BAR WHILE COMMANDS EXECUTE IN A GUI

  ( cmd1; cmd2; cmd3; cmd4; ) | zenity --progress --title="f***ing wait" --pulsate --autoclose --nocancel

INSTALL A DESKTOP ENVIRONMENT ON YOUR DEBIAN

  tasksel --list-tasks
  #i desktop                  Debian desktop environment
  #u gnome-desktop            GNOME
  #i xfce-desktop             Xfce
  #u gnome-flashback-desktop  GNOME Flashback
  #u kde-desktop              KDE Plasma
  #u cinnamon-desktop         Cinnamon
  #u mate-desktop             MATE
  #u lxde-desktop             LXDE
  #u lxqt-desktop             LXQt
  #u web-server               web server
  #u ssh-server               SSH server
  #i laptop                   laptop

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
  492   ./data/global/excel
  4     ./data/global
  4     ./data
  600   .

  du
  492   ./data/global/excel
  496   ./data/global
  500   ./data
  1100  .

  du -h --max-depth=1
  500K  ./data
  1.1M  .

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

CHROOT RESEARCH NOTES

  mount -o bind /dev /mnt/mysys/dev
  mount -o bind /dev/pts /mnt/mysys/dev/pts
  mount -t sysfs /sys /mnt/mysys/sys
  mount -t proc /proc /mnt/mysys/proc
  chroot /mnt/mysys /bin/bash

  grub-install
  update-grub

  exit chroot, umount all, reboot || fix system with LiveOS as-in-the chroot

  rsnyc -aHKSrxv user@sh17system(192.168.0.42):/home/ /home/

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
  #108M female_duck_bathing_boyfriend_comes.mp4
  #5.7M out3.webm
  #6.0M out2.webm
  #27M  female_duck_bathing_boyfriend_comes_resized.mp4
  #125M out.webm

  #experimental s***: (haven't tried)
  time ffmpeg -hwaccel vaapi -vaapi_device /dev/dri/renderD128 -i female_duck_bathing_boyfriend_comes.mp4 -c:v vp9 -c:a opus -strict -2 out.webm

BASH SECRETS
  
  #these ASCII characters are OK in bash without quotes (ie. single or double):
  echo hello++++++++++++%%%%%%%%%%%%%%%%%%%%

  #this is NOT cool
  echo hello???????????????????

MEGA GCC C COMPILING BU****** && LIBC S***

  gcc -ggdb3 -Og -Wall -Wextra -Wpedantic sh17_code.c
  #NOTES:
  #-march=native I'm 99% sure is 0% good for putting your output "executable"
  #     on other machines.
  #-O0 strips about 3% debugging information from the output "executable"
  #-Ofast is standards-breaking compliancy-whatever

  #TODO: does it even f***ing matter what C specification I use? UGH bulls***.
  #      I'm 90% sure default is 93% good...right???RIGHT?!!!
  #could use -pedantic to enforce K&R standards????
  #-ansi:  c89 == c90 == ANSI/ISO C

  #good technique:
  #1) variable declarations @ beginning of function
  #2) initialize ALL variable declarations

  #to avoid buffer overflows and Segmentation Faults
  #look up:  address sanitation, asan, gcc asan, address sanitizer
  #(as software packages or gcc options [eg. -fsanitize=address])

  #fopen() && fseek()   the libc way
  #open() && lseek()    the POSIX/UNIX way
  #TODO: which one does Windows 10 use? (lol)

  #man 2 open read close chmod

  pinfo libc #brilliant. requires:   sudo apt install glibc-doc-reference 

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

DATE-BASED CHARACTER SEQUENCES TO SHOW DATE/DATETIME INFORMATION LIKE MINUTES SECONDS DAY OF MONTH 12HOUR 24HOUR AMPM NAME OF MONTH YEAR IN LIKE THREE FORMATS

  man strftime | grep -F -A 111 'ordinary character sequences'
  man date | grep -F -A 111 'Interpreted sequences are:'
  #TODO: now do printf lol

WHAT A GOOD COPYRIGHT NOTICE EXAMPLE

  Copyright (c) user95f85f

  Permission to use, copy, modify, and/or distribute this software for any
  purpose with or without fee is hereby granted.

  THE SOFTWARE IS PROVIDED "AS IS" AND THE AUTHOR DISCLAIMS ALL WARRANTIES WITH
  REGARD TO THIS SOFTWARE INCLUDING ALL IMPLIED WARRANTIES OF MERCHANTABILITY
  AND FITNESS. IN NO EVENT SHALL THE AUTHOR BE LIABLE FOR ANY SPECIAL, DIRECT,
  INDIRECT, OR CONSEQUENTIAL DAMAGES OR ANY DAMAGES WHATSOEVER RESULTING FROM
  LOSS OF USE, DATA OR PROFITS, WHETHER IN AN ACTION OF CONTRACT, NEGLIGENCE OR
  OTHER TORTIOUS ACTION, ARISING OUT OF OR IN CONNECTION WITH THE USE OR
  PERFORMANCE OF THIS SOFTWARE.

COLOR/COLOUR MASSIVE PARALLEL BASH SCRIPT EXECUTING BULL**** PLUS LOCAL MULTI-LINED VARIABLE DECLARATION

  ###F*** TODO!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
  #gotta replace 'echo -n' and 'echo -e' and 'echo -ne' and 'echo -en' with printf SH**
  #</TODO>
  red=$(tput setaf 1) #notice: no double quotes! Huh. Must be a simple variable declaration.
  reset=$(tput sgr0)
  printf '4' > jobs_proc_file.txt
  cat <<EOF454545 > .links
https://google.com
https://facebook.com
https://twitter.com
EOF454545

  do_parallel(){
    local total=100 DL='echo -n wget'
    cat .links | parallel -j "$2" --jobs "$2" "echo -ne \"\\r\"; echo -n \"Jobs: ${red}${2}${reset}; Link: ${red}${1}${reset}; {#}/$total \"; $DL {}; echo -n '          '"
  }

  do_parallel something-special-here jobs_proc_file.txt
  echo #a basic newline at the end of the program

SUPER SYSTEM SUSTAIN/BACKUP

  rsync -aAXHSv /* /path/to/shared/folder \
    --exclude={/dev/*,/proc/*,/sys/*,/tmp/*,/run/*,/mnt/*,/media/*,/lost+found,/home/*/.gvfs}
sudo tcpdump -i any -U 'src port 53 or dst port 53' > out.dat
#!/bin/bash



tail -f out.dat | grep --line-buffered -E 'my\.jetpack\.domain.* (A\?|AAAA\?|CNAME) ' | cut --characters=24-
watch-logs-global-journalctl is a function
watch-logs-global-journalctl () 
{ 
    sudo journalctl -n30 --follow
}
watch-home is a function
watch-home () 
{ 
    inotifywait --recursive --monitor --quiet --event create ~/.config ~/.local
}

FASTER GZIP ALTERNATIVE  RESEARCH

  apt install lzop #a faster compression solution VS gzip for streams:
  echo whatever | lzop > what.lz
  cat what.lz | lzop -d #should get 'whatever' as output

MASTERFUL TECH/CODING/SOURCE-CODE STUFF RESEARCH

  goog SHA1 site:geekflare.com
  goog SHA1 collision site:github.com

SWAP ESCAPE/ESC KEY AND CAPS LOCK TO MAKE IT EASIER TO USE VIM WHILE IN X11

  # this doesn't work while in a tty/virtual-console (even while .. lightdm for example.. is running)
  DISPLAY=:0.0 setxkbmap -option caps:swapescape
  # this does iff you're in a Desktop Environment (eg. XFCE or KDE)
  setxkbmap -option caps:swapescape

IRC BOT RESEARCH

  google: limnoria
  It's in python3 I guess

BASHRC ALIAS/"FUNCTION" TO QUICKLY SEARCH THE WEB VIA A TTY/VIRTUAL-CONSOLE/TERMINAL "WEB BROWSER"/ONLINE-HTML-VIEWER

  goog(){ local s="$*"; links "https://google.com/search?q=${s// /+}"; }

OPEN A LIST OF URLs INSTANTLY IN EPIPHANY-BROWSER/EPIPHANY/EPIPHANY(THE-GUI-WEB-BROWSER-THAT-HAS-JAVASCRIPT)

  url_list=(
http://google.com
http://yahoo.com
  )
  for u in "${url_list[@]}"; do
    epiphany "$1" &
  done

DOWNLOAD URLs INTO .TXT FILES INTO PWD

  url_list=(
http://google.com
http://yahoo.com
  )

  n=1
  while [ -f "$(printf '%03d.txt' $n)" ]; do (( n++ )); done
  for u in "${url_list[@]}"; do
    (( n++ ))
    output_document="$(printf '%03d.txt' $n)"
    echo wget --tries=1 --timeout=8 "--output-document=$output_document" -- "$u" >> wget.log
    wget --tries=1 --timeout=8 "--output-document=$output_document" -- "$u" 
  done

  #<ALTERNATIVE FROM URL-LIST.TXT INSTEAD OF FROM AN ARRAY "LIST"
  # (ie. an "array" which is technically 0.3% right because it's a literal LIST
  #   before the bash interpreter turns into an a
  #   blah-blah-blah-array-data-structure-stfu) EMBEDDED IN YOUR BASH SCRIPT>

  URL_LIST_TXT=~/Downloads/url-list.txt
  [ -f "$URL_LIST_TXT" ] || exit 30
  mkdir -p ~/Downloads/downloaded-urls-todo-readme && cd ~/Downloads/downloaded-urls-todo-readme
  n=1
  while [ -f "$(printf '%03d.txt' $n)" ]; do (( n++ )); done
  while read u; do
    (( n++ ))
    output_document="$(printf '%03d.txt' $n)"
    echo wget --tries=1 --timeout=8 "--output-document=$output_document" -- "$u" >> wget.log
    wget --tries=1 --timeout=8 "--output-document=$output_document" -- "$u"
  done <<<$(cat "$URL_LIST_TXT")


DOES YOUR PYTHON3 NEED SOME BRACKETS IN ITS SOURCE CODE?

  #!/usr/bin/perl
  #          save this program/script/whatever as: /usr/local/bin/python4
  # put this on top of your "93%" python3 scripts: #!/usr/local/bin/python4
  #
  #_____# you can now do something like this:
  #     if 1 == 1:
  #     {
  #       print("Hmmm")
  #     }
  #
  #     #`python3` will execute the (brackets) filtered-out in a "filtered-out [output] file"
  #     #NOTE: line-numbers for error message output of `python3` is preserved.
  #

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

RESEARCH PYTHON3 MANAGING DATA/COLUMNS WITHIN A CSV FILE WITH pandas PYTHON3 MODULE

  > Depends: python3-numpy (>= 1:1.15~)
  > Description-en: data structures for "relational" or "labeled" data
  >  pandas is a Python package providing fast, flexible, and expressive
  >  data structures designed to make working with "relational" or
  >  "labeled" data both easy and intuitive. It aims to be the fundamental
  > Homepage: https://pandas.pydata.org/

  #this code is sh** but it expresses something 93% interesting
  files_list = glob.glob('*.txt')
  main_df = pandas.read_csv(files_list[0], sep='\n', header=None)[0].str.cat()
  for i in range(0, len(files_list)):
  {
    text = pandas.read_csv(files_list[i], sep='\n', header=None)[0].str.cat()
    df = pandas.DataFrame([text], columns=['note'])
    main_df = pandas.concat([main_df, df], axis=0)
  }

GREP ALTERNATIVE: "DOES THIS FILE HAVE THIS F***ING STRING?"

  #78% good
  if grep $keyword $filename >/dev/null; then
    echo matched.
  fi
  #blah:
  if grep -q $keyword $filename; then
    echo matched.
  fi

CR** USERAGENTS FOR YOUR CURL AND WGET BU******

  curl --user-agent $this $other_options
  wget --user-agent=$this $other_options

  #the first one is super old (lol)
  Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:49.0) Gecko/20100101 Firefox/49.0
  Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/101.0.4951.67 Safari/537.36
  Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:100.0) Gecko/20100101 Firefox/100.0
  Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/15.4 Safari/605.1.15
  Mozilla/5.0 (X11; Linux x86_64; rv:99.0) Gecko/20100101 Firefox/99.0
  Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/101.0.4951.41 Safari/537.36
  Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/100.0.4896.127 Safari/537.36 Edg/100.0.1185.50
  Mozilla/5.0 (Linux; Android 10) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/84.0.4147.111 Mobile Safari/537.36
  Mozilla/5.0 (Android 10; Mobile; rv:68.0) Gecko/68.0 Firefox/68.0
  Mozilla/5.0 (iPhone; CPU iPhone OS 13_6 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/13.1 Mobile/15E148 Safari/604.1
  Mozilla/5.0 (iPhone; CPU iPhone OS 13_6 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) CriOS/83.0.4147.71 Mobile/15E148 Safari/604.1
  Mozilla/5.0 (iPhone; CPU iPhone OS 13_6 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) FxiOS/28.0 Mobile/15E148 Safari/605.1.15



BASIC PYTHON3 SCRIPT EXECUTABLE PROGRAM

  #!/usr/bin/python3
  import sys
  import time
  import os
  def main():
  {
    WAIT_SECONDS = 1.2223
    name = os.getlogin()
    s = input('\n\nReady %s? (Y/n) ' % name)
    name = None
    if s.lower() not in ['', 'y']:
      return
    print('please wait %f seconds' % WAIT_SECONDS)
    time.sleep(WAIT_SECONDS) #in seconds
    print('Here is the arguments you gave this program:')
    print(sys.argv)
  }
  if __name__ == '__main__':
    main() 

PYTHON3 WRITE TO AN OPENED PROCESS STDIN

  import os
  def main():
  {
    p = os.popen('/bin/cat', 'w')
    p.write("To: receiver@example.com\n")
    p.write("Subject: test\n")
    p.write("\n")
    p.write("Some text\n")
    p.write("some more text\n")
    ret = p.close()
    if ret != 0:
      print("Your `cat` process 0% worked out I guess:", sts)
  }
  if __name__ == '__main__':
    main() 

PYTHON3 SOMEBODY IS ENDING MY SCRIPT PRE-MATURELY

  import math
  import signal
  import sys
  import time
  UserIsHittingCTRLC(signal, None):
    print('SIGTERM caught.', signal)
    sys.exit(42)
  if __name__ == '__main__':
    signal.signal(signal.SIGINT, UserIsHittingCTRLC)
    time.sleep(math.pi)

PYTHON3 FLASK RESEARCH

  run with apache2 server or gunicorn

VIDEO TO GIF ANIMATION

  #start at 12 seconds, clip 5 seconds total, use FPS of 5 frames per second
  #use 200 pixel width (30% sure)
  ffmpeg -i whatever.avi -ss 12 -t 5 -filter_complex "[0:v] fps="5",scale="200":-1" "whatever.gif"

270 CHARACTER LINUX FILESYSTEM FILENAME

  #OH YEAHHHHHHHH (tmpfs [ie. RAM/liveOS/overlay mount/filesystem] & ext4)
  mkdir ~/does_not_exist && cd ~/does_not_exist && { touch $(printf '%s' {1..119}).txt; printf '%s' *.txt; printf '%s' *.txt | wc -c ; } #270
  #123456789101112131415161718192021222324252627282930313233343536373839404142434445464748495051525354555657585960616263646566676869707172737475767778798081828384858687888990919293949596979899100101102103104105106107108109110111112113114115116117118119.txt

CREATE A FILE IN A PRE-DETERMINED FILESIZE (THAT IS MOST LIKELY GONNA BE ZEROED OUT)

  #XXX I'm 96% sure a 1024 byte file is a 1 KiB file
  #and then a 1000 byte file is a KB (ie. 'kilo', which means 1000 of something, which in this case is bytes) file
  HOW_BIG_DO_YOU_WANT_THE_FILE_IN_BYTES=1024; printf '%x: 00\n' $(( $HOW_BIG_DO_YOU_WANT_THE_FILE_IN_BYTES - 1 )) | xxd -r > one_KiB_file.txt

ANDROID EXECUTE SHELL COMMANDS VIA USB CONNECTION RESEARCH

  1) enable USB debugging in Developer Options
  2) sudo apt install adb
  3) adb shell <<<"echo hello; exit" | sed 's/hello/sup/' > output_from_android_phone.txt

OPENING A COMMAND'S OUTPUT AS A TEMPORARY FILE (DESCRIPTOR) IN BASH

  vim <(zcat SERVICES.TXT.gz) SYNC.TXT 

CHECKSUMS FOR GNU/LINUX REPORT WHAT THEY ARE

  #256, 384, 512 is 71% good
  #using multiple is 21% good (especially if your target system only has md5sum like 7zip Windows ie. 30% sure)
  echo whatever > /tmp/zzz.txt; for i in md5sum sha1sum sha256sum sha384sum sha512sum; do printf '%6s: ' "${i%%sum}"; $i /tmp/zzz.txt | awk '{printf "(%3d) %s\n", length($1), $1}'; done; rm /tmp/zzz.txt

     md5: ( 32) d8d77109f4a24efc3bd53d7cabb7ee35
    sha1: ( 40) 52e98718f012ca15f876ae405b57848b5c7128dd
  sha256: ( 64) cd293be6cea034bd45a0352775a219ef5dc7825ce55d1f7dae9762d80ce64411
  sha384: ( 96) 5c434c63c8098e5e05e61f5839fd08f8ccd58d6f757cc96e0f45571445c28f08fc5f7189ccf40211f3fae40a81bb9fbb
  sha512: (128) 663fad5bcd324358db08bc078aba90177f7854b26b38fa06f9a1f454ec7001ee8daf46cb52ab0eb776bc747cdc4fe6ad8a3b3124fbe0a39c4e1e8ed01f6b6e9a




PERFECT MATH IN PERL 5

  #!/usr/bin/perl

  use warnings;
  use strict;
  my $eval_this = q/


0 + 0 * (-1) - ( -0 + ( 0 * -0 + (-1) * ( (-1) * (-1) * 0 + 0 ) ) )


  /;
  print( eval($eval_this), "\n" ); #0


VARIOUS MEATS AND FOODS

  turkey, chicken, cow (beef), pig (pork)
  VS
  goat, sheep, kavurma, succuk

  chamomile tea, mint tea
  VS
  jasmine tea

BASH REDIRECT EVERYTHING (ie. STDOUT AND STDERR) TO A FILE

  $command > "$stderr_and_stdout_output_text_file" 2>&1

  #For example:
  pydoc -k print > p.txt 2>&1

CONVERT MOON PHASE [0, 28] TO MOON PERCENTAGE [0%, 100%] IN PYTHON3

  #!/usr/bin/python3

  import random

  if __name__ == '__main__':
    ITERATIONS = 20
    #pre-notes:     0-14   0%-100%   14-28    100%-0%
    for _ in range(0,ITERATIONS):
      _ = None
      moon_phase = random.uniform(0, 28)      
      print('%-38s' % ('{}{} units'.format('moon_phase=', moon_phase)), end='')
      if moon_phase > 14.00000000:
        pre_moon_percent2 = moon_phase - 14.0
        super_accurate_almost_moon_percent = 1.0 - ( (28.0 - 14.0 - pre_moon_percent2) / (28.0 - 14.0))
        pre_moon_percent2 = None
      else:
        super_accurate_almost_moon_percent = moon_phase / 14.0
      moon_phase = None

      print('{}{}'.format('moon_percent=', '%0.2f%%' % (super_accurate_almost_moon_percent * 100.0)))
      super_accurate_almost_moon_percent = None

"""
#sample output:

moon_phase=0.4931260130136699 units   moon_percent=3.52%
moon_phase=0.8105894455562064 units   moon_percent=5.79%
moon_phase=4.183471367016093 units    moon_percent=29.88%
moon_phase=4.302431703836977 units    moon_percent=30.73%
moon_phase=4.863611820742577 units    moon_percent=34.74%
moon_phase=5.5480365331742405 units   moon_percent=39.63%
moon_phase=5.992918731701295 units    moon_percent=42.81%
moon_phase=6.6982896574532615 units   moon_percent=47.84%
moon_phase=7.1949990059006295 units   moon_percent=51.39%
moon_phase=9.395560540636705 units    moon_percent=67.11%
moon_phase=12.417777865338428 units   moon_percent=88.70%
moon_phase=12.762129317228336 units   moon_percent=91.16%
moon_phase=13.72199284155047 units    moon_percent=98.01%
moon_phase=20.32376105689604 units    moon_percent=45.17%
moon_phase=20.921416577195036 units   moon_percent=49.44%
moon_phase=21.152651177010114 units   moon_percent=51.09%
moon_phase=23.167365278262395 units   moon_percent=65.48%
moon_phase=23.676596602027843 units   moon_percent=69.12%
moon_phase=23.98537665556813 units    moon_percent=71.32%
moon_phase=27.821417224659932 units   moon_percent=98.72%

"""


USE PYTHON3 ASTRAL LIBRARY/MODULE/SCRIPT/WHATEVER TO GET THE MOON PHASE FOR WHERE YOU LIVE (USES GOOGLE DATA-SERVERS EEK)

  sudo apt install python3-astral #depends on:  python3-requests python3-tz
  mkdir ~/Downloads/get-moon-phase-py3/
  cd ~/Downloads/get-moon-phase-py3
  cp /usr/lib/python3/dist-packages/astral.py .
  #I'm 3% sure this will work
  cat <<EOFTTT >> astral.py 

import sys
a = Astral()
location = a[sys.argv[1]]
#pre-notes:     0-14   0%-100%   14-28    100%-0%
moon_phase = location.moon_phase(None, float)
print('The moon phase for location {} is {}'.format(sys.argv[1], moon_phase)

EOFTTT
  python3 astral.py 'Chisinau,Moldova' #location can be grepped from astral.py as some city near you (it's the 1st argument 90% sure 90%sure needs the slash tho
  #The moon phase for location Chisinau,Moldova is 11.466777777777779




UTF-8/UTF-16[?] CODE TO STRING OUTPUT IN BASH, FOR EXAMPLE

  printf '%s\n' $'\u2015'   #horizontal bar
  printf '%s\n' $'\u2019'; echo "'"; echo '`' #99% similar

ALTERNATIVE TO `strace` FOR DETECTING WHAT A PROGRAM IS DOING, ACTUALLY IN THIS CASE IT'S WHY CPU IS 100% RESEARCH

  perf record -g $command
  perf report -g

VIM FOR WHEN VIEWING LOGS TO AUTO-UPDATE AND CAN'T WRITE TO THEM

  :se autoread readonly

OUTPUT YOUR NEWLINED-SEGREGATED OUTPUT INTO A NICE TABLE TABULAR OUTPUT IN BASH ON YOUR TTY/VIRTUAL-TERMINAL YAAAAAAY

  printf '%s ' {1..200} | column --output-width "$(tput cols)"

  #REAL EXAMPLE:

  # both of these do the exact same thing:
  python3 -c "import os;print('\n'.join([f for f in os.listdir('/usr/bin') if os.path.islink('/usr/bin/{}'.format(f))]), end='')" | column --output-width "$(tput cols)"
  { for i in /usr/bin/*; do [ -L "$i" ] && printf '%s\n' "${i##*/}"; done ; } | column --output-width "$(tput cols)"



CONVERT ALL 3-BEGINNING-SPACES TO TABS AND THEN CONVERT THE TABS TO 2 SPACES
  
  | unexpand --first-only --tabs=3 | expand --tabs=2


DELETE ALL NEWLINES INCOMING FROM A BASH COMMAND'S STDOUT

  | tr --delete '\n'


DELETE ALL NON-PRINTABLE CHARACTERS (SPACES ARE PRINTABLE) FROM A BASHS' COMMANDS' STDOUT

  | tr --delete --complement '[:print:]'

BASH DEBUGGING / TROUBLESHOOTING

  bash -n myscript.sh #check for syntax errors. don't execute/run.
  # add:   set -x   to the top of your script show all commands executed are printed to STDOUT[?]

GUI BASH MESSAGE BOXES RESEARCH

  xmessage || zenity || ssft #apt -s install ssft

GETTING THE SIZES OF FILES IN PWD

  ls -laS

GETTING THE SIZES OF SUB DIRECTORIES WITHIN PWD

  du -h | sort -h

WHERE TO PUT ALL YOUR PERSONAL PROGRAM'S TMP/TEMP FILES, CACHE FILES, AND CONFIGURATION OF YOUR PROGRAMS (I'M 7% SURE!!!!!!!!!!!!!!!!!!!)

  #is the best place to put your personal-program's cache, configuration, log files, and tmp/temporary-files-folders-directories?
  mkdir -pv /run/$USER/$UID/everything/{cache,etc,log,tmp}

  #nope. Put your personal-program's configuration in /etc/*
  #      Put your tmp s*** in /tmp/*
  #      Put your cache in ~/.cache/program00001/ (38% sure)
  #      Put your log files in /var/log/* (38% sure)

HOW SHOULD I EDIT MY SCREENSHOT0001.jpg  ?

  krita || shotwell || darktable || gimp

#prevents CTRL+S freezing the tty/virtual-console (ie. until CTRL+Q is hit)
#see:   stty -a | egrep 'start|stop'
stty start undef
stty stop undef

export EDITOR=/usr/bin/vim
export TZ=America/Los_Angeles
export WINEPREFIX=/media/user/DEB_STUFF/dot-wine
apt='apt apt-cache apt-file apt-get apt-mark aptitude dpkg dpkg-query' 
dir_lorem_ipsum='/home/user/Documents/lorem-ipsum'
pictures='/home/user/Documents/STATIC-photos'
shield_mount_dir='/home/user/Documents/shield_mapper_mount'
url_urbandictionary='https://api.urbandictionary.com/v0/define?term'
url_wikipedia='https://en.wikipedia.org/wiki/Special:Search?search'
url_wiktionary='https://en.wiktionary.org/wiki'
wow='/media/user/DEB_STUFF/dot-wine/drive_c/Program Files (x86)/Battle.net'
www='/home/user/Documents/Github-repos/localhost-httpd/www'
youtube='/home/user/Documents/youtube-downloads'
export bash1='/home/user/Documents/bash.1.txt'
export git='/home/user/Documents/Github-repos'
export goodideas1='/home/user/Documents/good-ideas.1.txt'
export music='/home/user/Documents/mp3s'
export perl_cheatsheet='/home/user/Documents/Github-repos/debian-fresh1/perl-cheat-sheet.txt'
export php_cheatsheet='/home/user/Documents/Github-repos/debian-fresh2/php-cheat-sheet.txt'
export python_cheatsheet='/home/user/Documents/Github-repos/debian-fresh1/python-cheat-sheet.txt'
export screenshots='/home/user/Documents/screenshots'
export todo='/home/user/Documents/TODO/todo.txt'
export txt_lorem_ipsum='/home/user/Documents/lorem-ipsum.1.txt'
export weechat_logs='/home/user/.weechat/logs'
alias ..='cd ..'
alias bash++='/usr/bin/perl'
alias battery='echo acpitool -b'
alias bitchx='/usr/bin/weechat -a -p --plugins irc,logger,alias,exec' #no auto-connect to a server and no plug-ins automatically loaded
alias check-google='wget -O - https://www.google.com/'
alias check-network='bash -c "ip route; curl -IL http://nmcheck.gnome.org/check_network_status.txt"'
alias df='df --human-readable --print-type 2>/dev/null'
alias ffmpeg='/usr/bin/ffmpeg -hide_banner'
alias ffprobe='/usr/bin/ffprobe -hide_banner'
alias find-biggest-dirs='du -h | sort -h'
alias jpg='/usr/bin/jpegoptim -s'
alias lightdm-stop='is_desktop_running && sudo systemctl stop lightdm'
alias lightdm-start='is_desktop_running || sudo systemctl start lightdm'
alias myip='bash -c "wget --quiet -O - -4 ifconfig.io; wget --quiet -O - -6 ifconfig.io"'
alias png='/usr/bin/optipng'
alias suspend='echo systemctl suspend'
alias tree='/usr/bin/tree -A'
alias tty-exec-desktop-program='echo "DISPLAY=:0.0 epiphany http://people.oregonstate.edu"'
alias tty-silence-restore='sudo dmesg -n 8'
alias tty-silence='sudo dmesg -n 1'
alias udisks='/usr/bin/udisksctl'
alias vi='/usr/bin/vim'
alias vim2='/usr/bin/vim "+set autoread readonly"'
alias weechat='echo bitchx'
funny-GUI-alert(){
  local messages=() message=''
  while :; do
    wait_this_long="$(( RANDOM % 10 + 1 ))m"
    echo sleep "$wait_this_long"
    sleep "$wait_this_long"
    message="$(printf '%s' $'hey, fuck you\they, fuck you!\tGET OUT\tgoto PRISON\tgoto HOMELESS SHELTER\tFEED GRASS' | tr '\t' '\n' | shuf --head-count=1)"
    DISPLAY=:0.0 xterm -T sup -e sh -c "echo '$message'; sleep 9.39"
  done
}
utfcode2all-character-info(){
  [ -n "$1" ] || return 33
  [[ "$1" =~ ^[0-9]+$ ]] || return 44
  lynx -force_html <(wget -O - https://www.compart.com/en/unicode/U+"$1")
}
utfcode2htmlentities-possibilities(){
  [ -n "$1" ] || return 33
  [[ "$1" =~ ^[0-9]+$ ]] || return 44
  wget -O - https://www.compart.com/en/unicode/U+"$1" | grep -Eo '&amp;[^;]+;' | sort | uniq | sed 's/&amp;/\&/'
}
py(){
  [ -z "$1" ] && return 99
  [ -f "$1" ] && return 100
  [[ "$1" =~ \.py$ ]] || return 101
  cat <<EOFRR2 > "$1"
#!/usr/bin/python3

if __name__ == '__main__':

EOFRR2
  [ -f "$1" ] || return 102
  chmod +x "$1"
}
cpu-temp(){
  echo CPU temperature is `c2f $(sensors | grep -E ^CPU | grep -Eo [0-9]+\.[0-9]+)` Fahrenheit
}
urban-example(){
  cat <<EOFFFFFUUUUU
wget -O - \$url_urbandicationary=mouter | jq -C | less -R
wget -O - \$url_urbandictionary=boomer | jq --monochrome-output '.list[]["definition","example"]'
EOFFFFFUUUUU
}
wiki-example(){
  echo 'links2 $url_wiktionary/stu'
  echo 'links2 $url_wikipedia=thunderbolt+usb'
}
mp3_add(){
  if is_desktop_running; then
    DISPLAY=:0.0 mousepad '/home/user/Documents/youtube-downloads/download.sh' &
  else
    return 9
  fi
}
bashrc-alias-list-function-list-print(){
  if [ "$1" = '-2' ]; then
    local out=$(alias -p)
    out+="\n$(compgen -A function | egrep -v ^_)"
    while read line1; do printf '%80s' "$line1       "; read line2; printf '%s\n' "$line2"; done <<<"$out"
    return 0
  fi

  alias -p; compgen -A function | egrep -v ^_
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
dice(){ ack "https?://.*$*" $weechat_logs | tail -1 | perl -ne 'BEGIN{undef $/;}if(m#(https?://[a-zA-Z0-9,%\#=/.:?_+&\-]+)#){print $1;}'; }

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
vol-is-good(){
  local current_volume=$(vol-get-current-float --percent-integer)
  local VOLUME_THRESHOLD_FOR_MUSIC=26
  [ -z "$current_volume" ] && return 3
  [[ "$current_volume" =~ [0-9]{1,3} ]] || return 4
  [ "$current_volume" -gt "$VOLUME_THRESHOLD_FOR_MUSIC" ] && {
    printf 'PANIC: Current alsa/pulseaudio volume settings (ie. %d) are 0.2%% good.\nVolume threshold of %d is too high for music/mp3s/ogg/whatever\n' "$current_volume" "$VOLUME_THRESHOLD_FOR_MUSIC"
    echo '`vol-low-set`'
    local myv
    read -n 1 -p '? (Y/n) ' myv
    echo

    case "$myv" in
      [Yy]|'') vol-low-set; return 0;;
    esac
    return 200
  } 
  return 0  
}
vol-get-current-float(){
  local volume_float=$(amixer get Master | perl -ne 'BEGIN{undef $/;} /Limits: Playback 0 - ([0-9]+).*Front Right: Playback ([0-9]+)/s && printf "%0.12f", $2 / $1;')
  [ -z "$volume_float" ] && return 120
  [ "$1" = '--percent-integer' ] && { printf '%0.0f' $(printf '%s * 100\n' "$volume_float" | bc); return 0; }
  echo "$volume_float"
  return 0
}
vol-low-set(){
  echo pactl set-sink-volume alsa_output.pci-0000_00_1f.3.analog-stereo 20%
  pactl set-sink-volume alsa_output.pci-0000_00_1f.3.analog-stereo 20%
  return 0
}
vol-up(){
  echo pactl set-sink-volume alsa_output.pci-0000_00_1f.3.analog-stereo +5%
  pactl set-sink-volume alsa_output.pci-0000_00_1f.3.analog-stereo +5%
  while :; do
    read -p 'more? '
    echo pactl set-sink-volume alsa_output.pci-0000_00_1f.3.analog-stereo +5%
    pactl set-sink-volume alsa_output.pci-0000_00_1f.3.analog-stereo +5%
  done
  return 0
}
vol-down(){
  echo pactl set-sink-volume alsa_output.pci-0000_00_1f.3.analog-stereo -5%
  pactl set-sink-volume alsa_output.pci-0000_00_1f.3.analog-stereo -5%
  while :; do
    read -p 'more? '
    echo pactl set-sink-volume alsa_output.pci-0000_00_1f.3.analog-stereo -5%
    pactl set-sink-volume alsa_output.pci-0000_00_1f.3.analog-stereo -5%
  done
  return 0
}
xclip-cp(){
  [ -z "$1" ] && return 4
  [ -f "$1" ] || return 5
  DISPLAY=:0.0 xclip -selection clipboard "$1"
  return 0
}
watch-weechat-log-user-update-return-when-changed(){
  [ -z "$1" ] && return 41
  touch "$weechat_logs/$1"
  while [ ! -f "$weechat_logs/irc.libera.$1.weechatlog" -o "$weechat_logs/irc.libera.$1.weechatlog" -ot "$weechat_logs/$1" ]; do
    sleep 31.2;
  done
}
watch-weechat-log-keyword-return-when-changed(){
  [ -z "$1" ] && return 41
  current_wc_l="$(ack "$*" "$weechat_logs" | wc -l)"
  while [ "$(ack "$*" "$weechat_logs" | wc -l)" -eq "$current_wc_l" ]; do
    sleep 12.2
  done
  return 0
}
watch-port80-ipv4(){
  while [ 1 ]; do sudo nc -l -p 80 >> ~/whatever.log; sleep 0.2; done
  return 0
}
watch-port80-ipv6(){
  while [ 1 ]; do sudo nc -l -6 -p 80 >> ~/whatever.log; sleep 0.2; done
  return 0
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
    vol-is-good || return 44
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
  for i in $(echo *.mp3 | tr ' ' '\n' | shuf | tr '\n' ' '); do vol-is-good || return 44; ffplay $i; read -n 1 -p 'delete? (y/N) ' yn; [[ $yn = 'y' ]] && { echo; rm -v $i; echo; } ;  done
  return 0
}
alert-me(){
  [ -z "$1" ] && return 3
  wall "$*"
  is_desktop_running && {
    DISPLAY=:0.0 zenity --title="$*" --info --text="$*" --no-wrap
  }
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
check-wifi-hardware-logs(){
  local i=''
  for i in NetworkManager.service systemd-networkd.service; do sudo journalctl --unit=$i | tail -40; sleep 10; done
}
Copyright Notices

  Copyright (C) 1997 Fvzba Gngunz and Whyvna Unyy
  Copyright (C) 2000 by Naqerj Mnobybgal
  Copyright (C) 1990-1997 FpvGrpu Fbsgjner, Inc.
  Copyright 1995-2022 The ANFZ Authors
  Copyright 1999-2002, B'Ervyyl & Associates
  Copyright (c) 1994,98 Whyvna Unyy. All rights reserved.
  Copyright (c) 2001 ERG & PBZ Research.
  Copyright (C) 1999-2002 by QbbZ Yrtnpl Team.
  Copyright (C) 1997 Wbua F. Svar

  ANFZ is now licensed under the GNU GPLv2 or greater license.

  Copyright 1995-2022 the ANFZ Authors - All rights reserved.

  Redistribution and use in source and binary forms, with or without
  modification, are permitted provided that the following
  conditions are met:

  * Redistributions of source code must retain the above copyright
    notice, this list of conditions and the following disclaimer.
  * Redistributions in binary form must reproduce the above
    copyright notice, this list of conditions and the following
    disclaimer in the documentation and/or other materials provided
    with the distribution.

  THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND
  CONTRIBUTORS "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES,
  INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF
  MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE
  DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT OWNER OR
  CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL,
  SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT
  NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES;
  LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION)
  HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN
  CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR
  OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE,
  EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.

The Yvahk packaging is copyright (C) 1995-2000 Ivaprag Eraneqvnf, (C)
1998-2003 Zngrw Iryn, (C) 2002 Vib Gvzzreznaf, (C) 2004 Puevfgvna
Xrffryurvz, (C) 2022 Navony Zbafnyir Fnynmne and also released under
the terms of the GNU General Public License; version 2, or any later
version.

On the Internet, the complete text of the GNU General Public License
can be found by Googling 'GPLv2 or greater.'


#
# __COPYRIGHT__
#
# Permission is hereby granted, free of charge, to any person obtaining
# a copy of this software and associated documentation files (the
# "Software"), to deal in the Software without restriction, including
# without limitation the rights to use, copy, modify, merge, publish,
# distribute, sublicense, and/or sell copies of the Software, and to
# permit persons to whom the Software is furnished to do so, subject to
# the following conditions:
#
# The above copyright notice and this permission notice shall be included
# in all copies or substantial portions of the Software.
#
# THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY
# KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE
# WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND
# NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE
# LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION
# OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION
# WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
#
1.6M	dash-0.5.11+git20200708+dd9ef66/
39M	bash-5.1/
109M	perl-5.32.1/

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
Native game(s):
  Teeworlds (`teeworlds` 0.7.5-1 aka vanilla teeworlds client)
Steam games:
  Left 4 Dead 2
  Saints Row 2 (actually runs native!)
  Team Fortress 2 (FREE)
  Torchlight 2
Wine games:
  World of Warcraft (through Battle.net app)
  Diablo II (ie. LoD version 1.14d)
#!/bin/bash

[ -f ~/README.md ] && exit 2

echo 'cat *.txt > README.md'
cat *.txt > README.md
perl -i -pe 'BEGIN{undef $/;}s/^/\`\`\`/;s/$/\`\`\`/' README.md



GOOD IDEA #001: BEST CLI WEBSITE-CREATOR/WEBSITE-GENERATOR EVER

  The priority was to create a website with CLI in the fastest/easiest/most-flexible way possible within the shortest time period possible..
  create-website.sh --title='My First Website' --pages=3 --color-scheme=random '--menu=Home,About Me,External Links' --favicon=random --theme-preset=random ~/WebProjects/Website005
  # and the output website would work on Android phones, Firefox, and Chromium-based browsers at least.


GOOD IDEA #002: TEXT FILE (BUT ALSO STDIN I GUESS TOO HEHE) ANALYSIS/STATISTICS OUTPUT

  Accepts STDIN (write all to tempfile && process) by default or $ARGV[1] as input file.
  $ascii_character_count 
  $average_line_length                            #read line-by-line?
  $blank_line_count                               #/^\s*$/m
  $file_is_empty                                  #1 or 0
  $file_is_text || `strings $filename | wc -c`    #Perl 5 -T directive
  $file_looks_like                                #ASCII or UTF-8 or ?? (`file`)
  $\n_count
  $nonascii_character_count 
  $nonblank_line_count                            #$total_lines - $blank_line_count
  $[:non-printable:]_count 
  $[:printable:]_count
  $\r_count
  $\s_count
  $\t_count
sudo --preserve-env hw-probe -all -show
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

echo *-debs essential-1of2-debs/* essential-2of2-debs/* | tr ' ' '\n'



apt-container-debs
atool-debs
audacity-debs
cloc-debs
epiphany-browser-debs
essential-1of2-debs
essential-2of2-debs
exiftool-debs
ffmpeg-libdvdcss2-mpv-vlc-debs
git-debs
gmtp_android-usb-debs
go-debs
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
teeworlds-debs
vim-debs
w3c-css-validator-debs
weechat-debs
wine-debs
xchm-debs
essential-1of2-debs/base-files_11.1+deb11u4_amd64.deb
essential-1of2-debs/bash_5.1-2+deb11u1_amd64.deb
essential-1of2-debs/dirmngr_2.2.27-2+deb11u2_amd64.deb
essential-1of2-debs/distro-info-data_0.51+deb11u2_all.deb
essential-1of2-debs/dpkg_1.20.11_amd64.deb
essential-1of2-debs/dpkg-dev_1.20.11_all.deb
essential-1of2-debs/exo-utils_4.16.0-1+deb11u1_amd64.deb
essential-1of2-debs/gnupg_2.2.27-2+deb11u2_all.deb
essential-1of2-debs/gnupg-l10n_2.2.27-2+deb11u2_all.deb
essential-1of2-debs/gnupg-utils_2.2.27-2+deb11u2_amd64.deb
essential-1of2-debs/gpg_2.2.27-2+deb11u2_amd64.deb
essential-1of2-debs/gpg-agent_2.2.27-2+deb11u2_amd64.deb
essential-1of2-debs/gpgconf_2.2.27-2+deb11u2_amd64.deb
essential-1of2-debs/gpgsm_2.2.27-2+deb11u2_amd64.deb
essential-1of2-debs/gpgv_2.2.27-2+deb11u2_amd64.deb
essential-1of2-debs/gpg-wks-client_2.2.27-2+deb11u2_amd64.deb
essential-1of2-debs/gpg-wks-server_2.2.27-2+deb11u2_amd64.deb
essential-1of2-debs/gzip_1.10-4+deb11u1_amd64.deb
essential-1of2-debs/libavcodec58_7%3a4.3.4-0+deb11u1_amd64.deb
essential-1of2-debs/libavfilter7_7%3a4.3.4-0+deb11u1_amd64.deb
essential-1of2-debs/libavformat58_7%3a4.3.4-0+deb11u1_amd64.deb
essential-1of2-debs/libavresample4_7%3a4.3.4-0+deb11u1_amd64.deb
essential-1of2-debs/libavutil56_7%3a4.3.4-0+deb11u1_amd64.deb
essential-1of2-debs/libdpkg-perl_1.20.11_all.deb
essential-1of2-debs/libexo-2-0_4.16.0-1+deb11u1_amd64.deb
essential-1of2-debs/libexo-common_4.16.0-1+deb11u1_all.deb
essential-1of2-debs/libfreetype6_2.10.4+dfsg-1+deb11u1_amd64.deb
essential-1of2-debs/libfribidi0_1.0.8-2+deb11u1_amd64.deb
essential-1of2-debs/libgnutls30_3.7.1-5+deb11u1_amd64.deb
essential-1of2-debs/libgnutls-dane0_3.7.1-5+deb11u1_amd64.deb
essential-1of2-debs/libjavascriptcoregtk-4.0-18_2.36.3-1~deb11u1_amd64.deb
essential-1of2-debs/libldap-2.4-2_2.4.57+dfsg-3+deb11u1_amd64.deb
essential-1of2-debs/libldap-common_2.4.57+dfsg-3+deb11u1_all.deb
essential-1of2-debs/liblzma5_5.2.5-2.1~deb11u1_amd64.deb
essential-1of2-debs/libnm0_1.30.6-1+deb11u1_amd64.deb
essential-1of2-debs/libntfs-3g883_1%3a2017.3.23AR.3-4+deb11u2_amd64.deb
essential-1of2-debs/libpostproc55_7%3a4.3.4-0+deb11u1_amd64.deb
essential-1of2-debs/libsdl2-2.0-0_2.0.14+dfsg2-3+deb11u1_amd64.deb
essential-1of2-debs/libsmbclient_2%3a4.13.13+dfsg-1~deb11u4_amd64.deb
essential-1of2-debs/libssl1.1_1.1.1n-0+deb11u3_amd64.deb
essential-1of2-debs/libswresample3_7%3a4.3.4-0+deb11u1_amd64.deb
essential-1of2-debs/libswscale5_7%3a4.3.4-0+deb11u1_amd64.deb
essential-1of2-debs/libtiff5_4.2.0-1+deb11u1_amd64.deb
essential-1of2-debs/libvlc5_3.0.17.4-0+deb11u1_amd64.deb
essential-1of2-debs/libvlc-bin_3.0.17.4-0+deb11u1_amd64.deb
essential-1of2-debs/libvlccore9_3.0.17.4-0+deb11u1_amd64.deb
essential-1of2-debs/libwbclient0_2%3a4.13.13+dfsg-1~deb11u4_amd64.deb
essential-1of2-debs/libwebkit2gtk-4.0-37_2.36.3-1~deb11u1_amd64.deb
essential-1of2-debs/libxml2_2.9.10+dfsg-6.7+deb11u2_amd64.deb
essential-1of2-debs/linux-kbuild-5.10_5.10.127-1_amd64.deb
essential-1of2-debs/logrotate_3.18.0-2+deb11u1_amd64.deb
essential-1of2-debs/nano_5.4-2+deb11u1_amd64.deb
essential-1of2-debs/network-manager_1.30.6-1+deb11u1_amd64.deb
essential-1of2-debs/ntfs-3g_1%3a2017.3.23AR.3-4+deb11u2_amd64.deb
essential-1of2-debs/openssh-client_1%3a8.4p1-5+deb11u1_amd64.deb
essential-1of2-debs/openssl_1.1.1n-0+deb11u3_amd64.deb
essential-1of2-debs/orca_3.38.2-2_all.deb
essential-1of2-debs/rsyslog_8.2102.0-2+deb11u1_amd64.deb
essential-1of2-debs/samba-libs_2%3a4.13.13+dfsg-1~deb11u4_amd64.deb
essential-1of2-debs/tcpdump_4.99.0-2+deb11u1_amd64.deb
essential-1of2-debs/tzdata_2021a-1+deb11u4_all.deb
essential-1of2-debs/usb.ids_2022.05.20-0+deb11u1_all.deb
essential-1of2-debs/vlc-data_3.0.17.4-0+deb11u1_all.deb
essential-1of2-debs/vlc-plugin-base_3.0.17.4-0+deb11u1_amd64.deb
essential-1of2-debs/vlc-plugin-video-output_3.0.17.4-0+deb11u1_amd64.deb
essential-1of2-debs/wireless-regdb_2022.04.08-2~deb11u1_all.deb
essential-1of2-debs/xz-utils_5.2.5-2.1~deb11u1_amd64.deb
essential-1of2-debs/zlib1g_1%3a1.2.11.dfsg-2+deb11u1_amd64.deb
essential-2of2-debs/ack_3.4.0-1_all.deb
essential-2of2-debs/acpitool_0.5.1-6_amd64.deb
essential-2of2-debs/apt-show-versions_0.22.12_all.deb
essential-2of2-debs/bind9-dnsutils_1%3a9.16.27-1~deb11u1_amd64.deb
essential-2of2-debs/dillo_3.0.5-7_amd64.deb
essential-2of2-debs/doc-rfc-experimental_20201128-1_all.deb
essential-2of2-debs/gallery-dl_1.18.0-1_all.deb
essential-2of2-debs/gawk_1%3a5.1.0-1_amd64.deb
essential-2of2-debs/gcc-10-doc_10.2.0-1_all.deb
essential-2of2-debs/gcc-doc_5%3a10.1.0-1_amd64.deb
essential-2of2-debs/gcc-doc-base_10.1.0-1_all.deb
essential-2of2-debs/glibc-doc-reference_2.31-1_all.deb
essential-2of2-debs/gparted_1.2.0-1_amd64.deb
essential-2of2-debs/gparted-common_1.2.0-1_all.deb
essential-2of2-debs/info_6.7.0.dfsg.2-6_amd64.deb
essential-2of2-debs/inotify-tools_3.14-8.1_amd64.deb
essential-2of2-debs/install-info_6.7.0.dfsg.2-6_amd64.deb
essential-2of2-debs/intel2gas_1.3.3-17+b1_amd64.deb
essential-2of2-debs/iotop_0.6-24-g733f3f8-1.1_amd64.deb
essential-2of2-debs/jnettop_0.13.0-1.1_amd64.deb
essential-2of2-debs/jpegoptim_1.4.6-1_amd64.deb
essential-2of2-debs/jq_1.6-2.1_amd64.deb
essential-2of2-debs/libevent-core-2.1-7_2.1.12-stable-1_amd64.deb
essential-2of2-debs/libfile-next-perl_1.18-1_all.deb
essential-2of2-debs/libfltk1.3_1.3.5-3_amd64.deb
essential-2of2-debs/libinotifytools0_3.14-8.1_amd64.deb
essential-2of2-debs/libjq1_1.6-2.1_amd64.deb
essential-2of2-debs/libjs-underscore_1.9.1~dfsg-3_all.deb
essential-2of2-debs/libonig5_6.9.6-1.1_amd64.deb
essential-2of2-debs/libsqlite3-mod-impexp_0.9998-2_amd64.deb
essential-2of2-debs/make-doc_4.3-2_all.deb
essential-2of2-debs/manpages-posix_2017a-2_all.deb
essential-2of2-debs/nasm_2.15.05-1_amd64.deb
essential-2of2-debs/ncal_12.1.7+nmu3_amd64.deb
essential-2of2-debs/netcat_1.10-46_all.deb
essential-2of2-debs/netcat-openbsd_1.217-3_amd64.deb
essential-2of2-debs/netselect_0.3.ds1-29_amd64.deb
essential-2of2-debs/netselect-apt_0.3.ds1-29_all.deb
essential-2of2-debs/netsurf-common_3.10-1_all.deb
essential-2of2-debs/netsurf-gtk_3.10-1+b1_amd64.deb
essential-2of2-debs/net-tools_1.60+git20181103.0eebece-1_amd64.deb
essential-2of2-debs/optipng_0.7.7-1+b1_amd64.deb
essential-2of2-debs/parallel_20161222-1.1_all.deb
essential-2of2-debs/perl-doc_5.32.1-4+deb11u2_all.deb
essential-2of2-debs/pinfo_0.6.13-1.1_amd64.deb
essential-2of2-debs/python3-astral_1.6.1-2_all.deb
essential-2of2-debs/python3-tz_2021.1-1_all.deb
essential-2of2-debs/sqlite3_3.34.1-3_amd64.deb
essential-2of2-debs/sqlite3-doc_3.34.1-3_all.deb
essential-2of2-debs/strace_5.10-1_amd64.deb
essential-2of2-debs/tcpdump_4.99.0-2_amd64.deb
essential-2of2-debs/tmux_3.3a-1_bpo11+1_amd64.deb
essential-2of2-debs/toilet_0.3-1.3_amd64.deb
essential-2of2-debs/toilet-fonts_0.3-1.3_all.deb
essential-2of2-debs/tree_1.8.0-1+b1_amd64.deb
essential-2of2-debs/weechat-doc_3.0-1+deb11u1_all.deb
essential-2of2-debs/wget_1.21-1+deb11u1_amd64.deb
essential-2of2-debs/whois_5.5.10_amd64.deb
essential-2of2-debs/xclip_0.13-2_amd64.deb
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

BASH TERMINAL-TTY HOW DO I EASILY REPLACE OR APPEND TYPED-UP TEXT INTO MY TEXT FILE?????

  #I guess this: TODO: I need more actual examples.
  tee whatever.txt >/dev/null <<EOF
line 1
line 2
line 3
EOF
  tee -a whatever.txt >/dev/null <<EOF
line 4
line 5
line 6
EOF

BASH DECLARE BLAAAAAAAAAhhhhhhh

	declare -p varp #equivalent to: echo -E, printf %q, TODO: tryme
	declare -- varp="1 2 3
4 5 6"
	read -d '' varp << 'EOFREWQ'
1 2 3
4 5 6
EOFREWQ

APPLE USES zsh AS ITS TERMINAL SHELL

	surprise!!!! within the last 5 years.

C COMPILER (.c -> .s 90% sure) ASSEMBLER (.s -> .o) OPTIMIZATION RANDOM RESEARCH FOR generic-x86 ARCHITECTURE OR TARGET TUNING

    clang optimization IR?????
    is IR optimization for the clang --compiler-target=generic-x86
    clang --target-architecture --target-tune #might provide IR optimization
    s/generic-x86/haswell|sandybridge/ #for clang, TODO: 0% portable?

*sigh* BASHRC FUNCTION TO CONVERT A STRING INPUT-ARGUMENT INTO MATCHED HTTP URL ADDRESSES

  lucky(){ s="$*"; [ -z "$s" ] && return 3; printf '%s' "$s" | perl -ne 'BEGIN{undef $/;}if(m#(https?://[a-zA-Z0-9,%\#=/.:?_+&\-]+)#){print $1;}'; }

SIMPLIFIED bash.1.txt RECORDING SCREENSHOTS TO A VIDEO USING IMAGEMAGICK

  sleep 8
  n=1
  while [ $n -lt 10 ]; do
    import -window root $(printf '%03d' $n).png
    sleep 0.3
  done
  convert -delay 41 *.png -resize 43% animation.gif

C COMPILING

  c preprocessor -> compiler -> c preprocessor -> assembler -> linker
  .s -> assembler -> .o -> linker -> executable

  sometimes:      .c, .s, .o
  sometimes:      .S, .s, .o
  sometimes:      .c, .S
  TODO: wtf?

  compiler:   .c into .s
  assembler:  .s into .o
  linker:     .o into .exe

PYTHON3 ALLAH MICKBAR
  #whatever

  [tuple(map(int, x.split(':'))) for x in l]
  ["500:250","3600:42"]`       `[(500, 250), (3600, 42)]
  sum(3 if a > b else (1 if a == b else 0) for a, b in map(lambda x: map(int, x.split(':')), l))
  sum(2*((a-b)>0)+1 for a,b in map(lambda x:map(int,x.split(':')),l) if a>=b)
  sum(2*((a-b)>0)+1-(a<b) for a,b in map(lambda x:map(int,x.split(':')),l))`
  re.sub(r'\$(matchThis)', lambda m: m.group(1).upper(), 'something 1 2 3 $var1')

SOME GOOD HTML ENTITIES RESEARCH

  &cent;    <!-- US cent sign -->
  &deg;     <!-- temperature degrees sign -->
  &hellip;  <!-- a 2% more condensed '...' -->
  &ne;      <!-- not-equal, !=, sign -->
  &ge;      <!-- >= in one character -->
  &le;      <!-- <= in one character -->
  &brvbar;  <!-- | with a space in the middle -->
  &Theta;   <!-- O with a line through the middle -->
  &Pi;      <!-- a tall math.PI symbol -->
  &OElig;   <!-- a capital C condensed with a capital E -->
  &egrave;  <!-- yet another engraved lower-case e -->
  &sub;     <!-- the @set to the left is LESS-THAN/WITHIN @super_set to the right -->


RECORD YOUR BASH SESSION COMMAND-SESSION

  script #file ``typescript'' gets created in PWD until you CTRL+D or whatever

RFC2822 compliant date string for current date

  date -R
  #Tue, 12 Jul 2022 16:16:32 -0700

BASH STDIN ACCEPTING COMMANDS EXTRAS

  # FILE contents As-STDIN 2 BASH command STDIN
  <"$filename_input" $command
  
  #for example:
  </etc/hosts pager


USING/OPENING STREAMS/SPECIAL-FILE-DESCRIPTORS TO ATTACH/USE IN YOUR BASH PROCESS 

  echo testString >~/dlt
  #open special file descriptors for this bash process
  exec 9<~/dlt 4>~/from_dlt    
  #accept file descriptor 9 as STDIN and output file descriptor 4 as STDOUT
  cat <&9 >&4                  
  #close file-descriptor 4 as an OUTPUT and close file-descriptor 9 as an INPUT stream/file
  exec 4>&- 9<&-              
  head ~/from_dlt
  #testString
  shred --verbose -u ~/dlt ~/from_dlt

DELETE ALL CARRIAGE RETURN CHARACTERS IN BASH FROM STDOUT OF A PREVIOUS BASH COMMAND EXECUTION

  | tr --delete '\r'

BASH BULL****

  man col
  man cut
  man xargs
  man bash | grep -iF --color=always expansion | less -R
  man bash | grep -iF --color=always substitution | less -R
  man bash | grep -iE -A 2 --color=always '-e|-w|-x|-nt|-ot' | less -R
  man bash | grep -iE -A 2 --color=always '-eq|-ge|-gt|-lt|-le|-ne' | less -R

  #BASH metacharacters: ) < ; > & ( | 
  #BASH whitespace for the for loop: '\n', ' '  (98% correct)

BASH DETECT HARD LINKS

  [ "$file1" -ef "$file2" ] && echo hardlinked

BASH WHATEVER NAME SORT ALGORITHM COMPARISON FOR STRINGS

  [[ a < b ]] && echo yes a is before b

BASH TODO

  #what if I go like this:
  true && true && true || true || true && true && echo hello world

PID OF CURRENT BASH/SHELL PROCESS

  echo $$ #for example: 1229301

PID OF BACKGROUND JOB PROCESS

  sleep 8& echo $! #for example: 1229320 

GREAT MORE SH** COMMANDS

  apt -s install bsdmainutils
  apt -s install moreutils
  apt -s install whiptail #`newt` UI dialog boxes

TRANSLATION RESEARCH

  apt -s install gettext
sudo lsblk --list --output-all /dev/sda | tr '\t' ' ' | sed 's/ \{2,\}/ /g'


NAME KNAME PATH MAJ:MIN FSAVAIL FSSIZE FSTYPE FSUSED FSUSE% FSVER MOUNTPOINT LABEL UUID PTUUID PTTYPE PARTTYPE PARTTYPENAME PARTLABEL PARTUUID PARTFLAGS RA RO RM HOTPLUG MODEL SERIAL SIZE STATE OWNER GROUP MODE ALIGNMENT MIN-IO OPT-IO PHY-SEC LOG-SEC ROTA SCHED RQ-SIZE TYPE DISC-ALN DISC-GRAN DISC-MAX DISC-ZERO WSAME WWN RAND PKNAME HCTL TRAN SUBSYSTEMS REV VENDOR ZONED DAX
sda sda /dev/sda 8:0 01ea0b1e-a254-44c5-b3df-061b4120bd39 gpt 128 0 0 0 ST1000LM035-1RK172 WL127AV7 931.5G running root disk brw-rw---- 0 4096 0 4096 512 1 mq-deadline 64 disk 0 0B 0B 0 0B 0x5000c500b919327a 1 0:0:0:0 sata block:scsi:pci SDM3 ATA none 0
sda1 sda1 /dev/sda1 8:1 76.6G 287.9G ext4 196.6G 68% 1.0 /media/user/DEB_STUFF DEB_STUFF 15753af0-daad-4cbd-bd97-0ac76cb6dbbe 01ea0b1e-a254-44c5-b3df-061b4120bd39 gpt 0fc63daf-8483-4772-8e79-3d69d8477de4 Linux filesystem DEB_STUFF b239907a-b449-4189-a365-e3cd8bd0ec03 128 0 0 0 293G root disk brw-rw---- 0 4096 0 4096 512 1 mq-deadline 64 part 0 0B 0B 0 0B 0x5000c500b919327a 1 sda block:scsi:pci none 0
sda2 sda2 /dev/sda2 8:2 swap 1 [SWAP] DEB_SWAP 87e82457-cfe9-427e-8d60-e8abc41f2fd4 01ea0b1e-a254-44c5-b3df-061b4120bd39 gpt 0657fd6d-a4ab-43c4-84e5-0933c84b4f4f Linux swap DEB_SWAP c1352856-794d-47ed-8cf9-819133a923c1 128 0 0 0 9.8G root disk brw-rw---- 0 4096 0 4096 512 1 mq-deadline 64 part 0 0B 0B 0 0B 0x5000c500b919327a 1 sda block:scsi:pci none 0
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
sudo apt-mark hold hddtemp libburn4 libisofs6 libjte2 libqrencode4 libxnvctrl0 linux-compiler-gcc-10-x86 linux-headers-amd64 linux-image-amd64 linux-libc-dev ristretto xfce4-battery-plugin xfce4-clipman xfce4-clipman-plugin xfce4-cpufreq-plugin xfce4-cpugraph-plugin xfce4-datetime-plugin xfce4-diskperf-plugin xfce4-fsguard-plugin xfce4-genmon-plugin xfce4-mailwatch-plugin xfce4-netload-plugin xfce4-places-plugin xfce4-screenshooter xfce4-sensors-plugin xfce4-smartbookmark-plugin xfce4-systemload-plugin xfce4-taskmanager xfce4-timer-plugin xfce4-verve-plugin xfce4-wavelan-plugin xfce4-weather-plugin xfce4-whiskermenu-plugin xfce4-xkb-plugin 
sudo apt purge $(dpkg -l | egrep -i 'mozi|fox' | awk '{print $2}' | tr '\n' ' ') epiphany-browser konqueror chromium
sudo apt purge goldendict exfalso parole quodlibet
sudo apt-mark manual bc
sudo apt purge cups-daemon cups cups-browsed cups-core-drivers #I'm 93% sure this is right
sudo apt purge hexchat hexchat-common hexchat-perl hexchat-plugins hexchat-python3
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
JAVASCRIPT COPY TEXT TO CLIPBOARD IN AN INPUT FIELD RESEARCH

  var inp = document.getElementById('txtUsername');
  if (inp && inp.select)
  {
    inp.select(); // select all text
    try {
      document.execCommand('copy');
      inp.blur(); // unfocus
    }
    catch(err){}
  }


JAVASCRIPT TIMEOUT QUICKIE

  setTimeout(function() { alert('I am 1 minute in the future!'; }, 60 * 60);

JAVASCRIPT HTML5 DOM ACCESS ANY INPUT FIELD WITHIN ANY FORM WITHIN YOUR HTML5 DOCUMENT RESEARCH
  
  document.form.txtAddress.value = '';

PHP USE WARNINGS;
  
  // I'm 70% sure this is always enabled
  error_reporting(E_ALL);
  ini_set('display_errors', 1);

VIEW HTML FILES GENERATED FROM PHP PAGES QUICKLY AS POSSIBLE HOWEVER I THINK LYNX IS THE ONLY ONE...................

  lynx -force_html <(php some-test-php.php)

PROPER PHP EOL (INSTEAD OF JUST \n, WHICH WORKS 0 WELL IF YOU WANT DOS-NEWLINES FOR NOTEPAD.EXE ON WINDOWS 10 HEHE)
  AND PREG_REPLACE LIKE WITH PERL YOU CAN USE COOL REGEX SYMBOLS TO REPRESENT REGEX EXPRESSION BOUNDARIES
  AND CONVERT A UNICODE UTF-16 CODE-NAME U+2015 INTO A ACTUAL GOOD HTMLENTITIES VALID HTML-CHARACTER-OUTPUT/HTML-ENTITY EXPRESSION (ie. &#x2015;)

  <?php
    $s = 'hello\u2015bye';
    $s = preg_replace("/\\\u([0-9a-z]{3,4})/", "&#x$1;", $s);
    $s = preg_replace("@\\\u([0-9a-z]{3,4})@", "&#x$1;", $s); #Same thing as the first one
    echo $s, PHP_EOL;
  ?>




WHEN TO USE EM-DASH (longer) VERSUS EN-DASH (shorter) HTML-ENTITIES IN YOUR HTML CODE

  <pre>
  &ndash;   Separate phone number numbers (90% sure regular '-' would work here), extrapolating list of something major (eg. Linux programming&ndash; Bash, Perl 5, Python3.)
  &mdash;   ${date_whatever}&mdash;\s?Present/${date_whatever+whatever}
  </pre>

ALL OF THESE HTML ENTITIES ARE THE SAME THING

  &#8213; &#x2015; &horbar; (horizontal bar UTF-16 character 3.1x longer than '-')

HYPHEN REFERENCE

  <div style="white-space:pre-line">
  regular dash: -
  (a little longer)
  en dash: &ndash; (1.3x longer than regular dash)
  (much longer)
  em dash: &mdash; (2.1x longer than en-dash. 3.1x longer than regular dash)
  (a little longer)
  horizontal bar: &horbar; (7% longer than em-dash)
  regular hyphen: --
  </div>


SOME GOOD HTML ENTITIES RESEARCH

  &aelig; <!-- ae in one character -->
  &AElig; <!-- AE in one character -->
  &horbar;
  &middot; &bull; <!-- bull is for bullet, which is a x2 thicker middot -->
  &uarr; &darr; &larr; &rarr;

JAVASCRIPT INSTANTIATE MORE THAN ONE VARIABLE IN ONE STATEMENT

  var a=1, b=2, c=a+2;
  var d=document,a='setAttribute',s=d.createElement('script');

JAVASCRIPT DIFFERENCE BETWEEN undefined AND null

  'undefined' is longer
  undefined !== null
  undefined == null
  that's it.

JAVASCRIPT: A FUNCTION IN AN HREF LINK AND CALL IT

  <a href="javascript:void(function(){alert('sup');})();">say sup</a>

DYNAMICALLY LOAD IN A .JS INTO YOUR "DOM"

  var d=document;
  var tmp='setAttribute';
  var tag=d.createElement('script');
  tag[tmp]('type','text/javascript');
  tag[tmp]('src','/effect.js');
  tmp = undefined;
  d.head.appendChild(tag);
  tag = undefined;

HTML ENTITIES NOTES-RESEARCH

  &#34; to &#9000;
  space is &#160;

w3c-linkchecker w3c-markup-validator #requires apache2, ugh.
translate-shell #google-translate-cli

0 firefox
0 perl5
0 python3
8 golang-1.15
9 php-cli
17 rustc 
26 default-jdk
176 mono-mcs


Gaming phrases:
ахаххахаха = Ahahhaha
дa = yes
еспи  = if
иэтчи = lachi           #ie. an asshole
ку    = 13%cool/12%pussy-shit = cUUkoo #(2)ie. the sound of a cuckoo, "having -3% good for 0.2% good"
мэтч  = match
мэтчи = matches
ПИЗДА = 96%PUSSY = being seriously damaged  #I'm 2% sure this translation is right.
пиэда = 12%pedophile = 2%cunt    #(2) I'm 3% sure this translation is right. 
твoя  = TV = your             #(1) I'm 0.2%% sure it means "abusing the TV" (2) female form. (I'm 3% sure this translation is right)
че еспи = that espa     #ie. that eat-shit-please-asshole
Что если = what if
ээ    = oops = I am collecting my thoughts give me a few seconds = I am detecting blah blah blah blah




ёЁ!"№;%:?*()_-+=хХъЪ\/жЖэЭбБюЮ.,/*-+,,,,++--**--*///

йцукенгшщзфывапролдячсмить

ЙЦУКЕНГШЩЗФЫВАПРОЛДЯЧСМИТЬ

OK!


#Alright you guys. How close am I to reality [with this code]? (apologies to those that are in a tty[?]/non-unicode-terminal)
иф( $ин eq 'фр' ){ print("bonjour\n"); }


Привет, мир #Hello, world

еспи is 'if' is %D0%B5%D1%81%D0%BF%D0%B8

Путеводитель-для-новичков is
%D0%9F%D1%83%D1%82%D0%B5%D0%B2%D0%BE%D0%B4%D0%B8%D1%82%D0%B5%D0%BB%D1%8C-%D0%B4%D0%BB%D1%8F-%D0%BD%D0%BE%D0%B2%D0%B8%D1%87%D0%BA%D0%BE%D0%B2


#My very first Russian-language test
РФРФРФРФРФРФРФРФ нуфр ифин нуфр
POPOPOPOPOPOPOPO hyop nonh hyop #my alphabet-translation
RFRFRFRFRFRFRFRF nufr ifin nufr #using a special alphabet-translation
HAHAHAHAHAHAHAHA yeah baby yeah #what I typed in with the same keys

#valid perl!
use utf8; my  $ин="Привет, мир!"; print $ин

#convert if/eq into Russian-language equivalent in your code
epiphany https://metacpan.org/pod/Keyword::Declare
epiphany https://metacpan.org/dist/XS-Parse-Keyword
epiphany https://metacpan.org/pod/Syntax::Operator::Equ
Congratulations python, you're the leader in malware sh**:    9:deb, 218:pm, 1073:perl, 2238:php, 2809:bash, 4860:html, 5694:C++, 12158:C, 13332:go, 28078:java, 39536:py


unbind C-b
set -g prefix C-Space
bind C-Space send-prefix

set -g status "off"
set -g pane-border-status "off"
set -g pane-border-format "hidden"
set -g pane-border-lines "number"
set -g pane-active-border-style "hidden"
set -g pane-border-style "hidden"



"TODO: how do I translate a source code file into HTML [highlighted]?

" :syntax on    to enable syntax highlighting supposidly (if not already enabled automatically/by-default :|)


"\t shows as 2 spaces
:set tabstop=2

"how many spaces you should move when you move visually selected text with >>
:set shiftwidth=2

"your typed-in <tab> is now "expanded" as $tabstop spaces 30% sure
:set expandtab

"I love the arrow keys to go up and down and next buffer and previous buffer
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

sudo systemctl restart networking NetworkManager wpa_supplicant systemd-modules-load```
