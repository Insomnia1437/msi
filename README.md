# MSI

## Desc

Original Description [https://epics.anl.gov/extensions/msi/index.php](https://epics.anl.gov/extensions/msi/index.php)

This project fix a bug which happens when substituting a template file.

Error happens:

1. when the comment character `#` is not the first character in a line.
2. when there is a trailing comment

## feature

Added two function
- `ltrim()`: remove the heading spaces
- `trimcomment()`: remove the trailing comment

## example

in file `mysub`
```
# Created by sdcswd@gmail.com
# on 2020-05-24

file "mytest.db" { # trail comment
## some normal comments
    ## this kind of comment is not allowed by original msi-1.7
    {USER = "sdcswd", HOST = "atari"} # another kind of trail comment
}
```

Output in EPICS 3.14.12 msi, installed by extension
```
$ /usr/users/control/epics/R3.14.12/extensions/bin/linux-x86_64/msi  -S mysub
substitution file mysub line 6:     ## this kind of comment is not allowed by original msi-1.7
Expecting pattern

```

Output in EPICS 3.15 or higher, (msi is a built-in tool in EPICS Base)
```
$ /usr/users/control/epics/R3.15.5/base/bin/linux-x86_64/msi -S mysub
msi: Parse error, expecting pattern
  in substitution file 'mysub' at line 7:
      ## this kind of comment is not allowed by original msi-1.7

```

Another kind of error:
```
$ /usr/users/sdcswd/epics/R3.15.5/base/bin/linux-x86_64/msi -S mysub
msi: Parse error, expecting pattern
  in substitution file 'mysub' at line 5:
  file "mytest.db" { # trail comment

```

Using this bug-fix version:
```
$ ~/epics/R3.14.12.8/extensions/bin/linux-x86_64/msi -S mysub
## comment
#
# Created by sdcswd@gmail.com
# on 2020-05-24
#
##

record(ai, "sdcswd:atari") {
    field(DESC, "description")
    field(DTYP, "Soft Channel stream")
    field(SCAN, "1 second")
    field(INP,  "atari")
}
```

Works.

