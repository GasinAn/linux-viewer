# linux-viewer
Simple and Stupid Linux FILE viewer applying corresponding methods to view different kinds of FILEs

```bash
alias ls='ls --color=auto'
alias ll='ls -alF'

view() {
  while [ $# -gt 0 ]; do
    if [ -L $1 ]; then
      file $1
      target=$(readlink $1)
      if [ ${target:0:1} == '/' ]; then
        view $target
      else
        view $(dirname $1)/$target
      fi

    else
      if [ ! -e $1 ]; then
        echo "\`$1' does not exist!"

      elif [ -f $1 ]; then
        if [[ $(file $1) =~ 'text' ]]; then
          if [ $(tail -n 1 $1 | wc -l) -eq 1 ]; then
            file $1
            cat $1
          else
            echo "\`$1' has no line terminator at the end!"
            file $1
            cat $1
            echo ""
          fi

        else
          file $1
        fi

      elif [ -d $1 ]; then
        file $1
        ll $1

      else
        file $1
      fi
    fi

    if [ $# -gt 1 ]; then
      echo ""
    fi

    shift
  done
}
```

## Examples
```
gasinan:~/linux-viewer$ view files_for_testing/ files_for_testing/* /bin/bash /dev/null
files_for_testing/: directory
total 16
drwxr-xr-x 2 gasinan gasinan 4096 Jan  1 00:00 ./
drwxr-xr-x 4 gasinan gasinan 4096 Jan  1 00:00 ../
-rw-r--r-- 1 gasinan gasinan   39 Jan  1 00:00 badfile
-rw-r--r-- 1 gasinan gasinan   42 Jan  1 00:00 goodfile
lrwxrwxrwx 1 gasinan gasinan   17 Jan  1 00:00 link2badfile -> link2link2badfile
lrwxrwxrwx 1 gasinan gasinan    8 Jan  1 00:00 link2goodfile -> goodfile
lrwxrwxrwx 1 gasinan gasinan   13 Jan  1 00:00 link2link2goodfile -> link2goodfile

`files_for_testing/badfile' has no line terminator at the end!
files_for_testing/badfile: ASCII text
line 1 in `badfile'
line 2 in `badfile'

files_for_testing/goodfile: ASCII text
line 1 in `goodfile'
line 2 in `goodfile'

files_for_testing/link2badfile: broken symbolic link to link2link2badfile
`files_for_testing/link2link2badfile' does not exist!

files_for_testing/link2goodfile: symbolic link to goodfile
files_for_testing/goodfile: ASCII text
line 1 in `goodfile'
line 2 in `goodfile'

files_for_testing/link2link2goodfile: symbolic link to link2goodfile
files_for_testing/link2goodfile: symbolic link to goodfile
files_for_testing/goodfile: ASCII text
line 1 in `goodfile'
line 2 in `goodfile'

/bin/bash: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa, for GNU/Linux 0.0.0, stripped

/dev/null: character special (1/3)
```

## License

[GLWT(Good Luck With That) Public License](https://github.com/GasinAn/linux-viewer/blob/main/LICENSE)
