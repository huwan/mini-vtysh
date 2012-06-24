##
## Configure template file for Zebra.
## autoconf will generate configure script.
##
##  Copyright (c) 1996, 97, 98, 99, 2000 Kunihiro Ishiguro <kunihiro@zebra.org>
##
AC_PREREQ(2.53)

AC_INIT(lib/zebra.h)
AM_INIT_AUTOMAKE(zebra, 0.95)
AM_CONFIG_HEADER(config.h)

dnl -----------------------------------
dnl Get hostname and other information.
dnl -----------------------------------
AC_CANONICAL_HOST

dnl ------------
dnl Check CFLAGS
dnl ------------
AC_ARG_WITH(cflags,
[  --with-cflags           Set CFLAGS for use in compilation.])
if test "x$with_cflags" != "x" ; then
  CFLAGS="$with_cflags" ; cflags_specified=yes ;
elif test -n "$CFLAGS" ; then
  cflags_specified=yes ;
fi

dnl ------------
dnl Check LINKBASE
dnl ------------
AC_ARG_WITH(linkbase,
[  --with-linkbase         Set local library base for use in compilation.])
if test "x$with_linkbase" != "x" ; then
  linkbase_specified=yes ;
fi

dnl --------
dnl Check CC
dnl --------
AC_PROG_CC

dnl -----------------------------------------
dnl If CLFAGS doesn\'t exist set default value
dnl -----------------------------------------
if test "x$cflags_specified" = "x" ; then
  CFLAGS="$CFLAGS -Wall"
fi

dnl -----------------------------------------
dnl If linkbase specified expand it
dnl -----------------------------------------
if test "$linkbase_specified" = "yes" ; then
  CFLAGS="$CFLAGS -I${with_linkbase}/include" ;
  LIBS="$LIBS -L${with_linkbase}/lib" ;
fi

dnl --------------
dnl Check programs
dnl --------------
AC_PROG_CPP
AC_PROG_INSTALL
AC_PROG_MAKE_SET
AC_CHECK_TOOL(AR, ar)
AC_CHECK_TOOL(RANLIB, ranlib, :)

dnl ---------
dnl AIX check
dnl ---------
AC_AIX

dnl ----------------------
dnl Packages configuration
dnl ----------------------
#AC_ARG_ENABLE(vtysh,
#[  --enable-vtysh       Do not make integrated VTY version of zebra])

AC_ARG_WITH(libpam,
[  --with-libpam           use libpam for PAM support in vtysh])

dnl -------------------
dnl Check header files.
dnl -------------------
AC_STDC_HEADERS
AC_CHECK_HEADERS(string.h stropts.h sys/conf.h sys/ksym.h sys/time.h sys/times.h sys/select.h sys/sysctl.h sys/sockio.h sys/types.h net/if_dl.h net/if_var.h linux/version.h kvm.h netdb.h netinet/in.h net/netopt.h netinet/in_var.h netinet/in6_var.h netinet/in6.h inet/nd.h asm/types.h netinet/icmp6.h netinet6/nd6.h libutil.h)

dnl check some types
AC_C_CONST
dnl AC_TYPE_PID_T
AC_TYPE_SIGNAL

dnl Some systems (Solaris 2.x) require libnsl (Network Services Library)
case "$host" in
  *-sunos5.6* | *-solaris2.6*)
      opsys=sol2-6
      AC_DEFINE(SUNOS_5,,SunOS 5)
      AC_CHECK_LIB(xnet, main)
      CURSES=-lcurses
  ;;
  *-sunos5* | *-solaris2*)
      AC_DEFINE(SUNOS_5,,SunOS 5)
      AC_CHECK_LIB(socket, main)
      AC_CHECK_LIB(nsl, main)
      CURSES=-lcurses
  ;;
  *-linux-*)
      opsys=gnu-linux
      AC_DEFINE(GNU_LINUX,,GNU Linux)
  ;;
  *-nec-sysv4*)
      AC_CHECK_LIB(nsl, gethostbyname)
      AC_CHECK_LIB(socket, socket)
  ;;
  *-freebsd3.2)
      AC_DEFINE(FREEBSD_32,,FreeBSD 3.2)
  ;;
  *-openbsd*)
      opsys=openbsd
      AC_DEFINE(OPEN_BSD,,OpenBSD)
  ;;
  *-bsdi*)
      opsys=bsdi
      OTHER_METHOD="mtu_kvm.o"
      AC_CHECK_LIB(kvm, main)
  ;;
