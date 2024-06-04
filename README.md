# Identifying Identical Files Using Hash Functions

This Python script scans a directory to identify identical files based on their hash digests. It uses the SHA-256 hash function by default, but other hash methods can be specified.

## Methodology

### Imports

```python
import hashlib  # Importing hashlib to use hash functions like sha256
from pathlib import Path  # Importing Path from pathlib for easier file path handling
```

- **hashlib**: Provides secure hash functions, such as `sha256`, which are used to generate a unique hash for file contents.
- **pathlib.Path**: Simplifies file path handling and allows easy navigation and manipulation of filesystem paths.

### Function to Calculate File Hash

```python
def get_file_digest(file_path, hash_method='sha256'):
    hash_func = hashlib.new(hash_method)  # Create a new hash object for the specified method
    try:
        with open(file_path, 'rb') as f:  # Open the file in binary mode (read-only)
            for chunk in iter(lambda: f.read(4096), b""):  # Read the file in chunks of 4096 bytes
                hash_func.update(chunk)  # Update the hash object with the current chunk of the file
    except (PermissionError, IOError) as e:
        print(f"Cannot access {file_path}: {e}")  # Handle exceptions for permission errors or IO errors
        return None
    return hash_func.hexdigest()  # Return the hexadecimal representation of the hash digest
```

- **Methodology**: This function reads a file in binary mode and calculates its hash digest using the specified hash method. Reading in chunks allows efficient processing of large files.
- **Goals Met**:
  - **Step 1**: Read the file as a binary file.
  - **Step 2**: Get the digest based on the hash.
  - **Cross-platform Compatibility**: Uses Python's standard libraries, which are cross-platform.

### Function to Compare Two Files

```python
def compare_files(file1, file2, hash_method='sha256'):
    return get_file_digest(file1, hash_method) == get_file_digest(file2, hash_method)  # Compare the hash digests
```

- **Methodology**: This function compares the hash digests of two files to determine if they are identical.
- **Goals Met**:
  - **Step 3**: Do the same for the second file.
  - **Step 4**: Compare the two digests, if they are the same then say identical.

### Function to Find Identical Files

```python
def find_identical_files(directory_path, hash_method='sha256'):
    file_paths = [str(p) for p in Path(directory_path).rglob('*') if p.is_file()]  # Get all file paths in the directory
    checked_files = set()  # Set to keep track of files that have already been checked
    identical_files = []  # List to store groups of identical files

    for i, file1 in enumerate(file_paths):  # Iterate over the list of file paths
        if file1 in checked_files:  # Skip the file if it has already been checked
            continue
        duplicates = [file1]  # List to store files that are identical to the current file
        for file2 in file_paths[i+1:]:  # Compare the current file with all subsequent files in the list
            if file2 not in checked_files and compare_files(file1, file2, hash_method):  # Check if files are identical
                duplicates.append(file2)  # Add the second file to the list of duplicates
                checked_files.add(file2)  # Mark the second file as checked
        if len(duplicates) > 1:  # If there are duplicates, add the group to the list of identical files
            identical_files.append(duplicates)
            checked_files.add(file1)  # Mark the first file as checked

    return identical_files  # Return the list of groups of identical files
```



```markdown
- **Methodology**: This function scans the specified directory and its subdirectories to find identical files based on their hash values. It uses `pathlib` to list all files and compares each file to every other file to identify duplicates.
- **Goals Met**:
  - **Demonstrate all identical files in a directory and subdirectories**: Uses `Path.rglob('*')` to recursively find all files.
  - **How to Compare All Files in a Directory**: Gets all filenames in a directory as a list and compares each file to every other file.

### Function to Display Identical Files

```python
def display_identical_files(identical_files):
    for group in identical_files:  # Iterate over each group of identical files
        print(f"Identical files group:")  # Print a header for the group
        for file in group:  # Print each file in the group
            print(f"  - {file}")
        print()  # Print a blank line for separation between groups
```

- **Methodology**: This function prints the groups of identical files found by the `find_identical_files` function.
- **Goals Met**:
  - **Display the results**: Clearly displays groups of identical files for easy identification.

### Main Execution

```python
# Define our directory
directory = './compare_files'
# Define our hash method
hash_method = 'sha256'
# Find identical files in the specified directory
identical_files = find_identical_files(directory, hash_method)
# Display the groups of identical files
display_identical_files(identical_files)
```

- **Methodology**: This part of the script sets the directory to be scanned and the hash method to be used. It then calls the functions to find and display identical files.
- **Goals Met**:
  - **Cross-platform Compatibility**: Uses Python's standard libraries, which work across Windows, Linux, and macOS.
  - **Customizable Hash Method**: Allows changing the hash method to balance reliability and speed.

### Additional Enhancements

#### List Available Hash Methods

```python
# List available hash methods
print("Available hash methods:", hashlib.algorithms_guaranteed)
```

- **Methodology**: Lists all hash algorithms guaranteed to be available in `hashlib`.
- **Goals Met**:
  - **Identify other hash methods**: Demonstrates available hash methods.

#### Infer Hash Method Based on Digest Length

```python
# Function to infer hash method based on digest length
def infer_hash_method(digest):
    length = len(digest)
    methods = {
        32: 'md5',
        40: 'sha1',
        64: 'sha256',
        128: 'sha512'
    }
    return methods.get(length, 'Unknown')

# Test infer_hash_method
test_digest = 'e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855'
print(f"The hash method for digest {test_digest} is likely: {infer_hash_method(test_digest)}")
```

- **Methodology**: Uses the length of a hash digest to infer the hash method used to generate it.
- **Goals Met**:
  - **Identify candidate hash methods given a hash digest**: Provides a way to infer the hash method from the digest length.

## Conclusion

This script effectively identifies identical files in a directory by comparing their hash digests. It is versatile, allowing for different hash methods, and demonstrates key forensic techniques for ensuring data integrity. The enhancements provide additional functionality to list available hash methods and infer hash methods based on digest length.
