
# Full Disk Encrypt Guide

## Create a keyfile
```
dd if=/dev/urandom bs=1024 count=8 2>/dev/null | openssl aes-256-cbc -out disk.key
```
