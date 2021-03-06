## Compress files
### Using `gzip`: 

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

Similar to `gzip` another compression utility `bzip2`. It has similar flags and usage like `gzip`  
For parallel gzip use https://zlib.net/pigz/  



---
### Using `xz`:  
`xz`(`lzma` compression) is better alternative to `gzip` use it with 
```bash
xz -k -6 -T 3 --verbose <file>
```
`xz` supports parallel processing, use it with `-T 3` with 3 being number of processor.  

For best possible compression use https://github.com/ckolivas/lrzip  

[A Quick Benchmark: Gzip vs. Bzip2 vs. LZMA](https://tukaani.org/lzma/benchmarks.html)  

Which one to use?  
* If you want to quickly compress the file and on a limited resource use `gzip`. 
* If you want the best compression/ smallest file size and have enough RAM and processor available use `xz`  

Useful utilities:  
* Parallel gzip [pigz](https://zlib.net/pigz/)
* Long range zip [lrzip](https://github.com/ckolivas/lrzip)

---
## Compress folder/directory  
### Using `tar`:   
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
To decompress the file into the folder again replace the create (`-c`) with extract (`-x`) flag use
```bash
tar -zxvf archive.tar.gz
```  


### Using `zip`:  
```bash
zip output.zip [filename] [-r folder_name]
```
to unzip use  
```bash
unzip file.zip [-d destination_folder]
```
