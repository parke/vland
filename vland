#! /bin/sh


#  vland.sh  -  Create and use chroot-style virtual software environments.
#  Copyright (c) 2021 Parke Bostrom, parke.nexus at gmail.com
#  Distributed under GPLv3 (see end of file) WITHOUT ANY WARRANTY.


readonly  VLAND_VERSION='0.21.0'    #  20211229


#  vland.sh expects /bin/sh to be dash (or busybox or bash).


set  -o errexit


help_common  ()  {    #  ----------------------------------------  help_common

  echo
  echo  'usage:  vland  --create  distro  [userland]  [overlay]'
  echo  '        vland  userland  [overlay]  [option ...]  [-- command ...]'

  return  ;  }


help  ()  {    #  ------------------------------------------------------  help

  help_common

  echo
  echo  'For more help, please run:  vland  --help-more'

  return  ;  }


help_more  ()  {    #  --------------------------------------------  help_more

  help_common

  echo
  echo  'To create a userland and overlay, run:'
  echo  '$  vland  --create  distro  [userland]  [overlay]'
  echo  'If not specified, userland=distro and overlay=userland.'
  echo
  echo  'To run a shell (or a commend) in a userland, run:'
  echo  '$  vland  overlay  [userland]  [option ...]  [-- command [arg ...]]'
  echo  'If not specified, overlay=userland.'
  echo  'Any specified options are simply passed through to lxroot.'
  echo  'Please refer to the Lxroot documentation for details.'
  echo
  echo  'Supported Distros'
  echo  'alpine     Alpine Linux'
  echo  'arch       Arch Linux'
  echo  'void       Void Linux'
  echo  'void-musl  Void Linux (musl libc)'
  echo
  echo  'An Example'
  echo  '$  vland  --create  arch    #  Create an Arch Linux userland.'
  echo  '$  vland  arch  -nr         #  Enter the arch userland as root.'
  echo
  echo  'Fyi, the default location for userlands and overlays is either:'
  echo  '$HOME/vland  or  /vland'
  echo
  echo  'undocumented commands (read the source to see what they do)'
  echo  '  vland  --alpine-sdk  userland'
  echo  '  vland  --download  distro'
  echo  '  vland  --list'
  echo  '  vland  --overlay  overlay  userland'
  echo
  echo  'internal testing commands (read the source to see what they do)'
  echo  '  vland  --aria2c-install'

  return  ;  }


main  ()  {    #  ------------------------------------------------------  main

  main_machine_check
  main_config_load

  case  "$1"  in
    ( ''               )  main_status           ;;
    ( --alpine-sdk     )  alpine_sdk      "$@"  ;;
    ( --aria2c-install )  aria2c_install        ;;
    ( --help           )  help                  ;;
    ( --help-more      )  help_more             ;;
    ( --create         )  create          "$@"  ;;
    ( --download       )  main_download   "$@"  ;;
    ( --list           )  main_list             ;;
    ( --self-test      )  self_test             ;;
    ( --version        )  main_version          ;;
    ( --*              )  die  "vland  error  invalid option  '$1'"  ;;
    ( *                )  launch  "$@"  ;;
    esac

  exit  ;  }


###  vland global variables  -------------------------  vland global variables
#
#  over_name    the name of the overlay
#  land_name    the name of the userland
#  distro       the name of the distro
#
#  vland_dir    typically:  $HOME/vland  or  /vland/$USER
#  over         typically:  $vland_dir/over/$over_name
#  land         typcially:  $vland_dir/land/$land_name
#
#  tgz_url      typically:  the url of distro rootfs tarball
#  tgz          typically:  the filename portion of $tgz_url
#  dist_path    typically:  $vland_dir/dist/$distro/$tgz


main_config_load  ()  {    #  ------------------------------  main_config_load
  local  path="$HOME/.config/vland/vland.conf"
  [ -f "$path" ]  &&  .  "$path"
  readonly  vland_dir="$(  main_config_vland_dir  )"
  return  ;  }


main_config_vland_dir  ()  {    #  --------------------  main_config_vland_dir
  [    "$vland_dir"       ]  &&  {  echo  "$vland_dir"    ;  return  ;  }
  [ -d "$HOME/vland/land" ]  &&  {  echo  "$HOME/vland"   ;  return  ;  }
  [ -d "/vland"           ]  &&  {  echo  "/vland/$USER"  ;  return  ;  }
  echo  "$HOME/vland"  ;  }


main_download  ()  {    #  ------------------------------------  main_download
  #  usage:  main_download  --download  distro
  case  "$2"  in
    ( alpine    )  alpine_download     ;  alpine_verify     ;;
    ( arch      )  arch_download       ;  arch_verify       ;;
    ( void      )  void_download       ;  void_verify       ;;
    ( void-musl )  void_musl_download  ;  void_musl_verify  ;;
    ( * )  die  "vland  error  download unsupported  '$2'"  ;;
   esac  }


main_list  ()  {    #  --------------------------------------------  main_list
  #  usage:  main_list
  echo
  echo  'main_list'
  echo  "  vland_dir  '$vland_dir'"
  return  ;  }


main_machine_check  ()  {    #  --------------------------  main_machine_check
  local  machine="$(  uname  -m  )"
  [ "$machine" = 'x86_64' ]  &&  return
  die  "vland  error  unsupported machine type  '$machine'"  ;  }


main_status  ()  {    #  ----------------------------------------  main_status

  echo
  echo  -n  'usage:  vland  overlay  [userland]  [option ...]  '
  echo                        '[-- command [arg ...]]'
  echo      '        vland  --help'

  echo
  echo  'available overlays'
  if  [ -d "$vland_dir/over" ]
    then  ls  "$vland_dir/over"
    else  echo  "(none)"  ;  fi  }


main_version  ()  {    #  --------------------------------------  main_version
  echo  "vland  version  $VLAND_VERSION"  ;  }


#  create  -----------------------------------------------------------  create


create_init  ()  {    #  ----------------------------------------  create_init
  #  usage:  create_init  --create  distro  [land]  [overlay]
  distro="$2"
  land_name="${3:-$distro}"
  over_name="${4:-$land_name}"
  land="$vland_dir/land/$land_name"
  over="$vland_dir/over/$over_name"  ;  }


create_userland  ()  {    #  --------------------------------  create_userland
  #  usage:  create_userland
  case  "$distro"  in
    ( alpine    )  alpine_create     ;  return  ;;
    ( arch      )  arch_create       ;  return  ;;
    ( void      )  void_create       ;  return  ;;
    ( void-musl )  void_musl_create  ;  return  ;;
    esac
  die  "vland  error  unsupported distro  '$distro'"  ;  }


create_overlay  ()  {    #  ----------------------------------  create_overlay
  #  usage:  create_overlay
  echo  'create_overlay'
  trace_mkdir_p  "$over"  ;  }


