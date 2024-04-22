
### For Terminal/CMD:

To access practice files via Terminal or Command Prompt, use:

```bash
wget -O - http://github.com/Kundan1804/LP5/raw/main/DL/pract1.txt
```

### For HPC Assignments 1-3:

Compile and run the program using gcc or g++:

```bash
g++ filename.cpp -o filename.exe -fopenmp
./filename.exe
```

### For HPC Assignment 4:

Switch the runtime environment to GPU:

1. Runtime
2. Change Runtime Type
3. Select GPU from the drop-down menu

## Python Script

To access practical files in Python:

```python
import requests
print(requests.get('http://github.com/Kundan1804/LP5/raw/main/DL/pract1.txt').text)
```
