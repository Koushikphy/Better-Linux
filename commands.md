## File/folder listing

1. size of the current directory
```
du -shx
```

1. size of folders in current directory
```bash
du -d1 -h
du -d1 -h | sort -h   # sort according to size
```

1. list all files with size greater than 1GB (using [fd](https://github.com/Koushikphy/Better-Linux/blob/main/betterLinux.md#1-fd-replacement-for-find))
```bash
fd -S +1G
```

1. total size of all files named `data.dat`
```bash
fd data.dat -X du -ch
fd -e txt -X du -ch    # files with `txt` extension
```

1. compress all `.txt` files with `xz`
```bash
fd -e txt -j 1 -x xz # -j 1 wll launch one instance of `xz`
```

1. Find multiple patterns with `fd`
```bash
fd '(pattern1|pattern2)'
```

1. Find exact matches with `fd`
```bash
fd '^pattern$'
```

1. tree with file size
```
tree -h -d 1 --du /path/to/dir
```


## File copy/transfer

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


## File operations:
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


## miscellaneous


1. set date and time directly from terminal:
```
sudo date -s "$(wget -qSO- --max-redirect=0 google.in 2>&1 | grep Date: | cut -d' ' -f5-8)Z"
```


1. Check diskIO of processes
```
sudo iotop
```

1. use function with `xargs`
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

1. tail with timestamp
```
tail -f outputfile | xargs -IL date +"%I:%M:%S %p"
```


1. Quickly benchmark file I/O speed
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

1. Check location of some running command
```
ls -l /proc/<proc_id>/exe
```

1. Mount remote with `sshfs`
```bash
sshfs user@ip:remote_directory local_directory
```