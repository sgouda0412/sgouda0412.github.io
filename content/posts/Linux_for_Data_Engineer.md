+++
title = '🐧 Linux for Data Engineers: `grep`, `sed`, and `awk`'
date = 2025-03-28T16:00:34+05:30
draft = false
author = 'Santosh Kumar Gouda'
+++


## 🔎 `grep` - Searching in Text Files

`grep` (Global Regular Expression Print) is used to search for patterns in files.

### 📌 Basic Usage
```sh
# Find lines containing 'error' in log.txt
grep 'error' log.txt
```

### 🎯 Common Options
| Option  | Description |
|---------|-------------|
| `-i`    | Ignore case |
| `-v`    | Invert match (show lines NOT matching) |
| `-c`    | Count occurrences |
| `-n`    | Show line numbers |
| `-r`    | Recursive search |
| `--color=auto` | Highlight matches |

### 🏆 Examples
```sh
# Find all occurrences of 'warning' (case-insensitive) in logs
grep -i 'warning' server.log

# Show lines NOT containing 'failed'
grep -v 'failed' report.txt

# Count occurrences of 'success'
grep -c 'success' results.csv
```

---

## ✂️ `sed` - Stream Editor for Modifying Text

`sed` (Stream Editor) is used to find and replace text, delete lines, or modify files.

### 📌 Basic Usage
```sh
# Replace 'foo' with 'bar' in a file
sed 's/foo/bar/g' file.txt
```

### 🎯 Common Options
| Option  | Description |
|---------|-------------|
| `-i`    | Edit file in place |
| `s`     | Substitute text |
| `g`     | Replace all occurrences |
| `d`     | Delete lines |
| `p`     | Print lines |

### 🏆 Examples
```sh
# Replace all instances of '2023' with '2024' in data.csv (modify in place)
sed -i 's/2023/2024/g' data.csv

# Delete lines containing 'error'
sed '/error/d' logs.txt

# Print lines 1 to 5
sed -n '1,5p' file.txt
```

---

## 📊 `awk` - Pattern Scanning and Processing Language

`awk` is used for text manipulation, filtering, and reporting.

### 📌 Basic Usage
```sh
# Print the first column from a CSV file
awk -F, '{print $1}' data.csv
```

### 🎯 Common Options
| Option  | Description |
|---------|-------------|
| `-F`    | Set field delimiter |
| `$1, $2` | Refer to specific columns |
| `NR`    | Line number |
| `NF`    | Number of fields |

### 🏆 Examples
```sh
# Print the second column from a space-separated file
awk '{print $2}' records.txt

# Print lines where the third column is greater than 100
awk '$3 > 100' sales.csv

# Sum values in the second column
awk '{sum += $2} END {print sum}' data.txt
```

---

## 🎯 Combining `grep`, `sed`, and `awk`

```sh
# Extract error lines, replace 'fail' with 'error', and print first column
grep 'error' logs.txt | sed 's/fail/error/g' | awk '{print $1}'
```

🚀 These tools are essential for processing large text-based datasets efficiently! Happy coding! 🎉

