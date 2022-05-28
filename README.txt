#!/usr/bin/perl


use strict;
use warnings;
use Data::Dumper;
$Data::Dumper::Maxdepth = 1;
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



export TZ=America/Los_Angeles
export WINEPREFIX=/media/user/DEB_STUFF/dot-wine
wow='/media/user/DEB_STUFF/dot-wine/drive_c/Program Files (x86)/Battle.net'
alias ..='cd ..'
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
  if [[ $file_extension = 'mp3' || $file_extension = 'wav' || $file_extension = 'ogg' ]]; then
    /usr/bin/ffplay -nodisp -autoexit "$filename"
  else
    /usr/bin/ffplay "$filename"
  fi
}

clipart-png
clipart-png/tools
clipart-png/signs_and_symbols
clipart-png/office
clipart-png/education
clipart-png/plants
clipart-png/electronics
clipart-png/recreation
clipart-png/animals
clipart-png/shapes
clipart-png/containers
clipart-png/unsorted
clipart-png/computer
clipart-png/decorations
clipart-png/buttons
clipart-png/special
clipart-png/geography
clipart-png/people
clipart-png/transportation
clipart-png/food
clipart-png/logos
clipart-png/buildings
clipart-png/science
d2-shit
d2-shit/diablo2ciri-master.zip
d2-shit/diablo2-savegames-main.zip
d2-shit/d2characters-master.zip
d2-shit/d2editor-cli-master.zip
d2-shit/d2s-master.zip
d2-shit/D2-Save-Checksum-Fixer-main.zip
d2-shit/go-d2editor-master.zip
d2-shit/diablo2_infodump-master.zip
d2-shit/Diablo2HeroEditor-master.zip
d2-shit/d2s-PHP-Parser-master.zip
d2-shit/d2editor-master.zip
d2-shit/Diablo2CharacterTracker-master.zip
d2-shit/d2fileparser-master.zip
d2-shit/d2_character_editor-master.zip
d2-shit/D2CE-main.zip
php-documentation
php-documentation/php_manual_en.tar.gz
php-documentation/php_enhanced_en.chm
d2-shit-phase2
d2-shit-phase2/Diablo2CharacterTracker-master
d2-shit-phase2/d2characters-master
d2-shit-phase2/Diablo2HeroEditor-master
d2-shit-phase2/d2fileparser-master
d2-shit-phase2/d2s-master
d2-shit-phase2/d2editor-cli-master
d2-shit-phase2/D2-Save-Checksum-Fixer-main
d2-shit-phase2/d2s-PHP-Parser-master
d2-shit-phase2/diablo2ciri-master
d2-shit-phase2/D2CE-main
d2-shit-phase2/diablo2-savegames-main
d2-shit-phase2/d2editor-master
d2-shit-phase2/d2_character_editor-master
d2-shit-phase2/go-d2editor-master
d2-shit-phase2/diablo2_infodump-master
black-wallpaper.png
apache2-icons
apache2-icons/folder.sec.gif
apache2-icons/generic.gif
apache2-icons/quill.png
apache2-icons/portal.gif
apache2-icons/pie6.gif
apache2-icons/hand.up.gif
apache2-icons/tar.png
apache2-icons/p.png
apache2-icons/odf6odg-20x22.png
apache2-icons/unknown.png
apache2-icons/image1.gif
apache2-icons/link.png
apache2-icons/binary.gif
apache2-icons/sphere2.png
apache2-icons/movie.png
apache2-icons/dvi.png
apache2-icons/pie2.gif
apache2-icons/dir.png
apache2-icons/apache_pb.svg
apache2-icons/sound2.gif
apache2-icons/pie5.png
apache2-icons/c.png
apache2-icons/tar.gif
apache2-icons/patch.gif
apache2-icons/box2.gif
apache2-icons/broken.gif
apache2-icons/odf6odp-20x22.png
apache2-icons/ball.gray.gif
apache2-icons/folder.open.gif
apache2-icons/odf6oti-20x22.png
apache2-icons/image1.png
apache2-icons/unknown.gif
apache2-icons/pie7.png
apache2-icons/bomb.png
apache2-icons/f.png
apache2-icons/burst.gif
apache2-icons/odf6odt-20x22.png
apache2-icons/sound1.png
apache2-icons/screw1.png
apache2-icons/tex.png
apache2-icons/odf6ott.png
apache2-icons/text.png
apache2-icons/world1.png
apache2-icons/box1.png
apache2-icons/screw2.png
apache2-icons/odf6odi-20x22.png
apache2-icons/odf6odp.png
apache2-icons/sphere1.png
apache2-icons/up.png
apache2-icons/odf6oth-20x22.png
apache2-icons/xml.png
apache2-icons/blank.png
apache2-icons/hand.right.gif
apache2-icons/bomb.gif
apache2-icons/binhex.png
apache2-icons/continued.png
apache2-icons/forward.gif
apache2-icons/folder.open.png
apache2-icons/hand.up.png
apache2-icons/pie4.gif
apache2-icons/diskimg.gif
apache2-icons/odf6otg.png
apache2-icons/folder.png
apache2-icons/world2.png
apache2-icons/portal.png
apache2-icons/comp.gray.png
apache2-icons/pie6.png
apache2-icons/alert.red.png
apache2-icons/sphere2.gif
apache2-icons/odf6odm-20x22.png
apache2-icons/folder.sec.png
apache2-icons/right.gif
apache2-icons/script.gif
apache2-icons/odf6oth.png
apache2-icons/compressed.gif
apache2-icons/continued.gif
apache2-icons/image2.gif
apache2-icons/world2.gif
apache2-icons/box2.png
apache2-icons/index.png
apache2-icons/odf6otf-20x22.png
apache2-icons/alert.red.gif
apache2-icons/c.gif
apache2-icons/dir.gif
apache2-icons/pie1.png
apache2-icons/patch.png
apache2-icons/comp.blue.gif
apache2-icons/broken.png
apache2-icons/left.png
apache2-icons/odf6ods-20x22.png
apache2-icons/burst.png
apache2-icons/odf6odf-20x22.png
apache2-icons/image3.png
apache2-icons/blank.gif
apache2-icons/layout.gif
apache2-icons/generic.png
apache2-icons/image2.png
apache2-icons/odf6odt.png
apache2-icons/generic.sec.png
apache2-icons/sphere1.gif
apache2-icons/down.gif
apache2-icons/openlogo-75.png
apache2-icons/world1.gif
apache2-icons/quill.gif
apache2-icons/pie0.gif
apache2-icons/hand.right.png
apache2-icons/generic.red.gif
apache2-icons/odf6odi.png
apache2-icons/screw2.gif
apache2-icons/pie4.png
apache2-icons/comp.gray.gif
apache2-icons/movie.gif
apache2-icons/image3.gif
apache2-icons/apache_pb2.gif
apache2-icons/link.gif
apache2-icons/up.gif
apache2-icons/odf6odg.png
apache2-icons/icon.sheet.gif
apache2-icons/script.png
apache2-icons/odf6ott-20x22.png
apache2-icons/apache_pb.png
apache2-icons/uuencoded.gif
apache2-icons/odf6otp.png
apache2-icons/left.gif
apache2-icons/f.gif
apache2-icons/back.gif
apache2-icons/pie2.png
apache2-icons/pie7.gif
apache2-icons/box1.gif
apache2-icons/ps.png
apache2-icons/sound1.gif
apache2-icons/layout.png
apache2-icons/odf6ots-20x22.png
apache2-icons/pie3.gif
apache2-icons/transfer.gif
apache2-icons/odf6odm.png
apache2-icons/text.gif
apache2-icons/transfer.png
apache2-icons/a.gif
apache2-icons/screw1.gif
apache2-icons/p.gif
apache2-icons/ball.red.gif
apache2-icons/pie3.png
apache2-icons/comp.blue.png
apache2-icons/pdf.png
apache2-icons/ball.red.png
apache2-icons/odf6oti.png
apache2-icons/forward.png
apache2-icons/apache_pb2.png
apache2-icons/odf6odb.png
apache2-icons/alert.black.gif
apache2-icons/compressed.png
apache2-icons/sound2.png
apache2-icons/uu.png
apache2-icons/ps.gif
apache2-icons/down.png
apache2-icons/odf6ods.png
apache2-icons/dvi.gif
apache2-icons/generic.sec.gif
apache2-icons/small
apache2-icons/pie8.png
apache2-icons/ball.gray.png
apache2-icons/odf6odc.png
apache2-icons/alert.black.png
apache2-icons/odf6otc.png
apache2-icons/binary.png
apache2-icons/odf6otp-20x22.png
apache2-icons/odf6ots.png
apache2-icons/odf6otg-20x22.png
apache2-icons/folder.gif
apache2-icons/tex.gif
apache2-icons/icon.sheet.png
apache2-icons/apache_pb.gif
apache2-icons/odf6otf.png
apache2-icons/odf6odb-20x22.png
apache2-icons/binhex.gif
apache2-icons/uu.gif
apache2-icons/generic.red.png
apache2-icons/odf6odf.png
apache2-icons/pie8.gif
apache2-icons/diskimg.png
apache2-icons/right.png
apache2-icons/index.gif
apache2-icons/pie5.gif
apache2-icons/pie1.gif
apache2-icons/uuencoded.png
apache2-icons/a.png
apache2-icons/pie0.png
apache2-icons/back.png
apache2-icons/odf6otc-20x22.png
apache2-icons/svg.png
apache2-icons/odf6odc-20x22.png
apache2-icons/pdf.gif
localhost-httpd-master
localhost-httpd-master/www
localhost-httpd-master/start-www.sh
python3-docs.chm
vim-doc-html
vim-doc-html/pi_vimball.html
vim-doc-html/os_win32.html
vim-doc-html/quotes.html
vim-doc-html/version6.html
vim-doc-html/pi_spec.html
vim-doc-html/howto.html
vim-doc-html/gui_w32.html
vim-doc-html/os_mint.html
vim-doc-html/fold.html
vim-doc-html/usr_09.html
vim-doc-html/version5.html
vim-doc-html/tags.html
vim-doc-html/develop.html
vim-doc-html/usr_02.html
vim-doc-html/os_risc.html
vim-doc-html/usr_23.html
vim-doc-html/popup.html
vim-doc-html/vi_diff.html
vim-doc-html/sign.html
vim-doc-html/mlang.html
vim-doc-html/motion.html
vim-doc-html/visual.html
vim-doc-html/usr_29.html
vim-doc-html/terminal.html
vim-doc-html/insert.html
vim-doc-html/usr_12.html
vim-doc-html/usr_22.html
vim-doc-html/os_unix.html
vim-doc-html/mbyte.html
vim-doc-html/ft_sql.html
vim-doc-html/uganda.html
vim-doc-html/usr_27.html
vim-doc-html/if_pyth.html
vim-doc-html/scroll.html
vim-doc-html/cmdline.html
vim-doc-html/usr_44.html
vim-doc-html/helphelp.html
vim-doc-html/gui_x11.html
vim-doc-html/usr_45.html
vim-doc-html/quickref.html
vim-doc-html/tabpage.html
vim-doc-html/usr_41.html
vim-doc-html/russian.html
vim-doc-html/vim9.html
vim-doc-html/os_msdos.html
vim-doc-html/usr_08.html
vim-doc-html/ft_ada.html
vim-doc-html/usr_46.html
vim-doc-html/os_vms.html
vim-doc-html/testing.html
vim-doc-html/tagsrch.html
vim-doc-html/version8.html
vim-doc-html/usr_31.html
vim-doc-html/intro.html
vim-doc-html/quickfix.html
vim-doc-html/various.html
vim-doc-html/diff.html
vim-doc-html/usr_05.html
vim-doc-html/workshop.html
vim-doc-html/netbeans.html
vim-doc-html/usr_04.html
vim-doc-html/os_haiku.html
vim-doc-html/eval.html
vim-doc-html/os_os2.html
vim-doc-html/textprop.html
vim-doc-html/os_390.html
vim-doc-html/usr_42.html
vim-doc-html/arabic.html
vim-doc-html/usr_32.html
vim-doc-html/usr_28.html
vim-doc-html/recover.html
vim-doc-html/debugger.html
vim-doc-html/pi_tar.html
vim-doc-html/rileft.html
vim-doc-html/pi_paren.html
vim-doc-html/digraph.html
vim-doc-html/tips.html
vim-doc-html/usr_11.html
vim-doc-html/vimindex.html
vim-doc-html/if_sniff.html
vim-doc-html/usr_07.html
vim-doc-html/options.html
vim-doc-html/usr_06.html
vim-doc-html/os_mac.html
vim-doc-html/usr_24.html
vim-doc-html/indent.html
vim-doc-html/if_tcl.html
vim-doc-html/os_qnx.html
vim-doc-html/print.html
vim-doc-html/remote.html
vim-doc-html/todo.html
vim-doc-html/starting.html
vim-doc-html/pi_getscript.html
vim-doc-html/if_ruby.html
vim-doc-html/farsi.html
vim-doc-html/channel.html
vim-doc-html/autocmd.html
vim-doc-html/usr_26.html
vim-doc-html/usr_90.html
vim-doc-html/usr_toc.html
vim-doc-html/if_perl.html
vim-doc-html/change.html
vim-doc-html/repeat.html
vim-doc-html/pi_gzip.html
vim-doc-html/if_mzsch.html
vim-doc-html/os_amiga.html
vim-doc-html/term.html
vim-doc-html/windows.html
vim-doc-html/pi_logipat.html
vim-doc-html/debug.html
vim-doc-html/syntax.html
vim-doc-html/editing.html
vim-doc-html/usr_21.html
vim-doc-html/if_cscop.html
vim-doc-html/spell.html
vim-doc-html/gui.html
vim-doc-html/usr_20.html
vim-doc-html/version7.html
vim-doc-html/hebrew.html
vim-doc-html/usr_25.html
vim-doc-html/index.html
vim-doc-html/map.html
vim-doc-html/hangulin.html
vim-doc-html/sponsor.html
vim-doc-html/filetype.html
vim-doc-html/usr_10.html
vim-doc-html/usr_03.html
vim-doc-html/usr_43.html
vim-doc-html/usr_30.html
vim-doc-html/version4.html
vim-doc-html/pi_zip.html
vim-doc-html/usr_40.html
vim-doc-html/usr_01.html
vim-doc-html/pi_netrw.html
vim-doc-html/message.html
vim-doc-html/os_dos.html
vim-doc-html/undo.html
vim-doc-html/ft_rust.html
vim-doc-html/if_lua.html
vim-doc-html/pattern.html
vim-doc-html/if_ole.html
vim-doc-html/os_beos.html
Untitled.txt
python4
python4/1.py
python4/1_py3.py
MIT License

