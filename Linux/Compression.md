Compressing deterministically:
````
tar cvf - /path/to/files | gzip -n > archive.tar.gz
````

#### Interesting XZ tools
- -v verbose
- - T (thread amount)
- -C (check, it can be none, crc32, crc64, or sha256)
```

```