create  ()  {    #  --------------------------------------------------  create
  #  usage:  create  --create  distro  [land]  [overlay]
  create_init  "$@"
  create_overlay
  create_userland
  echo  ;  echo  "vland  done  '$*'"  ;  }


#  launch  -----------------------------------------------------------  launch


launch_init_land  ()  {    #  ------------------------------  launch_init_land

  #  usage:  launch_init_land

  [ -d "$land" ]  ||  die  "vland  error  userland not found  '$land_name'"

  #  todo  add $USER to /etc/passwd

  return  ;  }


launch_init_over  ()  {    #  ------------------------------  launch_init_over

  #  usage:  launch_init_over

  [ -d "$over" ]  ||  die  "vland  error  overlay not found  '$over_name'"

  #  20211221  todo  read $HOME from $land/etc/passwd
  #  20211227  todo  add  $USER to   $land/etc/passwd

  mkdir  -p  "$over/$HOME"
  mkdir  -p  "$over/root"
  mkdir  -p  "$over/tmp/.X11-unix"

  return  ;  }


launch_shift  ()  {    #  --------------------------------------  launch_shift
  #  usage:  launch_shift  "$1"
  case  "$1"  in
    ( -* | '' )  return  1  ;;    #  don't discard these
    esac  ;  }                    #  discard everything else


launch  ()  {    #  --------------------------------------------------  launch

  #  usage:  launch  over  [land]  [option ...]

  overlay_parse  "$@"
  launch_init_land
  launch_init_over

  launch_shift  "$1"  &&  shift    #  discard  over
  launch_shift  "$1"  &&  shift    #  discard  land

  lxroot_install
  trace  exec  "$lxroot"  "$land"  "$over"  "$@"

  return  ;  }


#  lib  -----------------------------------------------------------------  lib


aria2c_install  ()  {    #  ----------------------------------  aria2c_install

  #  usage:  aria2c_install

  [ "$aria2c" ]  &&  return

  [ "$( which aria2c )" ]  &&  {  readonly  aria2c='aria2c'  ;  return  ;  }

  echo  ;  echo  'aria2c_install'

  local  url_base='https://github.com/parke/aria2.sh/releases/download/0.21.0'
  local  url="$url_base/aria2c-x86_64-20211228.txz"
  local  path="$vland_dir/dist/aria2/${url##*/}"
  local  sha='74272ef1b7cbccbcdf82aeda7464b2ffff9241cb18c06121263c3126b61e4810'
  local  b2='7be265a672873c5f4f164a3da8c003eea5c5c3006f70ec5fbb0d8a2fb4d028fe'
  local  bin_dir="$vland_dir/dist/bin"

  lib_download  "$url"  "$path"
  lib_verify    "$path"  "$sha"    sha256sum
  lib_verify    "$path"  "$b2"     b2sum  -l 256

  trace_mkdir_p  "$bin_dir"
  trace  tar  xJf  "$path"  -C "$bin_dir"

  local  sha='85c2cd2eff637fbd7eecdd438ccd1b1053660e052d41a3055dceb703d92c4dba'
  local  b2='43c93739d17431e7eaebcc0f0dc9b725acccef5e03cfca2923a3631c81a43649'

  readonly  aria2c="$bin_dir/aria2c-x86_64"

  lib_verify  "$aria2c"  "$sha"    sha256sum
  lib_verify  "$aria2c"  "$b2"     b2sum  -l 256  ;  }


die  ()  {    #  --------------------------------------------------------  die
  1>&2  echo  "$*"  ;  exit  1  ;  }


distro_unsupported  ()  {    #  --------------------------  distro_unsupported
  echo  ;  echo  "vland  distro not supported  '$1'"  ;  exit  1  ;  }


lib_download  ()  {    #  --------------------------------------  lib_download
  #  usage:  lib_download  url  path
  local  url="$1"  path="$2"
  [ -e "$path" ]  &&  return
  trace_mkdir_p_dirname  "$path"
  trace  wget  --no-clobber  "$url"  -O "$path"  ;  }


lib_download_grep_aria  ()  {    #  ------------------  lib_download_grep_aria

  #  usage:  lib_download_grep_aria

  local  line  prev
  while  read  line  ;  do
    case  "$line"  in

      ('');;
      ("$prev");;
      ('FILE: '*);;
      ('========'*);;
      ('--------'*);;
      (*' - Redirecting to '*);;
      (*' - Download aborted. URI='*);;
      ('Exception: [AbstractCommand.cc:'*);;
      ('*** Download Progress Summary as of '*);;
      ('-> [HttpResponse.cc:'*'] errorCode='*' Invalid range header. '*);;
      ('-> [SocketCore.cc:'*'] errorCode='*' SSL/TLS handshake failure: '*);;
      ('-> [HttpSkipResponseCommand.cc:'*'] errorCode=3 Resource not found');;

      (*)  echo  "$line"  ;  prev="$line"  ;;  esac  ;  done  }


lib_download_multi  ()  {    #  --------------------------  lib_download_multi

  #  usage:  lib_download_multi  filename  seeds  dest_path

  local  filename="$1"  seeds="$2"  dest_path="$3"

  [ -e "$dest_path" ]  &&  return

  aria2c_install

  local  dest_dir="${dest_path%/*}"  seed
  trace_mkdir_p  "$dest_dir"

  local  urls="$(
    for  seed  in  $seeds  ;  do  echo  "$seed/$filename"  ;  done  )"

  echo  ;  echo  "lib_download_multi  seeds(${#seeds})  $filename  ..."

  "$aria2c"  --dir="$dest_dir"  --split=40  --min-split-size=1M  \
      --summary-interval=1  $urls  |  lib_download_grep_aria  ;  }


lib_verify  ()  {    #  ------------------------------------------  lib_verify

  #  usage:  lib_verify  [-v]  path  expect  algo  [opts]

  local  verbose=''
  if  [ "$1" = '-v' ]  ;  then  verbose=1  ;  shift  ;  fi
  local  path="$1"  expect="$2"  algo="$3"  ;  shift  3

  if  [ ! -x "$( which "$algo" )" ]  ;  then
    case  "$algo"  in  ( b2sum | sha3sum )  return  ;;  esac
    echo  "vland  error  lib_verify  algo not found  '$algo'"
    exit  1  ;  fi

  if  [ "$verbose" = '1' ]  ;  then  echo  "$algo  $path"  ;  fi
  local  actual
  actual=`  "$algo"  "$@"  "$path"  `
  actual="${actual%% *}"

  if  [ ! "$actual" = "$expect" ]  ||  [ "$verbose" = '1' ]  ;  then
    local  realpath="$(  realpath  "$path"  )"
    echo  "verify    $algo  $path"
    echo  "  expect  $expect"
    echo  "  actual  $actual"
    #  busybox stat lacks '--dereference'
    #  busybox stat lacks '--format' but has '-c'
    echo  "  size    $(  stat  -c '%s'  "$realpath"  )"
    fi

  if  [ "$actual" = "$expect" ]  ;  then  return  ;  fi
  echo  "verify  failed  exiting..."
  exit  1  ;  }


