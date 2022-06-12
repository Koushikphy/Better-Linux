# Linux utilities you should use instead
Better and faster alternative to regularly use bash commands  

## 1. `fd`: replacement for `find`
Install with `sudo apt install fd-find` or download the latest release from https://github.com/sharkdp/fd/releases  
Preferred usage `fd (part of) file_name [-e extension] [-S size_specs] [-x executable {}] [-t d/f] [-E exclude]` 

---
## 1. `exa`: replacement for `ls`  
Download latest release from https://github.com/ogham/exa/releases  
Preferred usage `exa -l -T -L <depth> --no-user`  

---
## 2. `dust`: replacement for `du`
Install with `cargo install du-dus` or download the latest release from https://github.com/bootandy/dust/releases  
Preferred usage `dust [-b]`

---
## 3. `dua`: replacement for `du`
Install it with `cargo install dua-cli` or download the latest release from https://github.com/Byron/dua-cli/releases  
Its preferable for its interactive explorer (like `ncdu`) use it with `dua i`

---
## 4. `fzz`: replacement for `Ctrl+R`(reverse-i-search)
Install with `git clone --depth 1 https://github.com/junegunn/fzf.git ~/.fzf && ~/.fzf/install`  
Now source the `~/.bashrc` and it will replace the Ctrl+R, bash reverse with `fzf`

---
## 5. `bat`: replacement for `cat`: 
Download it from here://github.com/sharkdp/bat

---
## 6. Terminal file managers:
1. lf: https://github.com/gokcehan/lf
2. ranger: https://github.com/ranger/ranger
3. nnn: https://github.com/jarun/nnn