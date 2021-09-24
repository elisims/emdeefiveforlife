# HTB Emdee Five For Life Project

## Description 
- Hack The Box (HTB) challenges have become a popular way to test new techniques, hone your skills, and create content. 
- These labs can be quite fun, as it’s not usually a single person creating a whole course, but are generally one-off scenarios (so there’s no stress to not finishing).
- While “Hack The Box” is a company name that hosts some, these challenges can be included in CTFs, and well as a good way of demonstrating new tooling in an environment that was set up specifically for that purpose.

## Tools Utilized
- HTB (HackTheBox) 
- “Emdee Five For Life” Instance (Docker) 
- https://www.hackthebox.eu/home/challenges/Web 
- Python3 
  - Including the library “BeautifulSoup” for HTML data parsing 
- Oracle VirtualBox 
- PopOS (Ubuntu 20.4) VM 

# Report 

## Introduction 
- While  researching  which  challenge  from  HTB  (HackTheBox)  I  was  going  to attempt,  I  looked  under  “Web  Instances”  because  of  my  interest  in  Web  Design.  I am not  the  most  experienced  “hacker”  yet,  so  I  knew  I  wanted  to  try  a beginner-friendly  instance,  therefore  allowing  me  to  gain  confidence  and  branch out  into  harder  instances  in  the  future.  The  instance  that  really  piqued  my  interest is  titled  “Emdee  Five  For  Life”,  the  name  being  a  derivation  of  MD5-  A  common hashing  algorithm.  After  starting  the  instance,  I  began  my  initial  recon. 

## Initial  Recon 
- The  instance  is  set  to  the  address  of  “http://139.59.190.165:30450/“  and visiting  this  address  results  in  a  simple  form  with  the  instructions  given  to  use  MD5 to  encrypt  the  plaintext  given. 

![img](https://github.com/elisims/emdeefiveforlife/raw/main/images/1.png)

- This  seemed  easy,  as  a  simple  command  within  the  terminal  could  quickly  create an  MD5 hash  for  the  plaintext  given.  So  that’s  exactly  what  I  tried  to  do.

![img](https://github.com/elisims/emdeefiveforlife/raw/main/images/2.png)

- However, once the MD5 hash was submitted in the form, the result given is simply “TOO SLOW” prompting to try again.  

![img](https://github.com/elisims/emdeefiveforlife/raw/main/images/3.png)

- So I tried again, but this time I had the terminal on my second monitor and made sure to use the “ctrl+shft+c” and “ctrl+v” to copy/paste as fast as humanly possible. The resulting “too slow” meant that it wouldn’t be as easy as hashing the plaintext by hand- Therefore scripting would need to be implemented. 

## Creating the exploit.py script
- Inspecting the elements on the page resulted in the discovery that the form indeed uses a GET/POST logic- also showing the location of the plaintext within the “<h3>” HTML tags. 

![img](https://github.com/elisims/emdeefiveforlife/raw/main/images/4.png)
  
- With  this  knowledge,  I  started  by  researching  for  the  best  way  to  parse  HTML data,  which  led  me  to  the  python  library  “BeautifulSoup”.  BeautifulSoup  claims  to save  developers  hundreds  of  hours  with  easy-to-use  commands-  But  some  initial testing  would  need  to  be  done  in  order  to  learn  some  of  the  basics. 
- I  wrote  a  simple  python  script  that  started  a  session  at  the  URL  instance  provided by  HTB.  The  script  then  passes  the  HTML  into  BeautifulSoup,  which  then  allows me to  extract  the  plaintext  encoded  in  UTF-8  format. 

```python
import  requests 
import  hashlib from  bs4
import  BeautifulSoup 
  
req  =  requests.session() 
url  = "http://139.59.190.165:30450/" 

getReq  =  req.get(url) 
html  =  getReq.content

soup  =  BeautifulSoup(html, 'html.parser') 
print(soup) 
  
plaintext  =  soup.h3.get_text().encode('utf-8') 
print(plaintext)
```

- To  ensure  the  underlying  fundamentals  of  the  script  were  working  correctly,  I output  the  variables  “soup”  and  “plaintext”  to  observe  the  result.
  
- As  expected,  “soup”  printed  the  HTML  output  that  BeautifulSoup  had  been  givenAnd  “plaintext”  printed  the  parsed  “<h3>”  tag  from  bs4. 
- Next,  I  wrote  in  the  functionality  to  hash  the  extracted  plaintext  using  MD5.
  
```python
  import  requests 
  import  hashlib from  bs4 
  import  BeautifulSoup 
  
  req  =  requests.session() 
  url  = "http://139.59.190.165:30450/" 
  
  getReq  =  req.get(url) 
  html  =  getReq.content 
  
  soup  =  BeautifulSoup(html, 'html.parser') 
  plaintext  =  soup.h3.get_text().encode('utf-8') 
  
  #Testing  output/parse  of  bs4 
  #print(soup) 
  #print(plaintext) 
  
  ciphertext  =  hashlib.md5(plaintext).hexdigest() 
  print(ciphertext)
```
