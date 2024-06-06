
### Slide Deck for the Command-Line Toolkit

---

#### Slide 1: Title Slide

**Title: Assembling into a Toolkit**

---

#### Slide 2: Introduction

**Assembling into a Toolkit**

The first step in assembling a command-line menu-based toolkit is creating a simple text-based menu system. Users navigate using their keyboard, and Python print statements show options and input functions capture user selections.

Advantages:
- Simplicity: Easy to develop, no graphical interface needed.
- Speed: Faster to create and run.
- Portability: Can be run on any system with a terminal.
- Resource Efficiency: Consumes fewer resources.

---

#### Slide 3: Main Menu Example

**Main Menu Example**

```python
def main_menu():
    while True:
        print("\nMain Menu:")
        print("1. Forensic Tools")
        print("2. Network Tools")
        print("3. Exit")
        choice = input("Enter your choice: ")
        if choice == '1':
            forensic_tools_menu()
        elif choice == '2':
            network_tools_menu()
        elif choice == '3':
            print("Exiting...")
            break
        else:
            print("Invalid choice. Please try again.")
```

---

#### Slide 4: Forensic Tools Menu Example

**Forensic Tools Menu Example**

```python
def forensic_tools_menu():
    while True:
        print("\nForensic Tools Menu:")
        print("1. Compare Two Files Using Hash")
        print("2. Find Identical Files in Directory")
        print("3. Return to Main Menu")
        choice = input("Enter your choice: ")
        if choice == '1':
            compare_two_files()
        elif choice == '2':
            find_identical_files()
        elif choice == '3':
            break
        else:
            print("Invalid choice. Please try again.")
```

---

#### Slide 5: Network Tools Menu Example

**Network Tools Menu Example**

```python
def network_tools_menu():
    while True:
        print("\nNetwork Tools Menu:")
        print("1. Port Scan Using Nmap")
        print("2. Return to Main Menu")
        choice = input("Enter your choice: ")
        if choice == '1':
            port_scan_nmap()
        elif choice == '2':
            break
        else:
            print("Invalid choice. Please try again.")
```

---

#### Slide 6: File Hashing Example

**File Hashing Example**

```python
def compare_two_files():
    file1 = input("Enter the path for the first file: ")
    file2 = input("Enter the path for the second file: ")
    if compare_files(file1, file2):
        print("The files are identical")
    else:
        print("The files are not identical")
```

```python
def compare_files(file1, file2, hash_method='sha256'):
    return get_file_digest(file1, hash_method) == get_file_digest(file2, hash_method)

def get_file_digest(file_path, hash_method='sha256'):
    hash_func = hashlib.new(hash_method)
    try:
        with open(file_path, 'rb') as f:
            for chunk in iter(lambda: f.read(4096), b""):
                hash_func.update(chunk)
    except (PermissionError, IOError) as e:
        print(f"Cannot access {file_path}: {e}")
        return None
    return hash_func.hexdigest()
```

---

#### Slide 7: Finding Identical Files Example

**Finding Identical Files Example**

```python
def find_identical_files():
    directory = input("Enter the path for the directory: ")
    identical_files = find_identical_files_in_directory(directory)
    display_identical_files(identical_files)

def find_identical_files_in_directory(directory_path, hash_method='sha256'):
    file_paths = [str(p) for p in Path(directory_path).rglob('*') if p.is_file()]
    checked_files = set()
    identical_files = []

    for i, file1 in enumerate(file_paths):
        if file1 in checked_files:
            continue
        duplicates = [file1]
        for file2 in file_paths[i+1:]:
            if file2 not in checked_files and compare_files(file1, file2, hash_method):
                duplicates.append(file2)
                checked_files.add(file2)
        if len(duplicates) > 1:
            identical_files.append(duplicates)
            checked_files.add(file1)

    return identical_files

def display_identical_files(identical_files):
    for group in identical_files:
        print("Identical files group:")
        for file in group:
            print(f"  - {file}")
        print()
```

---

#### Slide 8: Port Scanning Example

**Port Scanning Example**

```python
def port_scan_nmap():
    ip = input("Enter IP address to scan: ")
    port_range = input("Enter port range to scan (e.g., 1-1024): ")
    result = nmap_port_scan(ip, port_range)
    print("Port Scan Result:", result)

def nmap_port_scan(ip, port_range):
    nm = nmap.PortScanner()
    nm.scan(ip, port_range)
    result = {}
    for proto in nm[ip].all_protocols():
        lport = nm[ip][proto].keys()
       
