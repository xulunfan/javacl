# Converting from C code #

  * remove static qualifier
  * qualify pointers as either global or local (and don't change the qualification)
  * ban double values, use float values instead (many devices don't support doubles)