esac

dnl ---------------------
dnl Integrated VTY option
dnl ---------------------
#case "${enable_vtysh}" in
#  "yes")
    VTYSH="vtysh";
    AC_DEFINE(VTYSH,,VTY shell)
    AC_CHECK_LIB(tinfo, tputs, , AC_CHECK_LIB(ncurses, tputs, ,
                 AC_CHECK_LIB(termcap, tputs)))
    AC_CHECK_LIB(readline, main)
    if test $ac_cv_lib_readline_main = no; then
      AC_MSG_ERROR([vtysh needs libreadline but was not found on your system.])
    fi
    AC_CHECK_HEADER(readline/history.h)
    if test $ac_cv_header_readline_history_h = no;then
      AC_MSG_ERROR([readline is too old to have readline/history.h, please update to the latest readline library.])
    fi
#    ;;
#  "no")
#    VTYSH=""
#    ;;
#  *)
#    ;;
#esac

dnl ----------
dnl PAM module
dnl ----------
if test "$with_libpam" = "yes"; then
dnl took this test from proftpd's configure.in and suited to our needs
dnl -------------------------------------------------------------------------
dnl
dnl This next check looks funky due to a linker problem with some versions
dnl of the PAM library.  Prior to 0.72 release, the Linux PAM shared library
dnl omitted requiring libdl linking information. PAM-0.72 or better ships
dnl with RedHat 6.2 and Debian 2.2 or better.
AC_CHECK_LIB(pam, pam_start,
  [AC_CHECK_LIB(pam, misc_conv,
    [AC_DEFINE(USE_PAM,,Use PAM for authentication)
     LIBPAM="-lpam"],
    [AC_DEFINE(USE_PAM,,Use PAM for authentication)
     LIBPAM="-lpam -lpam_misc"]
    )
  ],

  [AC_CHECK_LIB(pam, pam_end,
    [AC_CHECK_LIB(pam, misc_conv,
      [AC_DEFINE(USE_PAM)
       LIBPAM="-lpam -ldl"],
      [AC_DEFINE(USE_PAM)
       LIBPAM="-lpam -ldl -lpam_misc"]
     )
  ],AC_MSG_WARN([*** pam support will not be built ***]),
  [-ldl])
  ]
)
fi
AC_SUBST(LIBPAM)

dnl -------------------------------
dnl Endian-ness check
dnl -------------------------------
AC_WORDS_BIGENDIAN

dnl -------------------------------
dnl check the size in byte of the C
dnl -------------------------------
dnl AC_CHECK_SIZEOF(char)
dnl AC_CHECK_SIZEOF(int)
dnl AC_CHECK_SIZEOF(short)
dnl AC_CHECK_SIZEOF(long)

dnl ----------------------------
dnl check existance of functions
dnl ----------------------------
AC_CHECK_FUNCS(bcopy bzero strerror inet_aton daemon snprintf vsnprintf strlcat strlcpy if_nametoindex if_indextoname getifaddrs)
AC_CHECK_FUNCS(setproctitle, ,[AC_CHECK_LIB(util, setproctitle, [LIBS="$LIBS -lutil"; AC_DEFINE(HAVE_SETPROCTITLE)])])