Copyright (c) 2022 user95f85f

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
ls /media/user/DEB_STUFF/essential-debs/ > media_user_DEB_STUFF_essential-debs.txt
gparted_1.2.0-1_amd64.deb
gparted-common_1.2.0-1_all.deb
iotop_0.6-24-g733f3f8-1.1_amd64.deb
net-tools_1.60+git20181103.0eebece-1_amd64.deb
perl-doc_5.32.1-4+deb11u2_all.deb
wget_1.21-1+deb11u1_amd64.deb
ls /media/user/DEB_STUFF/ > media_user_DEB_STUFF.txt
atool-debs
_bashrc
Documents
dot_cache_mesa_shader_cache
dot_local_share_Steam
dot_local_share_volition
dot_steam
dot-wine
epiphany-browser-debs
essential-debs
ffmpeg-libdvdcss2-vlc-debs
go-debs
google-chrome-stable_100.0.4896.75-1_amd64.deb
hexchat-debs
imagemagick-debs
jdk-debs
links-links2-lynx-debs
lost+found
mono-cs-debs
php-debs
python3-doc-debs
python3-tk-debs
python4.pl
setup.txt
steam-debs
steam_latest.deb
vim-debs
_vimrc
wine-debs
xchm-debs



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
What I installed in Steam:
  Team Fortress 2 (FREE)
  Left 4 Dead 2
  Torchlight 2
  Saints Row 2 (actually runs native!)
What I installed in WINE:
  Battle.net app and then through that got World of Warcraft working
  I plan on installing Diablo II && Diablo II: LoD


" :syntax on    to enable syntax highlighting supposidly
:se ts=2
:se sw=2
:se expandtab

"I love the arrow keys.
noremap <right> :bn<CR>
noremap <left> :bp<CR>
noremap <up> <PageUp>
noremap <down> <PageDown>