lxr  ()  {    #  --------------------------------------------------------  lxr
  #  usage:  lxr  [option ...]  [ -- command [arg ...]]
  lxroot_install
  trace  "$lxroot"  "$land"  "$over"  "$@"  ;  }


lxroot_install  ()  {    #  ----------------------------------  lxroot_install

  #  usage:  lxroot_install

  [ "$lxroot" ]  &&  return

  [ "$( which lxroot )" ]  &&  {  readonly  lxroot='lxroot'  ;  return  ;  }

  local  url_base='https://github.com/parke/lxroot/releases/download/0.21.0'
  local  url="$url_base/lxroot-x86"
  local  path="$vland_dir/dist/bin/lxroot-x86"
  local  md5='8092c46013c130baaac4200a7d67ed16'
  local  sha='ea58e53d2d237ed2a11af1029d4f668c938e4ea1dc45f7681de9f441048394ac'
  local  sha3='c620136aa93839041515a63b27289a6c31ed29605f7099c01f46b5ee'
  local  b2='d6543397b0573b8b49ab48d60bfe09d1710d80149747687e21eed0a32f030ae2'

  lib_download  "$url"  "$path"
  lib_verify    "$path"  "$md5"   md5sum
  lib_verify    "$path"  "$sha"   sha256sum
  lib_verify    "$path"  "$sha3"  sha3sum
  lib_verify    "$path"  "$b2"    b2sum  -l 256

  chmod  +x  "$path"
  readonly  lxroot="$path"
  return  ;  }


overlay_parse  ()  {    #  ------------------------------------  overlay_parse

  #  usage:  overlay_parse  overlay  [userland]

  case  "$1"  in
    ( '' | -* )  die  "vland  error  invalid overlay  '$1'"  ;;
    ( *       )  over_name="$1"  ;;
    esac

  case  "$2"  in
    ( '' | -* )  land_name="$over_name"  ;;
    ( *       )  land_name="$2"     ;;
    esac

  [ "$vland_dir" ]  ||  die  "vland  internal error  vland_dir not set"

  over="$vland_dir/over/$over_name"
  land="$vland_dir/land/$land_name"  ;  }


pinwheel  ()  {    #  ----------------------------------------------  pinwheel
  local  line  n='0'
  echo  -n  '/ '
  while  read  line  ;  do
    case  "$n"  in
      ('0')  echo  -n  '\b\b- '  ;;
      ('1')  echo  -n  '\b\b\ '  ;;
      ('2')  echo  -n  '\b\b| '  ;;
      ('3')  echo  -n  '\b\b/ '  ;;
      (*)    echo  -n  '\b\bE '  ;;
      esac
    n="$(( ( n + 1 ) % 4 ))"
    done
  return  ;  }


resolv_conf_sync  ()  {    #  ------------------------------  resolv_conf_sync
  #  usage:  resolv_conf_sync  path/to/userland/etc/resolv.conf
  if  [ -h "$1" ]  ;  then  trace  rm  "$1"  ;  fi
  #  busybox cmp lacks '--silent' but has '-s'
  if  cmp  -s  /etc/resolv.conf  "$1"  ;  then  return  ;  fi
  trace  cp  /etc/resolv.conf  "$1"  ;  }


trace  ()  {    #  ----------------------------------------------------  trace
  echo  "+  $*"  1>&2  ;  "$@"  ;  }


trace_mkdir_p  ()  {    #  ------------------------------------  trace_mkdir_p
  #  usage:  trace_mkdir_p  path
  [ -d "$1" ]  &&  return
  trace  mkdir  -p  "$1"  ;  }


trace_mkdir_p_dirname  ()  {    #  --------------------  trace_mkdir_p_dirname
  #  usage:  trace_mkdir_p_dirname
  trace_mkdir_p  "${1%/*}"  ;  }


xray  ()  {    #  ------------------------------------------------------  xray

  #  usage:  xray  function_name  [arg ...]

  echo
  echo  "xray  $1"  ;  [ "$1" ]  &&  shift
  echo
  echo  "  args       '$*'  $#"
  echo
  echo  "  over_name  '$over_name'"
  echo  "  land_name  '$land_name'"
  echo  "  distro     '$distro'"
  echo
  echo  "  vland_dir  '$vland_dir'"
  echo  "  over       '$over'"
  echo  "  land       '$land'"
  echo  "  dist_path  '$dist_path'"
  echo
  echo  "  real  vland_dir  '$(  realpath  "$vland_dir"  )'"
  echo  "  real  over       '$(  realpath  "$over"       )'"
  echo  "  real  land       '$(  realpath  "$land"       )'"  ;  }




#  distro  ===========================================================  distro




#  alpine  -----------------------------------------------------------  alpine


alpine_vars_set  ()  {    #  --------------------------------  alpine_vars_set

  #  usage:  alpine_vars_set

  tgz_ver='3.15.0'
  tgz_url="https://dl-cdn.alpinelinux.org/alpine/v${tgz_ver%.*}"
  tgz_url="$tgz_url/releases/x86_64/alpine-minirootfs-${tgz_ver}-x86_64.tar.gz"
  tgz="${tgz_url##*/}"

  tgz_sha256='ec7ec80a96500f13c189a6125f2dbe8600ef593b87fc4670fe959dc02db727a2'
  tgz_b2='a67c1a7c7307534345946211f44a4e0bb5278b8eaccc1a2d3aa9f96b992da8f1'

  dist_path="$vland_dir/dist/alpine/${tgz}"  ;  }


alpine_download  ()  {    #  --------------------------------  alpine_download
  #  usage:  alpine_download
  [ -e "$dist_path" ]  &&  return
  echo  ;  echo  'alpine_download'
  alpine_vars_set
  lib_download  "$tgz_url"  "$dist_path"  ;  }


alpine_verify  ()  {    #  ------------------------------------  alpine_verify
  #  usage:  alpine_verify
  echo  ;  echo  "alpine_verify  $dist_path"
  lib_verify  "$dist_path"  "$tgz_sha256"    sha256sum
  lib_verify  "$dist_path"  "$tgz_b2"        b2sum  -l 256
  echo  'alpine_verify  done'  ;  }


alpine_extract  ()  {    #  ----------------------------------  alpine_extract
  #  usage:  alpine_extract
  [ -e "$land" ]  &&
    die  "vland  error  userland already exists  '$land'"
  alpine_download
  alpine_verify
  echo  ;  echo  'alpine_extract'
  trace  mkdir  -p  "$land"
  trace  tar  xzf "$dist_path"  -C "$land"  ;  }