dnl ------------------------------------
dnl Determine routing get and set method
dnl ------------------------------------
AC_MSG_CHECKING(zebra between kernel interface method)
if test x"$opsys" = x"gnu-linux"; then
  if test "${enable_netlink}" = "yes";then
    AC_MSG_RESULT(netlink)
    RT_METHOD=rt_netlink.o
    AC_DEFINE(HAVE_NETLINK,,netlink)
    netlink=yes
  elif test "${enable_netlink}" = "no"; then
    AC_MSG_RESULT(ioctl)
    RT_METHOD=rt_ioctl.o
    netlink=no
  else
    AC_MSG_RESULT(netlink)
    RT_METHOD=rt_netlink.o
    AC_DEFINE(HAVE_NETLINK,,netlink)
    netlink=yes
  fi
else
  if test "$opsys" = "sol2-6";then
    AC_MSG_RESULT(solaris)
    KERNEL_METHOD="kernel_socket.o"
    RT_METHOD="rt_socket.o"
  else
    AC_TRY_RUN([#include <errno.h>
#include <sys/types.h>
#include <sys/socket.h>

main ()
{
  int ac_sock;

  ac_sock = socket (AF_ROUTE, SOCK_RAW, 0);
  if (ac_sock < 0 && errno == EINVAL)
    exit (1);
  exit (0);
}],
  [KERNEL_METHOD=kernel_socket.o
   RT_METHOD=rt_socket.o
   AC_MSG_RESULT(socket)],
  [RT_METHOD=rt_ioctl.o
   AC_MSG_RESULT(ioctl)],
  [KERNEL_METHOD=kernel_socket.o
   RT_METHOD=rt_socket.o
   AC_MSG_RESULT(socket)])
  fi
fi
AC_SUBST(RT_METHOD)
AC_SUBST(KERNEL_METHOD)
AC_SUBST(OTHER_METHOD)

dnl -----------------------------
dnl check interface lookup method
dnl -----------------------------
AC_MSG_CHECKING(interface looking up method)
if test "$netlink" = yes; then
  AC_MSG_RESULT(netlink)
  IF_METHOD=if_netlink.o
else
  if test "$opsys" = "sol2-6";then
    AC_MSG_RESULT(solaris)
    IF_METHOD=if_ioctl.o
  elif test "$opsys" = "openbsd";then
    AC_MSG_RESULT(openbsd)
    IF_METHOD=if_ioctl.o
  elif grep NET_RT_IFLIST /usr/include/sys/socket.h >/dev/null 2>&1; then
    AC_MSG_RESULT(sysctl)
    IF_METHOD=if_sysctl.o
    AC_DEFINE(HAVE_NET_RT_IFLIST,,NET_RT_IFLIST)
  else
    AC_MSG_RESULT(ioctl)
    IF_METHOD=if_ioctl.o
  fi
fi
AC_SUBST(IF_METHOD)

dnl -----------------------
dnl check proc file system.
dnl -----------------------
if test -r /proc/net/dev; then
  AC_DEFINE(HAVE_PROC_NET_DEV,,/proc/net/dev)
  IF_PROC=if_proc.o
fi

if test -r /proc/net/if_inet6; then
  AC_DEFINE(HAVE_PROC_NET_IF_INET6,,/proc/net/if_inet6)
  IF_PROC=if_proc.o
fi
AC_SUBST(IF_PROC)

AC_CHECK_FUNCS(getaddrinfo, [have_getaddrinfo=yes], [have_getaddrinfo=no])

dnl ----------
dnl IPv6 check
dnl ----------
AC_MSG_CHECKING(whether does this OS have IPv6 stack)
if test "${enable_ipv6}" = "no"; then
  AC_MSG_RESULT(disabled)
else
dnl ----------
dnl INRIA IPv6
dnl ----------
if grep IPV6_INRIA_VERSION /usr/include/netinet/in.h >/dev/null 2>&1; then
   zebra_cv_ipv6=yes
   AC_DEFINE(HAVE_IPV6,,IPv6)
   AC_DEFINE(INRIA_IPV6,,Inria IPv6)
   RIPNGD="ripngd"
   OSPF6D="ospf6d"
   LIB_IPV6=""
   AC_MSG_RESULT(INRIA IPv6)
