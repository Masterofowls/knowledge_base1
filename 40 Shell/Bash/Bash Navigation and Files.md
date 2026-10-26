---
tags: [bash, snippet]
aliases: [ls cd cp mv rm, chmod, file permissions]
created: 2026-10-26
status: evergreen
---
# Bash Navigation and Files

```bash
pwd; cd ~/projects; cd -          # previous dir
ls -lah                           # long, all, human sizes
tree -L 2                         # if installed
mkdir -p a/b/c                    # create parents
cp -r src/ backup/                # recursive copy
mv old.txt new.txt
rm -rf build/                     # ⚠️ no undo
ln -s /opt/app/current app        # symlink
touch file.txt
cat f; less f; head -n 20 f; tail -f app.log
du -sh *; df -h                   # disk usage
which python; type ll             # where's the command?
```

## Permissions
```
-rwxr-x---  1 ada dev  4096 file
 │  │  │
 │  │  └── others: ---
 │  └───── group:  r-x
 └──────── owner:  rwx
```
```bash
chmod +x deploy.sh
chmod 640 secrets.env          # r=4 w=2 x=1 → owner rw, group r, others none
chown ada:dev file
```

## Handy shortcuts
`Ctrl+R` reverse search · `!!` last command · `!$` last arg · `Ctrl+A/E` line start/end · `Ctrl+W` delete word · `cd ~` / `~user`

## Related
- [[Bash find and xargs]] · [[PowerShell File System]]
