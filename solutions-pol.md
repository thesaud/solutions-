#SDA1003

# Create a mock auth.log file with simulated failed SSH login attempts
cat > /tmp/auth.log <<EOF
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
EOF

# Extract the top 5 IP addresses with the most failed login attempts
grep "Failed password" /tmp/auth.log | awk '{print $11}' | sort | uniq -c | sort -nr | head -5 | awk '{print $2}'

# Expected output:
# 203.0.113.45
# 198.51.100.22
# 192.0.2.128
# 172.16.254.3
# 10.0.0.27

# Show number of attempts, IP address, and the usernames targeted from each IP
grep "Failed password" /tmp/auth.log | awk '{
    ip[$11]++;
    users[$11] = users[$11] ? users[$11] ", " $9 : $9
} END {
    for (i in ip)
        print ip[i], i, users[i]
}' | sort -nr | head -5

# Expected output:
# 7 203.0.113.45 root, admin, test, admin, admin, root, guest
# 4 198.51.100.22 ubuntu, guest, test, root
# 2 192.0.2.128 root, admin
# 1 172.16.254.3 root
# 1 10.0.0.27 ubuntu
