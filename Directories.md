### Gobuster ###

*Always add 403*
```
gobuster dir -u http://target -w /usr/share/wordlists/SecLists/Discovery/Web-Content/DirBuster-2007_directory-list-lowercase-2.3-medium.txt -x php,html,cgi,php5 -s 200,301,302,303,403 -b ""
```
```
gobuster dir -u http://target -w /usr/share/wordlists/dirb/small.txt -s 200,301,302,303,403 -b ""
```
```
gobuster dir -u http://target -w /usr/share/wordlists/SecLists/Discovery/Web-Content/DirBuster-2007_directory-list-lowercase-2.3-medium.txt -x php,html,cgi,php5,sh,pl -s 200,301,302,303,403 -b ""
```
```
gobuster dir -u http:/target -w /usr/share/wordlists/SecLists/Discovery/Web-Content/common.txt -x php,html,cgi,php5,sh,pl -s 200,301,302,303,403 -b ""
```
