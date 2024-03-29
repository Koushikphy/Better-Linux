- [Useful commands](#useful-commands)
  - [File/folder listing](#filefolder-listing)
  - [File copy/transfer](#file-copytransfer)
  - [File operations](#file-operations)
  - [miscellaneous](#miscellaneous)
- [File \& Folder compression](#file--folder-compression)
  - [`gzip`](#gzip)
  - [`xz`](#xz)
  - [`tar`](#tar)
  - [`zip`](#zip)
- [Process Handling](#process-handling)
- [Linux utilities you should use instead](#linux-utilities-you-should-use-instead)
  - [`fd`: replacement for `find`](#fd-replacement-for-find)
  - [`exa`: replacement for `ls`](#exa-replacement-for-ls)
  - [`gdu`: replacement for `du`](#gdu-replacement-for-du)
  - [`fzz`: replacement for `Ctrl+R`(reverse-i-search)](#fzz-replacement-for-ctrlrreverse-i-search)
  - [`bat`: replacement for `cat`](#bat-replacement-for-cat)
  - [Terminal file managers](#terminal-file-managers)
- [Useful bash variables](#useful-bash-variables)
- [Bash color schemes](#bash-color-schemes)
- [Things to do after installing ubuntu](#things-to-do-after-installing-ubuntu)


## Useful commands
### File/folder listing

1. size of the current directory
```
du -shx
```

2. size of folders in current directory
```bash
du -d1 -h
du -d1 -h | sort -h   # sort according to size
```

3. list all files with size greater than 1GB (using [fd](https://github.com/Koushikphy/Better-Linux#fd-replacement-for-find))
```bash
fd -S +1G
```

4. total size of all files named `data.dat`
```bash
fd data.dat -X du -ch
fd -e txt -X du -ch    # files with `txt` extension
```

5. compress all `.txt` files with `xz`
```bash
fd -e txt -j 1 -x xz # -j 1 wll launch one instance of `xz`
```

6. Find multiple patterns with `fd`
```bash
fd '(pattern1|pattern2)'
```

7. Find exact matches with `fd`
```bash
fd '^pattern$'
```

8. tree with file size
```
tree -h -d 1 --du /path/to/dir
```


### File copy/transfer

1. Copy files with progress and speed shown:
```
rsync -zmavh --progress --stats
```

2. rsync only copy file with certain extension
copy files only with `.sh` [extension](https://stackoverflow.com/questions/11111562/rsync-copy-over-only-certain-types-of-files-using-include-option/11111793)
```
rsync -zarvm --include="*/" --include="*.sh" --exclude="*" "$from" "$to"
```


3. `rsync` only transfer certain files  
search the list of files with `fd` (or `find`) and send it using `rsync`
```bash
fd <search_pattern> | rsync -avm  --progress --stats --files-from=- . $dst
```


### File operations
1. Replace text in file without opening it
```
sed -i "s/string/replace/g" file
```
2. print two file side by side 
```
pr FILE1 FILE2 -m 
```
3. Print m to n lines of a file:
```
sed -n 'm,np' file
```


### miscellaneous


1. set date and time directly from terminal:
```
sudo date -s "$(wget -qSO- --max-redirect=0 google.in 2>&1 | grep Date: | cut -d' ' -f5-8)Z"
```


2. Check diskIO of processes
```
sudo iotop
```

3. use function with `xargs`
```
moveRun() {
  cwd=$(pwd)
  cd $1
  pwd
  cd $cwd
}

export -f moveRun
ls -d */ | xargs -n1 bash -c 'moveRun "$@"' _
```

4. tail with timestamp
```
tail -f outputfile | xargs -IL date +"%I:%M:%S %p"
```


5. Quickly benchmark file I/O speed
```
# clear cache
echo 3 | sudo tee /proc/sys/vm/drop_caches
# write speed
dd if=/dev/zero of=./output conv=fdatasync bs=384k count=1k
# read speed
dd if=./output of=/dev/null bs=384k count=1k
# clear
rm output
```

6. Check location of some running command
```
ls -l /proc/<proc_id>/exe
```

7. Mount remote with `sshfs`
```bash
sshfs user@ip:remote_directory local_directory
```

8. Wait for a process to finish with PID
```bash
tail --pid=$PID -f /dev/null
```


## File & Folder compression
### `gzip` 

```bash
gzip -kv -6 <filename>
#or
gzip -kvr -6 <directory>
```
`-k` : is to keep the original file after generating the compressed one, by default it removes the original file.   
`-v` is for `verbose`.   
`-6` is the (default) compression level, available number are from 1 to 9, 1 being the fastest and 9 meaning the best/slowest compression.   
For directory `-r` flag is used for recursive compression.  

Decompress the same with 
```bash
gzip -d file.gz
```

- Similar to `gzip` another compression utility `bzip2`. It has similar flags and usage like `gzip`  
- For parallel gzip use https://zlib.net/pigz/  




### `xz`
`xz`(`lzma` compression) is better alternative to `gzip` use it with 
```bash
xz -k -6 -T 3 --verbose <file>
```
`xz` supports parallel processing, use it with `-T 3` with 3 being number of processor.  

- For best possible compression of large files use [lrzip](https://github.com/ckolivas/lrzip)

- [A Quick Benchmark: Gzip vs. Bzip2 vs. LZMA](https://tukaani.org/lzma/benchmarks.html)  

- Which one to use?  
  * If you want to quickly compress the file and on a limited resource use `gzip`. 
  * If you want the best compression/ smallest file size and have enough RAM and processor available use `xz`  

<!--  - Useful utilities:  
 * Parallel gzip [pigz](https://zlib.net/pigz/)
  * Long range zip [lrzip](https://github.com/ckolivas/lrzip) -->


### `tar`   
`tar` by itself only archive multiple files or folder into one single file and doesn't compress anything. Then other compression utilities are used to compress the file. Gnu `tar` can handle this whole process on its own. To compress the `sample-folder` into `archive.tar.gz`
```bash
tar -zcvf archive.tar.gz sample-folder
```
`-z` : Use `gzip` to compress  
`-c` : Create archive  
`-v` : Verbose  
`-f` : Archive file name  
`-j` : Use `bzip2` utility  
`-J` : Use `xz` utility  
`-I` : Specify compression utility, `-I 'gzip -6'` is same as `-z`  
To decompress the file into the folder again replace the create (`-c`) with extract (`-x`) flag
```bash
tar -xvf archive.tar.gz
```  

To list the contents of a compressed file use:
```bash
tar -tf file.tar.gz
```


### `zip`  
```bash
zip output.zip [filename] [-r folder_name]
```
to unzip use  
```bash
unzip file.zip [-d destination_folder]
```


## Process Handling
| Command    |  Description|
| ----------- | ----------- 
|    `Ctrl+C`     | Kill the current forground processs. (Send SIGINT signale to the process) |
|    `Ctrl+Z`     | Suspend the current forground processs. (Send SIGSTOP signale to the process) | 
|    `kill <pid>`     | Send signall to a process. Default is SIGTERM. <br> Use `kill -l` to check the full list of available signal | 
|    `pkill <name>`     | Kill all processes with name "name" | 
|    `fg`    | Move jobs to foreground |
|    `bg`    | Move jobs to background |
|    `jobs -l`    | List all background jobs for the current shell. |
|    `pgrep -la <name>` <br> `top -bc -n1 \| grep <name>`   | List all jobs with name "name" |  



## Linux utilities you should use instead
Better and faster alternative to regularly use bash commands  

### `fd`: replacement for `find`
Install with `sudo apt install fd-find` or download the latest release from https://github.com/sharkdp/fd  
Preferred usage `fd (part of) file_name [-e extension] [-S size_specs] [-x executable {}] [-t d/f] [-E exclude]` 

---
### `exa`: replacement for `ls`  
Download latest release from https://github.com/ogham/exa  
Preferred usage `exa -l -T -L <depth> --no-user`  

---
### `gdu`: replacement for `du`
Install `gdu` from https://github.com/dundee/gdu#installation.
Preferred usage `gdu`


---
### `fzz`: replacement for `Ctrl+R`(reverse-i-search)
Install with `git clone --depth 1 https://github.com/junegunn/fzf.git ~/.fzf && ~/.fzf/install`  
Now source the `~/.bashrc` and it will replace the Ctrl+R, bash reverse with `fzf`

---
### `bat`: replacement for `cat` 
Download it from https://github.com/sharkdp/bat

---
### Terminal file managers
1. lf: https://github.com/gokcehan/lf
2. ranger: https://github.com/ranger/ranger
3. nnn: https://github.com/jarun/nnn


## Useful bash variables

- `$1`, `$2`, `$3`, ... are the [positional parameters][1].
- `"$@"` is an array-like construct of all positional parameters, `{$1, $2, $3 ...}`.
- `"$*"` is the IFS expansion of all positional parameters, `$1 $2 $3 ...`.
- `$#` is the number of positional parameters.
- `$-` current options set for the shell.
- `$$` pid of the current shell (not subshell).
- `$_` most recent parameter (or the abs path of the command to start the current shell immediately after startup).
- `$IFS` is the (input) field separator.
- `$?` is the most recent foreground pipeline exit status.
- `$!` is the PID of the most recent background command.
- `$0` is the name of the shell or shell script.

Most of the above can be found under [Special Parameters][2] in the Bash Reference Manual. There are all the [environment variables set by the shell][3].

## Bash color schemes
```bash
# Reset
Color_Off='\033[0m'       # Text Reset

# Regular Colors
Black='\033[0;30m'        # Black
Red='\033[0;31m'          # Red
Green='\033[0;32m'        # Green
Yellow='\033[0;33m'       # Yellow
Blue='\033[0;34m'         # Blue
Purple='\033[0;35m'       # Purple
Cyan='\033[0;36m'         # Cyan
White='\033[0;37m'        # White

# Bold
BBlack='\033[1;30m'       # Black
BRed='\033[1;31m'         # Red
BGreen='\033[1;32m'       # Green
BYellow='\033[1;33m'      # Yellow
BBlue='\033[1;34m'        # Blue
BPurple='\033[1;35m'      # Purple
BCyan='\033[1;36m'        # Cyan
BWhite='\033[1;37m'       # White

# Underline
UBlack='\033[4;30m'       # Black
URed='\033[4;31m'         # Red
UGreen='\033[4;32m'       # Green
UYellow='\033[4;33m'      # Yellow
UBlue='\033[4;34m'        # Blue
UPurple='\033[4;35m'      # Purple
UCyan='\033[4;36m'        # Cyan
UWhite='\033[4;37m'       # White

# Background
On_Black='\033[40m'       # Black
On_Red='\033[41m'         # Red
On_Green='\033[42m'       # Green
On_Yellow='\033[43m'      # Yellow
On_Blue='\033[44m'        # Blue
On_Purple='\033[45m'      # Purple
On_Cyan='\033[46m'        # Cyan
On_White='\033[47m'       # White

# High Intensity
IBlack='\033[0;90m'       # Black
IRed='\033[0;91m'         # Red
IGreen='\033[0;92m'       # Green
IYellow='\033[0;93m'      # Yellow
IBlue='\033[0;94m'        # Blue
IPurple='\033[0;95m'      # Purple
ICyan='\033[0;96m'        # Cyan
IWhite='\033[0;97m'       # White

# Bold High Intensity
BIBlack='\033[1;90m'      # Black
BIRed='\033[1;91m'        # Red
BIGreen='\033[1;92m'      # Green
BIYellow='\033[1;93m'     # Yellow
BIBlue='\033[1;94m'       # Blue
BIPurple='\033[1;95m'     # Purple
BICyan='\033[1;96m'       # Cyan
BIWhite='\033[1;97m'      # White

# High Intensity backgrounds
On_IBlack='\033[0;100m'   # Black
On_IRed='\033[0;101m'     # Red
On_IGreen='\033[0;102m'   # Green
On_IYellow='\033[0;103m'  # Yellow
On_IBlue='\033[0;104m'    # Blue
On_IPurple='\033[0;105m'  # Purple
On_ICyan='\033[0;106m'    # Cyan
On_IWhite='\033[0;107m'   # White

```


For a comprehensive index, please see the [Reference Manual Variable Index][4].  
Reference : [StackOverflow][5]

  [1]: https://www.gnu.org/software/bash/manual/html_node/Positional-Parameters.html
  [2]: https://www.gnu.org/software/bash/manual/html_node/Special-Parameters.html
  [3]: https://www.gnu.org/software/bash/manual/html_node/Shell-Variables.html
  [4]: https://www.gnu.org/software/bash/manual/html_node/Variable-Index.html
  [5]: https://stackoverflow.com/questions/5163144/what-are-the-special-dollar-sign-shell-variables


## Things to do after installing ubuntu
- [Things to do after installing ubuntu](afterInstallUbuntu.md)
