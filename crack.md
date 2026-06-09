# JohnTheReaper #
```
john --wordlist=/usr/share/wordlists/rockyou.txt hash
```
* krb5asrep *
```
john --format=krb5asrep --wordlist=/usr/share/wordlists/rockyou.txt cleanhash
```

# Hashcat #
* krb5asrep *
```
hashcat -m 18200 hash /usr/share/wordlists/rockyou.txt
```