alpine_configure  ()  {    #  ------------------------------  alpine_configure
  trace  resolv_conf_sync  "$land"/etc/resolv.conf
  return  ;  }


alpine_create  ()  {    #  ------------------------------------  alpine_create
  #  usage:  alpine_create
  alpine_vars_set
  alpine_extract
  alpine_configure
  echo  ;  echo  'vland  alpine_create  done'  ;  }


alpine_sdk  ()  {    #  ------------------------------------------  alpine_sdk

  #  usage:  alpine_sdk  --alpine-sdk  overlay  [userland]

  echo  ;  echo  'alpine_sdk'

  shift  ;  overlay_parse  "$@"

  [ -d "$over" ]  || die  "vland  error  overlay not found  '$over'"
  [ -d "$land" ]  || die  "vland  error  userland not found  '$land'"

  local  url='https://gitlab.alpinelinux.org/alpine/aports/-/archive/master'
  local  url="$url/aports-master.tar.bz2"

  local  abuild_path="$land/usr/bin/abuild"
  local  over_home="$over/${HOME#/}"
  local  tbz="${url##*/}"
  local  tbz_path="$over_home/$tbz"
  local  ports="$over_home/aports-master"

  if  [ -e "$abuild_path" ]
    then  echo  "alpine_sdk  found  '$abuild_path'"
    else  trace  lxr  -nw  --  apk  update
          trace  lxr  -nw  --  apk  add  alpine-sdk  ;  fi

  #  remove abuild privilege escalation
  ln  -sf  '/usr/sbin/addgroup'  "$land/usr/bin/abuild-addgroup"
  ln  -sf  '/usr/sbin/adduser'   "$land/usr/bin/abuild-adduser"
  ln  -sf  '/sbin/apk'           "$land/usr/bin/abuild-apk"

  [ -e "$tbz_path" ]  &&  echo  "alpine_sdk  found  '$tbz_path'"
  lib_download  "$url"  "$tbz_path"

  if  [ -e "$ports" ]
    then  echo  "alpine_sdk  found  '$ports'"
    else  trace  tar  xjf "$tbz_path"  -C "$over_home"  ;  fi

  #  todo:  generate & install package signing key

  echo  'alpine_sdk  done'

  return  ;  }


#  arch  ---------------------------------------------------------------  arch


arch_vars_set  ()  {    #  ------------------------------------  arch_vars_set

  mirror='https://mirror.sfo12.us.leaseweb.net'
  mirror="Server = ${mirror}/archlinux/\$repo/os/\$arch"

  tgz_ver='2021.12.01'
  tgz_md5='bf22de5c01c7ddf2349646d32dd74b5b'
  tgz_sha1='5cf00edc931f92b1cac8117ef51ac0325369bd96'
  tgz_b2='f339b9f129f306e92375240ab81f97f681a273bf7d37482b32eb4f6e86e52c90'

  tgz_url="http://mirror.rackspace.com/archlinux/iso/${tgz_ver}"
  tgz_url="${tgz_url}/archlinux-bootstrap-${tgz_ver}-x86_64.tar.gz"
  tgz="${tgz_url##*/}"

  dist_path="$vland_dir/dist/arch/$tgz"

  return  ;  }


arch_download  ()  {    #  ------------------------------------  arch_download
  #  usage:  arch_download
  arch_vars_set
  echo  ;  echo  "arch_download  $dist_path"
  lib_download_multi  "$tgz"  "$arch_web_seeds"  "$dist_path"
  echo  "arch_download  done"  ;  }


arch_verify  ()  {    #  ----------------------------------------  arch_verify
  #  usage:  arch_verify
  arch_vars_set
  echo  ;  echo  "arch_verify  $dist_path"
  lib_verify  "$dist_path"  "$tgz_md5"     md5sum
  lib_verify  "$dist_path"  "$tgz_sha1"    sha1sum
  lib_verify  "$dist_path"  "$tgz_b2"      b2sum  -l 256
  echo  'arch_verify  done'  ;  }


arch_extract  ()  {    #  --------------------------------------  arch_extract

  #  usage:  arch_extract

  [ -e "$land" ]  &&
    die  "vland  error  userland already exists  '$land'"

  arch_download
  arch_verify

  echo  ;  echo  'arch_extract'
  trace  mkdir  -p  "$land"
  local  opts='--delay-directory-restore  --strip-components=1'
  trace  tar  xzf "$dist_path"  -C "$land"  $opts
  trace  chmod  u+w  "$land/etc/ca-certificates/extracted/cadir"
  echo  'arch_extract  done'

  return  ;  }


arch_configure  ()  {    #  ----------------------------------  arch_configure

  #  usage:  arch_configure

  lxroot_install

  echo  ;  echo  'arch_configure  ...'  ;  echo

  #  20211219  todo  copy localtime from host rather than using Los_Angeles?

  trace  resolv_conf_sync  "$land"/etc/resolv.conf
  trace  ln  -snf  /usr/share/zoneinfo/America/Los_Angeles  \
    "$land"/etc/localtime

  echo  "$mirror"  \
    |  trace  dd  of="$land"/etc/pacman.d/mirrorlist  status=none

  echo  ;  trace  lxr  -nr  --  pacman-key  --init
  echo  ;  trace  lxr  -nr  --  pacman-key  --populate  archlinux

  echo  ;  echo  'arch_configure  done'  ;  }


arch_create  ()  {    #  ----------------------------------------  arch_create
  #  usage:  arch_create
  echo  ;  echo  'arch_create'
  arch_vars_set
  arch_extract
  arch_configure
  echo  ;  echo  'arch_create  done'  ;  }


#  void  ---------------------------------------------------------------  void


void_vars_set  ()  {    #  ------------------------------------  void_vars_set

  #  usage:  void_vars_set

  tgz_ver='20210930'
  tgz_url='https://alpha.de.repo.voidlinux.org/live/current'
  tgz_url="${tgz_url}/void-x86_64-ROOTFS-${tgz_ver}.tar.xz"

  tgz_sha256='8681b060e39e173682e1721a6088280c2b6eade628f5e5e3e8e4b74163d187f6'
  tgz_b2='697b2e4e92c3fce1d74fea30fc3c9b95324a01ff59cb4cacf493a0ff29d750b7'

  tgz="${tgz_url##*/}"

  dist_path="$vland_dir/dist/void/$tgz"  ;  }


void_download  ()  {    #  ------------------------------------  void_download
  #  usage:  void_download
  [ -e "$dist_path" ]  &&  return
  echo  ;  echo  'void_download'
  void_vars_set
  lib_download  "$tgz_url"  "$dist_path"  ;  }


