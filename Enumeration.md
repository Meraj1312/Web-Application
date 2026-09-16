# Ffuf (Directories) # 

*Always add 403*
```
ffuf -u http://target/FUZZ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/DirBuster-2007_directory-list-lowercase-2.3-medium.txt -e .php,.html,.cgi,.php5 -mc 200,301,302,303,403
```
```
ffuf -u http://target/FUZZ -w /usr/share/wordlists/dirb/small.txt -mc 200,301,302,303,403
```
```
ffuf -u http://target/FUZZ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/DirBuster-2007_directory-list-lowercase-2.3-medium.txt -e .php,.html,.cgi,.php5,.sh,.pl -mc 200,301,302,303,403
```
```
ffuf -u http://target/FUZZ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/common.txt -e .php,.html,.cgi,.php5,.sh,.pl -mc 200,301,302,303,403
```
# Ffuf (Subdomain) #
```
ffuf -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-110000.txt -u http://target.com -H "Host: FUZZ.target.com" -fs {filter size}
```

# Dnsenum #
```
dnsenum --enum inlanefreight.com -f  /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-110000.txt
```
