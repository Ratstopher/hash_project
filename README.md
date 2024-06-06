### Simplified Explanation of the Command-Line Script

#### Overview

This script is a basic toolkit that provides two main categories of tools:
1. **Forensic Tools**: For comparing files and finding identical files.
2. **Network Tools**: For scanning network ports.

Users interact with the toolkit through a simple text-based menu system. The script repeatedly shows menus and executes the user's chosen actions until the user decides to exit.

### Main Parts of the Script

#### Main Menu

This is the starting point of the toolkit. It shows the main options and lets the user choose what they want to do.

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

- **Menu Display**: Shows three options: Forensic Tools, Network Tools, and Exit.
- **User Input**: Waits for the user to enter their choice.
- **Action**: Calls the appropriate submenu based on the user's choice or exits the program if '3' is chosen.

#### Forensic Tools Menu

This submenu provides two tools: comparing two files using hash and finding identical files in a directory.

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

- **Menu Display**: Shows three options: Compare Two Files Using Hash, Find Identical Files in Directory, and Return to Main Menu.
- **User Input**: Waits for the user to enter their choice.
- **Action**: Calls the appropriate function based on the user's choice or returns to the main menu if '3' is chosen.

#### Network Tools Menu

This submenu provides a tool for scanning network ports using Nmap.

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

- **Menu Display**: Shows two options: Port Scan Using Nmap and Return to Main Menu.
- **User Input**: Waits for the user to enter their choice.
- **Action**: Calls the port scanning function based on the user's choice or returns to the main menu if '2' is chosen.

### Forensic Tools Functions

#### Compare Two Files Using Hash

This function compares the hash values of two files to check if they are identical.

```python
def compare_two_files():
    file1 = input("Enter the path for the first file: ")
    file2 = input("Enter the path for the second file: ")
    if compare_files(file1, file2):
        print("The files are identical")
    else:
        print("The files are not identical")
```

- **User Input**: Prompts the user to enter the paths of two files.
- **Action**: Calls `compare_files` to check if the files are identical and prints the result.

#### Find Identical Files in Directory

This function finds and displays groups of identical files in a directory.

```python
def find_identical_files():
    directory = input("Enter the path for the directory: ")
    identical_files = find_identical_files_in_directory(directory)
    display_identical_files(identical_files)
```

- **User Input**: Prompts the user to enter the path of a directory.
- **Action**: Calls `find_identical_files_in_directory` to find identical files and `display_identical_files` to print the results.

### Network Tools Function

#### Port Scan Using Nmap

This function scans a specified range of ports on a given IP address using Nmap.

```python
def port_scan_nmap():
    ip = input("Enter IP address to scan: ")
    port_range = input("Enter port range to scan (e.g., 1-1024): ")
    result = nmap_port_scan(ip, port_range)
    print("Port Scan Result:", result)
```

- **User Input**: Prompts the user to enter an IP address and a port range.
- **Action**: Calls `nmap_port_scan` to perform the port scan and prints the results.

### Utility Functions

#### Compare Files

Compares the hash values of two files.

```python
def compare_files(file1, file2, hash_method='sha256'):
    return get_file_digest(file1, hash_method) == get_file_digest(file2, hash_method)
```

- **Action**: Returns `True` if the files are identical, `False` otherwise.

#### Get File Digest

Calculates the hash value of a file.

```python
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

- **Action**: Returns the hash value of the file.

#### Find Identical Files in Directory

Finds groups of identical files in a directory using hash values.

```python
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
```

- **Action**: Returns a list of groups of identical files.

#### Display Identical Files

Prints the groups of identical files.

```python
def display_identical_files(identical_files):
    for group in identical_files:
        print("Identical files group:")
        for file in group:
            print(f"  - {file}")
        print()
```

- **Action**: Prints the paths of identical files.

#### Nmap Port Scan

Performs a port scan using Nmap and returns the results.

```python
def nmap_port_scan(ip, port_range):
    nm = nmap.PortScanner()
    nm.scan(ip, port_range)
    result = {}
    for proto in nm[ip].all_protocols():
        lport = nm[ip][proto].keys()
        for port in lport:
            result[port] = nm[ip][proto][port]['state']
    return result
```

- **Action**: Returns a dictionary of port states.

### Final Script Execution

Starts the main menu.

```python
if __name__ == "__main__":
    main_menu()
```

- **Action**: Runs the `main_menu` function, starting the toolkit.

### Slide Content for Each Section

---

#### Slide: Forensic Tools Menu Breakdown

**Forensic Tools Menu**

This menu provides access to various forensic tools within the toolkit. It allows users to compare files using hash functions or find identical files in a directory. The menu loops until the user chooses to return to the main menu.

**Code Example:**

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

**Detailed Explanation:**

1. **Function Definition**: The function is defined using `def forensic_tools_menu():`.
2. **Infinite Loop**: The `while True:` statement creates an infinite loop to keep the menu active.
3. **Menu Display**:
   - The menu title and options are printed using `print()` statements.
   - Users can see three options: "Compare Two Files Using Hash", "Find Identical Files in Directory", and "Return to Main Menu".
4. **User Input**: The `choice = input("Enter your choice: ")` statement waits for the user to input their choice.
5. **Condition Checking**:
   - **Option

 1**: If the user inputs '1', the `compare_two_files()` function is called.
   - **Option 2**: If the user inputs '2', the `find_identical_files()` function is called.
   - **Option 3**: If the user inputs '3', the `break` statement exits the loop, returning to the main menu.
   - **Invalid Input**: If the input is not '1', '2', or '3', the `else` clause prints an error message and the loop continues, re-displaying the menu.

**Key Points:**
- **Interactive Menu**: Users can interactively choose forensic tools.
- **Loop Control**: The infinite loop ensures the menu reappears after each action, providing continuous interaction.
- **Function Calls**: Depending on the user's choice, the corresponding function is called to perform the selected task.
- **Error Handling**: The `else` clause handles invalid inputs gracefully, prompting the user to try again.
```
