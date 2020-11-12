# Cheatsheet

#### Command Line Editing

| Combo | Action | 
| ---   | ---    |
| `CLTR-B` | Move cursor left |
| `CLTR-F` | Move cursor right |
| `CLTR-A` | Move curosr to beginning of line | 
| `CLTR-E` | Move cursor to end of line |
| `CTRL-W` | Erase the preceding word |
| `CTRL-U` | Erase from cursor to beginning of line | 
| `CTLR-K` | Erase from cursor to end of line | 
| `CLTR-Y` | Paste erased text |

#### vi 

| Combo | Action |
| --- | --- |
| `0` | Go to start of line |
| `$` | Go to the end of the line | 
| `H` | Move to the top of the window | 
| `L` | Move to the bottom of the window | 
| `yy` FOLLOWED BY `P` | Copy the line, move the cursor then insert below the cursor | 
| `o` | Insert line after cursor |

#### Misc

```bash
# generate a random string
cat /dev/urandom | head -c16 | xxd -p | cut -c1-32

# see the meaning of different flags such as if [[ -z ${X} ]]; then 
man test
```