fi
dnl ---------
dnl KAME IPv6
dnl ---------
if grep WIDE /usr/include/netinet6/in6.h >/dev/null 2>&1; then
   zebra_cv_ipv6=yes
   AC_DEFINE(HAVE_IPV6,,IPv6)
   AC_DEFINE(KAME,,KAME IPv6)
   RIPNGD="ripngd"
   OSPF6D="ospf6d"
   if test -d /usr/local/v6/lib -a -f /usr/local/v6/lib/libinet6.a; then
      LIB_IPV6="-L/usr/local/v6/lib -linet6"
   fi
   AC_MSG_RESULT(KAME)
fi
dnl ---------
dnl NRL check
dnl ---------
if grep NRL /usr/include/netinet6/in6.h >/dev/null 2>&1; then
   zebra_cv_ipv6=yes
   AC_DEFINE(HAVE_IPV6,,IPv6)
   AC_DEFINE(NRL,,NRL)
   RIPNGD="ripngd"
   OSPF6D="ospf6d"
   if test x"$opsys" = x"bsdi";then
      AC_DEFINE(BSDI_NRL,,BSDI)
      AC_MSG_RESULT(BSDI_NRL)
   else
      AC_MSG_RESULT(NRL)
   fi
fi

dnl ----------
dnl Linux IPv6
dnl ----------
if test "${enable_ipv6}" = "yes"; then
   AC_EGREP_CPP(yes, [
   #include <linux/version.h>
   /* 2.1.128 or later */
   #if LINUX_VERSION_CODE >= 0x020180
   yes
   #endif],
   [zebra_cv_ipv6=yes; zebra_cv_linux_ipv6=yes;AC_MSG_RESULT(Linux IPv6)])
else
   if test x`ls /proc/net/ipv6_route 2>/dev/null` = x"/proc/net/ipv6_route"
   then
      zebra_cv_ipv6=yes
      zebra_cv_linux_ipv6=yes
      AC_MSG_RESULT(Linux IPv6)
   fi
fi

if test "$zebra_cv_linux_ipv6" = "yes";then
   AC_DEFINE(HAVE_IPV6)
   AC_MSG_CHECKING(for GNU libc 2.1)
   AC_EGREP_CPP(yes, [
#include <features.h>
#if __GLIBC__ >= 2 && __GLIBC_MINOR__ >= 1
  yes
#endif], [glibc=yes; AC_MSG_RESULT(yes)], AC_MSG_RESULT(no))
   AC_DEFINE(LINUX_IPV6,,Linux IPv6)   
   RIPNGD="ripngd"
   OSPF6D="ospf6d"
   if test "$glibc" != "yes"; then
      INCLUDES="-I/usr/inet6/include"
      if test x`ls /usr/inet6/lib/libinet6.a 2>/dev/null` != x;then
         LIB_IPV6="-L/usr/inet6/lib -linet6"
      fi
   fi
fi

dnl -----------------------
dnl Set IPv6 related values
dnl -----------------------
LIBS="$LIB_IPV6 $LIBS"
AC_SUBST(LIB_IPV6)

if test x"$RIPNGD" = x""; then
  AC_MSG_RESULT(IPv4 only)
fi
fi

dnl --------------------
dnl Daemon disable check
dnl --------------------

AC_SUBST(VTYSH)
AC_SUBST(INCLUDES)
AC_SUBST(CURSES)
AC_CHECK_LIB(c, inet_ntop, [AC_DEFINE(HAVE_INET_NTOP,,inet_ntop)])
AC_CHECK_LIB(c, inet_pton, [AC_DEFINE(HAVE_INET_PTON,,inet_pton)])
AC_CHECK_LIB(crypt, crypt)
AC_CHECK_LIB(resolv, res_init)
AC_CHECK_LIB(m, main)

dnl ---------------------------------------------------
dnl BSD/OS 4.1 define inet_XtoY function as __inet_XtoY
dnl ---------------------------------------------------
AC_CHECK_FUNC(__inet_ntop, AC_DEFINE(HAVE_INET_NTOP))
AC_CHECK_FUNC(__inet_pton, AC_DEFINE(HAVE_INET_PTON))
AC_CHECK_FUNC(__inet_aton, AC_DEFINE(HAVE_INET_ATON))

dnl ---------------------------
dnl check system has GNU regexp
dnl ---------------------------
dnl AC_MSG_CHECKING(whether system has GNU regex)
AC_CHECK_LIB(c, regexec,
[AC_DEFINE(HAVE_GNU_REGEX,,GNU regexp library)
 LIB_REGEX=""],
[LIB_REGEX="regex.o"])
AC_SUBST(LIB_REGEX)

dnl ----------------------------
dnl check sa_len of sockaddr
dnl ----------------------------
AC_MSG_CHECKING(whether struct sockaddr has a sa_len field)
AC_TRY_COMPILE([#include <sys/types.h>
#include <sys/socket.h>
],[static struct sockaddr ac_i;int ac_j = sizeof (ac_i.sa_len);],
[AC_MSG_RESULT(yes)
 AC_DEFINE(HAVE_SA_LEN,,sa_len)],
 AC_MSG_RESULT(no))

dnl ----------------------------
dnl check sin_len of sockaddr_in
dnl ----------------------------
AC_MSG_CHECKING(whether struct sockaddr_in has a sin_len field)
AC_TRY_COMPILE([#include <sys/types.h>
#include <netinet/in.h>
],[static struct sockaddr_in ac_i;int ac_j = sizeof (ac_i.sin_len);],
[AC_MSG_RESULT(yes)
 AC_DEFINE(HAVE_SIN_LEN,,sin_len)],
 AC_MSG_RESULT(no))

dnl ----------------------------
dnl check sun_len of sockaddr_un
dnl ----------------------------
AC_MSG_CHECKING(whether struct sockaddr_un has a sun_len field)
AC_TRY_COMPILE([#include <sys/types.h>
#include <sys/un.h>
],[static struct sockaddr_un ac_i;int ac_j = sizeof (ac_i.sun_len);],
[AC_MSG_RESULT(yes)
 AC_DEFINE(HAVE_SUN_LEN,,sun_len)],
 AC_MSG_RESULT(no))

dnl -----------------------------------
dnl check sin6_scope_id of sockaddr_in6
dnl -----------------------------------
if test "$zebra_cv_ipv6" = yes; then
  AC_MSG_CHECKING(whether struct sockaddr_in6 has a sin6_scope_id field)
  AC_TRY_COMPILE([#include <sys/types.h>
#include <netinet/in.h>
],[static struct sockaddr_in6 ac_i;int ac_j = sizeof (ac_i.sin6_scope_id);],
[AC_MSG_RESULT(yes)
 AC_DEFINE(HAVE_SIN6_SCOPE_ID,,scope id)],
 AC_MSG_RESULT(no))
fi

dnl -----------------------------------
dnl check in6addr_loopback global variable
dnl -----------------------------------
if test "$zebra_cv_ipv6" = yes; then
  AC_MSG_CHECKING(whether in6addr_loopback is usable)
  AC_TRY_COMPILE([#include <sys/types.h>
#include <netinet/in.h>
],[static struct in6_addr ac_i; ac_i = in6addr_loopback;],
[AC_MSG_RESULT(yes)
 AC_DEFINE(HAVE_IN6ADDR_GLOBAL,,in6addr global variables e.g. in6addr_loopback)],
 AC_MSG_RESULT(no))
fi

dnl ----------------------------
dnl check socklen_t exist or not
dnl ----------------------------
AC_MSG_CHECKING(whther socklen_t is defined)
AC_TRY_COMPILE([#include <sys/types.h>
#include <sys/socket.h>
#include <netinet/in.h>
],[socklen_t ac_x;],
[AC_MSG_RESULT(yes)
 AC_DEFINE(HAVE_SOCKLEN_T,,socklen_t)],
 AC_MSG_RESULT(no))

dnl ------------------------
dnl check struct sockaddr_dl
dnl ------------------------
AC_MSG_CHECKING(whether struct sockaddr_dl exist)
AC_EGREP_HEADER(sockaddr_dl,
net/if_dl.h,
[AC_MSG_RESULT(yes)
 AC_DEFINE(HAVE_SOCKADDR_DL,,sockaddr_dl)],
 AC_MSG_RESULT(no))

dnl --------------------------
dnl check structure ifaliasreq
dnl --------------------------
AC_MSG_CHECKING(whether struct ifaliasreq exist)
AC_EGREP_HEADER(ifaliasreq,
net/if.h,
[AC_MSG_RESULT(yes)
 AC_DEFINE(HAVE_IFALIASREQ,,ifaliasreq)],
 AC_MSG_RESULT(no))

dnl ----------------------------
dnl check structure in6_aliasreq
dnl ----------------------------
AC_MSG_CHECKING(whether struct if6_aliasreq exist)
AC_EGREP_HEADER(in6_aliasreq,
netinet6/in6_var.h,
[AC_MSG_RESULT(yes)
 AC_DEFINE(HAVE_IN6_ALIASREQ,,in6_aliasreq)],
 AC_MSG_RESULT(no))

dnl ---------------------------
dnl check structure rt_addrinfo
dnl ---------------------------
AC_MSG_CHECKING(whether struct rt_addrinfo exist)
AC_EGREP_HEADER(rt_addrinfo,
net/route.h,
[AC_MSG_RESULT(yes)
 AC_DEFINE(HAVE_RT_ADDRINFO,,rt_addrinfo)],
 AC_MSG_RESULT(no))

dnl --------------------------
dnl check structure in_pktinfo
dnl --------------------------
AC_MSG_CHECKING(whether struct in_pktinfo exist)
AC_TRY_COMPILE([#include <netinet/in.h>
],[struct in_pktinfo ac_x;],
[AC_MSG_RESULT(yes)
 AC_DEFINE(HAVE_INPKTINFO,,in_pktinfo)],
 AC_MSG_RESULT(no))

dnl --------------------------------------
dnl checking for getrusage struct and call
dnl --------------------------------------
AC_MSG_CHECKING(whether getrusage is available)
AC_TRY_COMPILE([#include <sys/resource.h>
],[struct rusage ac_x; getrusage (RUSAGE_SELF, &ac_x);],
[AC_MSG_RESULT(yes)
 AC_DEFINE(HAVE_RUSAGE,,rusage)],
 AC_MSG_RESULT(no))

dnl -------------
dnl check version
dnl -------------
file="${srcdir}/lib/version.h"
VERSION=`sed -ne 's/^#.*ZEBRA_VERSION.*\"\([^\"]*\)\"$/\1/p' $file`
AC_SUBST(VERSION)


dnl ---------------------------
dnl Check htonl works correctly
dnl ---------------------------
AC_MSG_CHECKING(for working htonl)
AC_CACHE_VAL(ac_cv_htonl_works, [
AC_TRY_LINK([#ifdef HAVE_SYS_TYPES_H
#include <sys/types.h>
#endif
#ifdef HAVE_NETDB_H
#include <netdb.h>
#endif
#ifdef HAVE_NETINET_IN_H
#include <netinet/in.h>
#endif],
[htonl (0);],
ac_cv_htonl_works=yes,
ac_cv_htonl_works=no)])
AC_MSG_RESULT($ac_cv_htonl_works)

AC_OUTPUT(Makefile lib/Makefile vtysh/Makefile doc/Makefile)

echo "
zebra configuration
-------------------
zebra version           : ${VERSION}
host operationg system  : ${host_os}
source code location    : ${srcdir}
compiler                : ${CC}
compiler flags          : ${CFLAGS}
link libraries          : ${LIBS}
"
