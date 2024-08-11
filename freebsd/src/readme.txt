the parameter " -Wno-error=implicit-function-declaration" to bypass the error:

In file included from /usr/src/sys/dev/bxe/bxe.c:35:
/usr/src/sys/dev/bxe/ecore_init_ops.h:853:23: error: call to undeclared function 'bxe_ilog2'; ISO C99 and later do not support implicit function declarations [-Werror,-Wimplicit-function-declaration]
  853 |                 REG_WR(sc, psz_reg, ILOG2(ilt_cli->page_size >> 12));
      |                                     ^
/usr/src/sys/dev/bxe/ecore_sp.h:162:21: note: expanded from macro 'ILOG2'
  162 | #define ILOG2(x)    bxe_ilog2(x)
      |                     ^
/usr/src/sys/dev/bxe/bxe.c:5846:11: error: call to undeclared function 'ilog2'; ISO C99 and later do not support implicit function declarations [-Werror,-Wimplicit-function-declaration]
 5846 |           ilog2(ilt_client->page_size >> 12));
      |           ^
/usr/src/sys/dev/bxe/bxe.c:5867:15: error: call to undeclared function 'ilog2'; ISO C99 and later do not support implicit function declarations [-Werror,-Wimplicit-function-declaration]
 5867 |               ilog2(ilt_client->page_size >> 12));
      |               ^
/usr/src/sys/dev/bxe/bxe.c:5885:15: error: call to undeclared function 'ilog2'; ISO C99 and later do not support implicit function declarations [-Werror,-Wimplicit-function-declaration]
 5885 |               ilog2(ilt_client->page_size >> 12));
      |               ^
/usr/src/sys/dev/bxe/bxe.c:5901:15: error: call to undeclared function 'ilog2'; ISO C99 and later do not support implicit function declarations [-Werror,-Wimplicit-function-declaration]
 5901 |               ilog2(ilt_client->page_size >> 12));
      |               ^
5 errors generated.
*** Error code 1