void_verify  ()  {    #  ----------------------------------------  void_verify
  #  usage:  void_verify
  echo  ;  echo  'void_verify'
  void_vars_set
  lib_verify  "$dist_path"  "$tgz_sha256"    sha256sum
  lib_verify  "$dist_path"  "$tgz_b2"        b2sum  -l 256  ;  }


void_extract  ()  {    #  --------------------------------------  void_extract
  #  usage:  void_extract
  [ -e "$land" ]  &&
    die  "vland  error  userland already exists  '$land'"
  void_download
  void_verify
  echo  ;  echo  'void_extract'
  trace  mkdir  -p  "$land"
  trace  tar  xJf "$dist_path"  -C "$land"  ;  }


void_configure  ()  {    #  ----------------------------------  void_configure
  #  usage:  void_configure
  echo  ;  echo  'void_configure'
  trace  resolv_conf_sync  "$land"/etc/resolv.conf
  return  ;  }


void_create  ()  {    #  ----------------------------------------  void_create
  #  usage:  void_create
  void_vars_set
  void_extract
  void_configure
  echo  ;  echo  'vland  void_create  done'  ;  }


#  void_musl  -----------------------------------------------------  void_musl


void_musl_vars_set  ()  {    #  --------------------------  void_musl_vars_set

  #  usage:  void_musl_vars_set

  tgz_ver='20210930'
  tgz_url='https://alpha.de.repo.voidlinux.org/live/current'
  tgz_url="${tgz_url}/void-x86_64-musl-ROOTFS-${tgz_ver}.tar.xz"

  tgz_sha256='d322171b39e3c670faa2835f6c6bba27951a9710f018410e090247b651f9251a'
  tgz_b2='b35d71e4c330dc47412e3e8e1a4c99482ef306f0dff39ce494a4164babf5baca'

  tgz="${tgz_url##*/}"

  dist_path="$vland_dir/dist/void-musl/$tgz"  ;  }


void_musl_download  ()  {    #  --------------------------  void_musl_download
  #  usage:  void_download
  [ -e "$dist_path" ]  &&  return
  echo  ;  echo  'void_download'
  void_musl_vars_set
  lib_download  "$tgz_url"  "$dist_path"  ;  }


void_musl_verify  ()  {    #  ------------------------------  void_musl_verify
  #  usage:  void_verify
  echo  ;  echo  'void_verify'
  void_musl_vars_set
  lib_verify  "$dist_path"  "$tgz_sha256"    sha256sum
  lib_verify  "$dist_path"  "$tgz_b2"        b2sum  -l 256  ;  }


void_musl_create  ()  {    #  ------------------------------  void_musl_create
  #  usage:  void_musl_create
  void_musl_vars_set
  void_extract
  void_configure
  echo  ;  echo  'vland  void_musl_create  done'  ;  }


#  self_test  -----------------------------------------------------  self_test


self_test_clean  ()  {    #  --------------------------------  self_test_clean
  #  usage:  self_test_clean
  echo  ;  echo  'self_test_clean'
  [ -d "$vland_dir" ]  ||  return
  [ -d              "$vland_dir"/land/vland-self-test ]  &&
    trace  rm  -rf  "$vland_dir"/land/vland-self-test
  [ -d              "$vland_dir"/over/vland-self-test ]  &&
    trace  rm  -rf  "$vland_dir"/over/vland-self-test
  return  0  ;  }


self_test_download  ()  {    #  --------------------------  self_test_download
  #  usage:  self_test_download
  echo  ;  echo  'self_test_download'
  lxroot_install
  alpine_vars_set  ;  alpine_download  ;  alpine_verify
  arch_vars_set    ;  arch_download    ;  arch_verify  ;  }


self_test_vland_create  ()  {    #  ------------------  self_test_vland_create
  #  usage:  self_test_vland_create
  echo  ;  echo  'self_test_vland_create'
  local  vland="$_"
  trace  /bin/sh  "$vland"  --create  alpine  vland-self-test
  return  ;  }


self_test_vland_install  ()  {    #  ----------------  self_test_vland_install
  #  usage:  self_test_vland_install
  echo  ;  echo  'self_test_vland_install'
  local  vland="$_"
  local  dest="$HOME"/vland/land/vland-self-test/usr/local/bin/vland
  trace  cp     "$vland"  "$dest"
  trace  chmod  +x  "$dest"
  return  ;  }


self_test_arch_copy  ()  {    #  ------------------------  self_test_arch_copy
  #  usage  self_test_files_copy
  echo  ;  echo  'self_test_files_copy'
  local  outer_path="$HOME"/vland/over/vland-self-test
  local  inner_path="$HOME"/vland/dist/arch/
  arch_vars_set
  trace  mkdir  -p            "${outer_path}/${inner_path}"
  trace  cp     "$dist_path"  "${outer_path}/${inner_path}/"
  return  ;  }


self_test_vland_create_arch  ()  {    #  --------  self_test_vland_create_arch
  echo  ;  echo  'self_test_vland_create_arch'
  local  vland="$_"
  #  Alpine busybox tar lacks '--delay-directory-restore' (needed for Arch)
  #  Alpine busybox lacks xz
  trace  /bin/sh  "$vland"  vland-self-test  -nr  --  apk  update
  trace  /bin/sh  "$vland"  vland-self-test  -nr  --  apk  add  tar  xz
  trace  /bin/sh  "$vland"  vland-self-test  -n   --  vland  --create  arch
  echo
  echo
  echo  '----------------------------------------------------------------'
  echo  '  vland  --self-test  now launching interactive root shell ...  '
  echo  '----------------------------------------------------------------'
  echo
  echo
  trace  /bin/sh  "$vland"  vland-self-test  -n   --  vland  arch  -nr
  return  ;  }


self_test  ()  {    #  --------------------------------------------  self_test
  #  usage:  self_test
  echo  ;  echo  'vland  self_test  ...'
  self_test_clean
  self_test_download
  self_test_vland_create
  self_test_vland_install
  self_test_arch_copy
  self_test_vland_create_arch
  echo  ;  echo  'vland  self_test  done'
  return  ;  }


#  arch_web_seeds  -------------------------------------------  arch_web_seeds


