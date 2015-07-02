# Introduction #

You may meet link error when buiding boost.python.


# Solution #
  * Open pyconfig.h, and remove the codes:
```
 #ifdef _DEBUG
...
 #endif
```