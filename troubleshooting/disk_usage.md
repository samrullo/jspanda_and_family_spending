To check **disk usage on Linux (Ubuntu)**, here are the **most useful and practical commands**, from quick overview → detailed investigation.

---

## 🔹 1. Overall disk usage (most common)

```bash
df -h
```

**What it shows**

* Total / Used / Available space
* Mounted filesystems
* `-h` = human-readable (GB, MB)

Example:

```
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda2       100G   62G   33G  66% /
```

👉 If `/` is close to **90–100%**, that’s your problem.

---

## 🔹 2. Disk usage of a specific directory

```bash
du -sh /path/to/directory
```

Examples:

```bash
du -sh /home
du -sh /var
du -sh /var/log
```

* `-s` → summary
* `-h` → human-readable

---

## 🔹 3. Find **largest directories** (very useful)

```bash
du -h / --max-depth=1 2>/dev/null | sort -hr
```

Or safer (avoid permission spam):

```bash
sudo du -h / --max-depth=1 | sort -hr
```

This helps you see **what eats space**:

```
20G /var
15G /home
3G  /usr
```

Then drill down:

```bash
sudo du -h /var --max-depth=1 | sort -hr
```

---

## 🔹 4. Check inode usage (if disk is “full” but space exists)

```bash
df -i
```

If **inodes are 100%**, too many small files (logs, cache).

---

## 🔹 5. Find large files (over 1GB)

```bash
sudo find / -type f -size +1G -exec ls -lh {} \; 2>/dev/null
```

Or top 20 largest files:

```bash
sudo find / -type f -exec du -h {} + 2>/dev/null | sort -hr | head -20
```

---

## 🔹 6. Check Docker disk usage (if you use Docker)

```bash
docker system df
```

Clean unused Docker data:

```bash
docker system prune -a
```

⚠️ Removes unused images/containers.

---

## 🔹 7. Check logs size (common culprit)

```bash
sudo du -sh /var/log/*
```

Clean old logs:

```bash
sudo journalctl --vacuum-time=7d
```

---

## 🔹 8. Interactive disk analyzer (optional)

```bash
sudo apt install ncdu
sudo ncdu /
```

Best **visual way** to explore disk usage.

---

## 🧠 Quick recommendation

If you want **fast diagnosis**, run in this order:

```bash
df -h
sudo du -h / --max-depth=1 | sort -hr
sudo du -h /var --max-depth=1 | sort -hr
```

---

If you want, paste the output of:

```bash
df -h
```

and I’ll **analyze it for you and tell exactly what to clean** 👍