arch_web_seeds='
  https://mirror.aarnet.edu.au/pub/archlinux/iso/2021.12.01/
  https://mirrors.rit.edu/archlinux/iso/2021.12.01/
  https://ftp.heanet.ie/mirrors/ftp.archlinux.org/iso/2021.12.01/
  https://mirror.puzzle.ch/archlinux/iso/2021.12.01/
  https://mirror.csclub.uwaterloo.ca/archlinux/iso/2021.12.01/
  https://mirror.umd.edu/archlinux/iso/2021.12.01/
  https://mirror.archlinux.no/iso/2021.12.01/
  https://mirror.isoc.org.il/pub/archlinux/iso/2021.12.01/
  https://mirror.yandex.ru/archlinux/iso/2021.12.01/
  https://ftp.spline.inf.fu-berlin.de/mirrors/archlinux/iso/2021.12.01/
  https://mirror.selfnet.de/archlinux/iso/2021.12.01/
  https://mirrors.lug.mtu.edu/archlinux/iso/2021.12.01/
  https://archlinux.nautile.nc/archlinux/iso/2021.12.01/
  https://mirrors.kernel.org/archlinux/iso/2021.12.01/
  https://ftp.rnl.tecnico.ulisboa.pt/pub/archlinux/iso/2021.12.01/
  https://mirrors.dotsrc.org/archlinux/iso/2021.12.01/
  https://ftp.jaist.ac.jp/pub/Linux/ArchLinux/iso/2021.12.01/
  https://ftp.halifax.rwth-aachen.de/archlinux/iso/2021.12.01/
  https://shadow.ind.ntou.edu.tw/archlinux/iso/2021.12.01/
  https://mirrors.rutgers.edu/archlinux/iso/2021.12.01/
  https://mirrors.nix.org.ua/linux/archlinux/iso/2021.12.01/
  https://mirrors.ustc.edu.cn/archlinux/iso/2021.12.01/
  https://ftp.lysator.liu.se/pub/archlinux/iso/2021.12.01/
  https://mirror.ams1.nl.leaseweb.net/archlinux/iso/2021.12.01/
  https://mirror.dal10.us.leaseweb.net/archlinux/iso/2021.12.01/
  https://mirror.fra10.de.leaseweb.net/archlinux/iso/2021.12.01/
  https://mirror.mia11.us.leaseweb.net/archlinux/iso/2021.12.01/
  https://mirror.sfo12.us.leaseweb.net/archlinux/iso/2021.12.01/
  https://mirror.wdc1.us.leaseweb.net/archlinux/iso/2021.12.01/
  https://mirrors.n-ix.net/archlinux/iso/2021.12.01/
  https://mirror.dkm.cz/archlinux/iso/2021.12.01/
  https://mirror.lnx.sk/pub/linux/archlinux/iso/2021.12.01/
  https://mirror.ps.kz/archlinux/iso/2021.12.01/
  https://mirror.bytemark.co.uk/archlinux/iso/2021.12.01/
  https://mirror.rol.ru/archlinux/iso/2021.12.01/
  https://mirror.i3d.net/pub/archlinux/iso/2021.12.01/
  https://mirrors.tuna.tsinghua.edu.cn/archlinux/iso/2021.12.01/
  https://mirrors.neusoft.edu.cn/archlinux/iso/2021.12.01/
  https://www.mirrorservice.org/sites/ftp.archlinux.org/iso/2021.12.01/
  https://mirror.netcologne.de/archlinux/iso/2021.12.01/
  https://archlinux.vi-di.fr/iso/2021.12.01/
  https://mirror.system.is/arch/iso/2021.12.01/
  https://dfw.mirror.rackspace.com/archlinux/iso/2021.12.01/
  https://hkg.mirror.rackspace.com/archlinux/iso/2021.12.01/
  https://iad.mirror.rackspace.com/archlinux/iso/2021.12.01/
  https://lon.mirror.rackspace.com/archlinux/iso/2021.12.01/
  https://mirror.rackspace.com/archlinux/iso/2021.12.01/
  https://ord.mirror.rackspace.com/archlinux/iso/2021.12.01/
  https://syd.mirror.rackspace.com/archlinux/iso/2021.12.01/
  https://arch.mirror.constant.com/iso/2021.12.01/
  https://mirror.premi.st/archlinux/iso/2021.12.01/
  https://download.nus.edu.sg/mirror/archlinux/iso/2021.12.01/
  https://arch.nimukaito.net/iso/2021.12.01/
  https://mirror.neuf.no/archlinux/iso/2021.12.01/
  https://mirror.gnomus.de/iso/2021.12.01/
  https://ftp.fau.de/archlinux/iso/2021.12.01/
  https://gluttony.sin.cvut.cz/arch/iso/2021.12.01/
  https://mirror.one.com/archlinux/iso/2021.12.01/
  https://mirror.t-home.mk/archlinux/iso/2021.12.01/
  https://ftp.yzu.edu.tw/Linux/archlinux/iso/2021.12.01/
  https://mirror.metalgamer.eu/archlinux/iso/2021.12.01/
  https://mirrors.niyawe.de/archlinux/iso/2021.12.01/
  https://mirrors.atviras.lt/archlinux/iso/2021.12.01/
  https://arch.yourlabs.org/iso/2021.12.01/
  https://arch.midov.pl/arch/iso/2021.12.01/
  https://arch.mirror.zachlge.org/iso/2021.12.01/
  https://archlinux.koyanet.lv/archlinux/iso/2021.12.01/
  https://ftp.myrveln.se/pub/linux/archlinux/iso/2021.12.01/
  https://mirror.telepoint.bg/archlinux/iso/2021.12.01/
  https://archlinux.mailtunnel.eu/iso/2021.12.01/
  https://mirrors.cqu.edu.cn/archlinux/iso/2021.12.01/
  https://archlinux.mirror.digitalpacific.com.au/iso/2021.12.01/
  https://mirror.f4st.host/archlinux/iso/2021.12.01/
  https://mirrors.ocf.berkeley.edu/archlinux/iso/2021.12.01/
  https://ftp.acc.umu.se/mirror/archlinux/iso/2021.12.01/
  https://mirror.hackingand.coffee/arch/iso/2021.12.01/
  https://mirrors.uni-plovdiv.net/archlinux/iso/2021.12.01/
  https://mirror.pseudoform.org/iso/2021.12.01/
  https://mirror.lty.me/archlinux/iso/2021.12.01/
  https://arch.jensgutermuth.de/iso/2021.12.01/
  https://pkg.adfinis.com/archlinux/iso/2021.12.01/
  https://ftp.sh.cvut.cz/arch/iso/2021.12.01/
  https://arch-mirror.wtako.net/iso/2021.12.01/
  https://muug.ca/mirror/archlinux/iso/2021.12.01/
  https://mirror.0x.sg/archlinux/iso/2021.12.01/
  https://mirror.wormhole.eu/archlinux/iso/2021.12.01/
  https://mirror.kaminski.io/archlinux/iso/2021.12.01/
  https://mirror.ubrco.de/archlinux/iso/2021.12.01/
  https://archlinux.ip-connect.vn.ua/iso/2021.12.01/
  https://mirror.sergal.org/archlinux/iso/2021.12.01/
  https://mirrors.xjtu.edu.cn/archlinux/iso/2021.12.01/
  https://arlm.tyzoid.com/iso/2021.12.01/
  https://archlinux.thaller.ws/iso/2021.12.01/
  https://archimonde.ts.si/archlinux/iso/2021.12.01/
  https://www.ratenzahlung.de/mirror/archlinux/iso/2021.12.01/
  https://mirror.smith.geek.nz/archlinux/iso/2021.12.01/
  https://archlinux.mirror.wearetriple.com/iso/2021.12.01/
  https://mirror.kku.ac.th/archlinux/iso/2021.12.01/
  https://mirror.osbeck.com/archlinux/iso/2021.12.01/
  https://glua.ua.pt/pub/archlinux/iso/2021.12.01/
  https://ftp.lanet.kr/pub/archlinux/iso/2021.12.01/
  https://mirrors.celianvdb.fr/archlinux/iso/2021.12.01/
  https://arch.mirror.square-r00t.net/iso/2021.12.01/
  https://mirror.pkgbuild.com/iso/2021.12.01/
  https://mirror.xtom.com.hk/archlinux/iso/2021.12.01/
  https://mirror.truenetwork.ru/archlinux/iso/2021.12.01/
  https://mirrors.nxthost.com/archlinux/iso/2021.12.01/
  https://mirror-hk.koddos.net/archlinux/iso/2021.12.01/
  https://mirror.koddos.net/archlinux/iso/2021.12.01/
  https://ftp.wrz.de/pub/archlinux/iso/2021.12.01/
  https://mirror.thekinrar.fr/archlinux/iso/2021.12.01/
  https://mirror.neostrada.nl/archlinux/iso/2021.12.01/
  https://mirrors.ukfast.co.uk/sites/archlinux.org/iso/2021.12.01/
  https://archlinux.mivzakim.net/iso/2021.12.01/
  https://mirror.srv.fail/archlinux/iso/2021.12.01/
  https://mirror.reisenbauer.ee/archlinux/iso/2021.12.01/
  https://packages.oth-regensburg.de/archlinux/iso/2021.12.01/
  https://mirror.orbit-os.com/archlinux/iso/2021.12.01/
  https://mirror.stephen304.com/archlinux/iso/2021.12.01/
  https://mirrors.sjtug.sjtu.edu.cn/archlinux/iso/2021.12.01/
  https://mirrors.cat.net/archlinux/iso/2021.12.01/
  https://mirror.oldsql.cc/archlinux/iso/2021.12.01/
  https://arch.mirrors.lavatech.top/iso/2021.12.01/
  https://mirror.checkdomain.de/archlinux/iso/2021.12.01/
  https://mirrors.xtom.com/archlinux/iso/2021.12.01/
  https://dist-mirror.fem.tu-ilmenau.de/archlinux/iso/2021.12.01/
  https://mirror.fsmg.org.nz/archlinux/iso/2021.12.01/
  https://archlinux.grena.ge/iso/2021.12.01/
  https://archlinux.mirror.pcextreme.nl/iso/2021.12.01/
  https://mirror.cyberbits.eu/archlinux/iso/2021.12.01/
  https://repo.ialab.dsu.edu/archlinux/iso/2021.12.01/
  https://arch.unixpeople.org/iso/2021.12.01/
  https://archlinux.mirror.liquidtelecom.com/iso/2021.12.01/
  https://nova.quantum-mirror.hu/mirrors/pub/archlinux/iso/2021.12.01/
  https://quantum-mirror.hu/mirrors/pub/archlinux/iso/2021.12.01/
  https://super.quantum-mirror.hu/mirrors/pub/archlinux/iso/2021.12.01/
  https://archlinux.uk.mirror.allworldit.com/archlinux/iso/2021.12.01/
  https://archlinux.za.mirror.allworldit.com/archlinux/iso/2021.12.01/
  https://mirror.librelabucm.org/archlinux/iso/2021.12.01/
  https://mirror-archlinux.webruimtehosting.nl/iso/2021.12.01/
  https://mirror.netweaver.uk/archlinux/iso/2021.12.01/
  https://mirror.wtnet.de/arch/iso/2021.12.01/
  https://mirror.mirohost.net/archlinux/iso/2021.12.01/
  https://ftp.harukasan.org/archlinux/iso/2021.12.01/
  https://mirror.ufro.cl/archlinux/iso/2021.12.01/
  https://mirror.aktkn.sg/archlinux/iso/2021.12.01/
  https://arch.nixlab.pl/iso/2021.12.01/
  https://mirrors.xtom.nl/archlinux/iso/2021.12.01/
  https://mirror.scd31.com/arch/iso/2021.12.01/
  https://ftp.icm.edu.pl/pub/Linux/dist/archlinux/iso/2021.12.01/
  https://mirror.mikrogravitation.org/archlinux/iso/2021.12.01/
  https://mirror.chaoticum.net/arch/iso/2021.12.01/
  https://iad.mirrors.misaka.one/archlinux/iso/2021.12.01/
  https://mirror.pit.teraswitch.com/archlinux/iso/2021.12.01/
  https://archlinux.mirror.liteserver.nl/iso/2021.12.01/
  https://mirrors.ims.nksc.lt/archlinux/iso/2021.12.01/
  https://mirrors.eric.ovh/arch/iso/2021.12.01/
  https://mirror.arizona.edu/archlinux/iso/2021.12.01/
  https://mirror.cloroformo.org/archlinux/iso/2021.12.01/
  https://mirror2.evolution-host.com/archlinux/iso/2021.12.01/
  https://mirror.redrock.team/archlinux/iso/2021.12.01/
  https://archmirror1.octyl.net/iso/2021.12.01/
  https://mirror.telkomuniversity.ac.id/archlinux/iso/2021.12.01/
  https://mirror.serverion.com/archlinux/iso/2021.12.01/
  https://plug-mirror.rcac.purdue.edu/archlinux/iso/2021.12.01/
  https://mirror.efect.ro/archlinux/iso/2021.12.01/
  https://mirrors.mit.edu/archlinux/iso/2021.12.01/
  https://arch.hu.fo/archlinux/iso/2021.12.01/
  https://mirrors.chroot.ro/archlinux/iso/2021.12.01/
  https://mirrors.melbourne.co.uk/archlinux/iso/2021.12.01/
  https://mirror.tarellia.net/distr/archlinux/iso/2021.12.01/
  https://mirrors.piconets.webwerks.in/archlinux-mirror/iso/2021.12.01/
  https://mirrors.urbanwave.co.za/archlinux/iso/2021.12.01/
  https://mirrors.dgut.edu.cn/archlinux/iso/2021.12.01/
  https://mirror.sysa.tech/archlinux/iso/2021.12.01/
  https://mirror.rasanegar.com/archlinux/iso/2021.12.01/
  https://mirror.wuki.li/archlinux/iso/2021.12.01/
  https://ftp.sudhip.com/archlinux/iso/2021.12.01/
  https://mirrors.bfsu.edu.cn/archlinux/iso/2021.12.01/
  https://mirroir.wptheme.fr/archlinux/iso/2021.12.01/
  https://mirror.kumi.systems/archlinux/iso/2021.12.01/
  https://mirrors.slaanesh.org/archlinux/iso/2021.12.01/
  https://mirrors.nju.edu.cn/archlinux/iso/2021.12.01/
  https://mirror1.cl.netactuate.com/archlinux/iso/2021.12.01/
  https://mirrors.gethosted.online/archlinux/iso/2021.12.01/
  https://phinau.de/arch/iso/2021.12.01/
  https://mirrors.daan.vodka/archlinux/iso/2021.12.01/
  https://mirror.satis-faction.de/archlinux/iso/2021.12.01/
  https://mirror.sfinae.tech/pub/mirrors/archlinux/iso/2021.12.01/
  https://mirror.gi.co.id/archlinux/iso/2021.12.01/
  https://mirror.papua.go.id/archlinux/iso/2021.12.01/
  https://mirror.lyrahosting.com/archlinux/iso/2021.12.01/
  https://mirror.hodgepodge.dev/archlinux/iso/2021.12.01/
  https://repo.inara.pk/archlinux/iso/2021.12.01/
  https://mirror.dogado.de/archlinux/iso/2021.12.01/
  https://opnmirror.co.za/archlinux/iso/2021.12.01/
  https://mirror.clientvps.com/archlinux/iso/2021.12.01/
  https://zxcvfdsa.com/arch/iso/2021.12.01/
  https://mirror.ihost.md/archlinux/iso/2021.12.01/
  https://pkg.fef.moe/archlinux/iso/2021.12.01/
  https://mirror.ette.biz/archlinux/iso/2021.12.01/
  https://theswissbay.ch/archlinux/iso/2021.12.01/
  https://archmirror.it/repos/iso/2021.12.01/
  https://mirror.anigil.com/archlinux/iso/2021.12.01/
  https://mirrors.hit.edu.cn/archlinux/iso/2021.12.01/
  https://mirror.hoster.kz/archlinux/iso/2021.12.01/
  https://arch.lucassymons.net/iso/2021.12.01/
  https://ftp.agdsn.de/pub/mirrors/archlinux/iso/2021.12.01/
  https://mirror.ava.dev/archlinux/iso/2021.12.01/
  https://mirror.guillaumea.fr/archlinux/iso/2021.12.01/
  https://vpsmurah.jagoanhosting.com/archlinux/iso/2021.12.01/
  https://mirror.arctic.lol/ArchMirror/iso/2021.12.01/
  https://mirror.surf/archlinux/iso/2021.12.01/
  https://mirror.cspacehostings.com/archlinux/iso/2021.12.01/
  https://arch.mcstrugs.org/iso/2021.12.01/
  https://repo.greeklug.gr/data/pub/linux/archlinux/iso/2021.12.01/
  https://europe.mirror.pkgbuild.com/iso/2021.12.01/
  https://america.mirror.pkgbuild.com/iso/2021.12.01/
  https://asia.mirror.pkgbuild.com/iso/2021.12.01/
  https://arch.mirror.jsc.mx/iso/2021.12.01/
  https://mirror.darklinux.uk/archlinux/iso/2021.12.01/
  https://free.nchc.org.tw/arch/iso/2021.12.01/
  https://mirror.juniorjpdj.pl/archlinux/iso/2021.12.01/
  https://tedwall.se/archlinux/iso/2021.12.01/
  https://archlinux.qontinuum.space:4443/iso/2021.12.01/
  https://mirror.nw-sys.ru/archlinux/iso/2021.12.01/
  https://repo.skni.umcs.pl/archlinux/iso/2021.12.01/
  https://mirror.cybersecurity.nmt.edu/archlinux/iso/2021.12.01/
  https://mirror.0xem.ma/arch/iso/2021.12.01/
  https://mirror.cj2.nl/archlinux/iso/2021.12.01/
  https://mirror.hostup.org/archlinux/iso/2021.12.01/
  https://mirrors.xtom.de/archlinux/iso/2021.12.01/
  https://mirrors.xtom.ee/archlinux/iso/2021.12.01/
  https://mirror.cyberbits.asia/archlinux/iso/2021.12.01/
  https://mirror.moson.org/arch/iso/2021.12.01/
  https://mirror.phx1.us.spryservers.net/archlinux/iso/2021.12.01/
  https://arch.yhtez.xyz/iso/2021.12.01/
  https://mirror.2degrees.nz/archlinux/iso/2021.12.01/
  https://arch.powerfly.ca/iso/2021.12.01/
  https://ftp.ludd.ltu.se/mirrors/archlinux/iso/2021.12.01/
  https://mirror.jingk.ai/archlinux/iso/2021.12.01/
  https://mirrors.wsyu.edu.cn/archlinux/iso/2021.12.01/
  https://mirrors.radwebhosting.com/archlinux/iso/2021.12.01/
  https://mirror.theash.xyz/arch/iso/2021.12.01/
  https://mirror.cov.ukservers.com/archlinux/iso/2021.12.01/
  https://archlinux.astra.in.ua/iso/2021.12.01/
  https://ftp.psnc.pl/linux/archlinux/iso/2021.12.01/
  https://mirror.clarkson.edu/archlinux/iso/2021.12.01/
  https://mirror.repository.id/archlinux/iso/2021.12.01/
  https://repo.endpoint.ml/archlinux/iso/2021.12.01/
  https://mirrors.kamey.tk/archlinux/iso/2021.12.01/
  https://mirror.iusearchbtw.nl/iso/2021.12.01/
  https://mirrors.up.pt/pub/archlinux/iso/2021.12.01/
  https://mirror.bardia.tech/archlinux/iso/2021.12.01/
  https://de.arch.mirror.kescher.at/iso/2021.12.01/
  https://tick-tack.mynetgear.com/archlinux/iso/2021.12.01/
  https://archlinux.homeinfo.de/iso/2021.12.01/
  https://archlinux.ourhome.kiwi/iso/2021.12.01/'




#  main  ---------------------------------------------------------------  main


main  "$@"




#  vland.sh  -  Create and use chroot-style virtual software environments.
#
#  Copyright (c) 2021 Parke Bostrom, parke.nexus at gmail.com
#
#  This program is free software: you can redistribute it and/or
#  modify it under the terms of version 3 of the GNU General Public
#  License as published by the Free Software Foundation.
#
#  This program is distributed in the hope that it will be useful, but
#  WITHOUT ANY WARRANTY; without even the implied warranty of
#  MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See version 3
#  of the GNU General Public License for more details.
#
#  You should have received a copy of version 3 of the GNU General
#  Public License along with this program.  If not, see
#  <https://www.gnu.org/licenses/>.
