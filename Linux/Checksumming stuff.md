```
find -type f -exec md5sum '{}' \; > md5sum.txt
```

**Sorted**
```
find . -type f -print0 | sort -z | xargs -r0 md5sum > md5sums.txt
```