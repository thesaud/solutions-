
# 🔐 Security Log Analysis  
**Author:** saud1003  

This document provides a shell pipeline to analyze failed SSH login attempts from `/var/log/auth.log`, meeting the following requirements:

1. Count of attempts per IP  
2. Sorted from most to least attempts  
3. Only display the IP addresses (not full log lines)  
4. *(Bonus)* Include the usernames that were attempted  

---

## ✅ Task 1: Top 5 IP addresses with the most failed SSH login attempts

```bash
grep "Failed password" /var/log/auth.log \
  | awk '{print $(NF-3)}' \
  | sort \
  | uniq -c \
  | sort -nr \
  | awk '{print $2}' \
  | head -n 5
```

### 📝 Explanation:
- `grep "Failed password"` filters only failed SSH login attempts.
- `awk '{print $(NF-3)}'` extracts the IP address from each line.
- `sort` prepares the data for counting.
- `uniq -c` counts the number of occurrences per IP.
- `sort -nr` sorts the IPs by number of attempts (descending).
- `awk '{print $2}'` outputs only the IP addresses.
- `head -n 5` shows the top 5 IPs.

---

## 🌟 Bonus: Top 5 (IP + username) combinations

```bash
grep "Failed password" /var/log/auth.log \
  | awk '{print $(NF-3), $(NF-5)}' \
  | sort \
  | uniq -c \
  | sort -nr \
  | head -n 5
```

### 📝 Explanation:
- `awk '{print $(NF-3), $(NF-5)}'` extracts both the IP address and the attempted username.
- The rest of the pipeline is the same: sort, count, sort again, and show top 5 results.

---

## 🧪 Testing with Sample Data (Optional)

If `/var/log/auth.log` is empty (e.g., on a local machine), you can populate it with sample entries:

```bash
sudo bash -c 'cat > /var/log/auth.log <<EOF
Mar 10 14:23:45 server sshd[12345]: Failed password for root from 203.0.113.45 port 54322 ssh2
Mar 10 14:23:47 server sshd[12345]: Failed password for admin from 203.0.113.45 port 54322 ssh2
Mar 10 14:23:50 server sshd[12345]: Failed password for ubuntu from 198.51.100.22 port 42351 ssh2
Mar 10 14:23:52 server sshd[12345]: Failed password for test from 203.0.113.45 port 54322 ssh2
Mar 10 14:23:55 server sshd[12345]: Failed password for root from 192.0.2.128 port 38422 ssh2
Mar 10 14:23:57 server sshd[12345]: Failed password for admin from 203.0.113.45 port 54322 ssh2
Mar 10 14:24:00 server sshd[12345]: Failed password for guest from 198.51.100.22 port 42351 ssh2
Mar 10 14:24:02 server sshd[12345]: Failed password for root from 172.16.254.3 port 58211 ssh2
Mar 10 14:24:05 server sshd[12345]: Failed password for admin from 203.0.113.45 port 54322 ssh2
Mar 10 14:24:07 server sshd[12345]: Failed password for ubuntu from 10.0.0.27 port 49233 ssh2
Mar 10 14:24:10 server sshd[12345]: Failed password for test from 198.51.100.22 port 42351 ssh2
Mar 10 14:24:12 server sshd[12345]: Failed password for root from 203.0.113.45 port 54322 ssh2
Mar 10 14:24:15 server sshd[12345]: Failed password for admin from 192.0.2.128 port 38422 ssh2
Mar 10 14:24:17 server sshd[12345]: Failed password for guest from 203.0.113.45 port 54322 ssh2
Mar 10 14:24:20 server sshd[12345]: Failed password for root from 198.51.100.22 port 42351 ssh2
EOF'
```

---

## ✅ Output Example

Example output for IPs:
```
203.0.113.45
198.51.100.22
192.0.2.128
172.16.254.3
10.0.0.27
```

Example output for (IP + username) pairs:
```
5 203.0.113.45 admin
3 198.51.100.22 ubuntu
2 192.0.2.128 root
2 203.0.113.45 root
2 198.51.100.22 test
```

---

**End of Report**  
