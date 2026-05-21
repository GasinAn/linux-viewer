# viewer
Simple and Stupid Linux FILE viewer applying corresponding methods to view different kinds of FILEs

```bash
alias ls='ls --color=auto'
alias ll='ls -alhF'
view() {
    if [ "$#" -eq 0 ]; then
        view .

    else
        while [ "$#" -gt 0 ]; do
            if [ ! -e "$1" ] && [ ! -L "$1" ]; then
                echo "\"$1\" does not exist!"

            else
                local res=$(file -- "$1")
                local res_tail=${res#$1}

                if [ -L "$1" ]; then
                    local target=$(readlink -- "$1")
                    echo "\"$1\"${res_tail/$target/\"$target\"}"
                    if [ ${target:0:1} == '/' ]; then
                        view "$target"
                    else
                        local dir=$(dirname -- "$1")
                        if [ "$dir" != '/' ]; then
                            view "$dir"/"$target"
                        else
                            view /"$target"
                        fi
                    fi

                elif [ -f "$1" ]; then
                    if [[ "${res_tail,,}" =~ 'text' ]]; then
                        if [ $(tail -n 1 -- "$1" | wc -l --) -eq 1 ]; then
                            echo "\"$1\"${res_tail/$target/\"$target\"}"
                            cat -- "$1"
                        else
                            echo "\"$1\" has no line terminator at the end!"
                            echo "\"$1\"${res_tail/$target/\"$target\"}"
                            cat -- "$1"
                            echo ""
                        fi
                    else
                        echo "\"$1\"${res_tail/$target/\"$target\"}"
                    fi

                elif [ -d "$1" ]; then
                    echo "\"$1\"${res_tail/$target/\"$target\"}"
                    ll -- "$1"

                else
                    echo "\"$1\"${res_tail/$target/\"$target\"}"
                fi
            fi

            if [ "$#" -gt 1 ]; then
                echo ""
            fi

            shift
        done
    fi
}
```

## Examples
```
gasinan:~/linux-viewer$ view files_for_testing/ ./files_for_testing/* /bin/bash /dev/null
"files_for_testing/": directory
total 16K
drwxr-xr-x 2 gasinan gasinan 4.0K Jan  1 00:00  ./
drwxr-xr-x 4 gasinan gasinan 4.0K Jan  1 00:00  ../
-rw-r--r-- 1 gasinan gasinan   41 Jan  1 00:00 'bad file'
-rw-r--r-- 1 gasinan gasinan   44 Jan  1 00:00 'good file'
lrwxrwxrwx 1 gasinan gasinan    9 Jan  1 00:00 'link 2 good file' -> 'good file'
lrwxrwxrwx 1 gasinan gasinan   15 Jan  1 00:00 'link 2 link 2 bad file' -> 'link 2 bad file'
lrwxrwxrwx 1 gasinan gasinan   16 Jan  1 00:00 'link 2 link 2 good file' -> 'link 2 good file'

"./files_for_testing/bad file" has no line terminator at the end!
"./files_for_testing/bad file": ASCII text
line 1 in "bad file"
line 2 in "bad file"

"./files_for_testing/good file": ASCII text
line 1 in "good file"
line 2 in "good file"

"./files_for_testing/link 2 good file": symbolic link to "good file"
"./files_for_testing/good file": ASCII text
line 1 in "good file"
line 2 in "good file"

"./files_for_testing/link 2 link 2 bad file": broken symbolic link to "link 2 bad file"
"./files_for_testing/link 2 bad file" does not exist!

"./files_for_testing/link 2 link 2 good file": symbolic link to "link 2 good file"
"./files_for_testing/link 2 good file": symbolic link to "good file"
"./files_for_testing/good file": ASCII text
line 1 in "good file"
line 2 in "good file"

"/bin/bash": ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa, for GNU/Linux 0.0.0, stripped

"/dev/null": character special (1/3)
```

## License

[GLWT(Good Luck With That) Public License](https://github.com/GasinAn/linux-viewer/blob/main/LICENSE)
