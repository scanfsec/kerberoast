# kerberoast
Kerberos attack toolkit -pure python-

### Install
```pip3 install kerberoast```   

#### Prereqirements
Python 3.6
See requirements.txt


### For the impatient
IMPORTANT: the accepted formats are the following  
```<target>``` : ```<domainname>/<username>:<password>@<DC_ip>```  
```<userlogin>```: ```<kerberos realm>/<username>:<password or NT hash or AES key>@<DC_ip>```  
  
If nt hash or AES key is used insted of plaintext password, you MUST indicate it with ```-n``` and ```-a``` respectively  
  
Steps:  
1. Look for vulnerable users via LDAP  
```kerberoast ldap  all <target> -o ldapenum```
2. Use ASREP roast against users in the ```ldapenum_asrep_users.txt``` file  
```kerberoast asreproast <DC_ip> -t ldapenum_asrep_users.txt```
3. Use SPN roast against users in the ```ldapenum_spn_users.txt``` file  
```kerberoast spnroast <userlogin> -t ldapenum_spn_users.txt```
4. Crack SPN roats output with hashcat  
<strike>5. Crack ASREP roast results with hashcat </strike> See: [hashcat issue ](https://github.com/hashcat/hashcat/issues/1707)  

## Commands
### ldap
This command group is for enumerating potentially vulnerable users via LDAP.  
#### Command structure  
&nbsp;&nbsp;&nbsp;&nbsp;```kerberoast ldap  <type> <target> <options>```  
  
```Type```: It supports three types of users to be enumerated  
1. ```spn``` Enumerates users with ```servicePrincipalName``` attribute set.  
2. ```asrep``` Enumerates users with ```DONT_REQ_PREAUTH``` flag set in their UAC attribute.
3. ```all``` Startes all the above mentioned enumerations.

```target```:  Specifies the usercredential and the target server in the following format
  
&nbsp;&nbsp;&nbsp;&nbsp;```<domainname>/<username>:<password>@<DC_ip>```  
If password is omitted, the script will promt for the password.  
  
```options```:  
&nbsp;&nbsp;&nbsp;&nbsp;```-n```: Specifies if the password is in fact an NT hash  
&nbsp;&nbsp;&nbsp;&nbsp;```-o```: Output file base name  

### brute
This command is to perform username enumeration by brute-forcing the kerberos service with possible username candidates  
#### Command structure  
&nbsp;&nbsp;&nbsp;&nbsp;```kerberoast brute <realm> <dc_ip> <targets> <options>```  
  
```realm```: The kerberos realm usually looks like ```COMPANY.corp```  
```dc_ip```: IP or hostname of the domain controller  
```targets```: Path to the file which contains the possible username candidates  
```options```:   
&nbsp;&nbsp;&nbsp;&nbsp;```-o```: Output file base name 

### asreproast
This command is to perform ASREProast attack
#### Command structure  
&nbsp;&nbsp;&nbsp;&nbsp;```kerberoast asreproast <dc_ip> <options>```  
  
```dc_ip```: IP or hostname of the domain controller  
```options```:  
&nbsp;&nbsp;&nbsp;&nbsp;```-r```: Specifies the kerberos realm to be used. It overrides all other realm info.  
&nbsp;&nbsp;&nbsp;&nbsp;```-o```: Output file base name  
&nbsp;&nbsp;&nbsp;&nbsp;```-t```: Path to the file which contains the usernames to perform the attack on   
&nbsp;&nbsp;&nbsp;&nbsp;```-u```: Specifies the user to perform the attack on. Format is either ```<username>``` or ```<realm>/<username>``` but in the first case, the ```-r``` option must be used to specify the realm  
  
## spnroast
This command is to perform SPNroast (AKA kerberoast) attack.  
#### Command structure  
&nbsp;&nbsp;&nbsp;&nbsp;```kerberoast spnroast <logincreds> <options>```  
  
```logincreds```: Specifies the usercredential and the target server in the following format ```<kerberos realm>/<username>:<password or NT hash or AES key>@<DC_ip>```  
```options```:  
&nbsp;&nbsp;&nbsp;&nbsp;```-r```: Specifies the kerberos realm to be used. It overrides all other realm info.  
&nbsp;&nbsp;&nbsp;&nbsp;```-o```: Output file base name  
&nbsp;&nbsp;&nbsp;&nbsp;```-t```: Path to the file which contains the usernames to perform the attack on   
&nbsp;&nbsp;&nbsp;&nbsp;```-u```: Specifies the user to perform the attack on. Format is either ```<username>``` or ```<realm>/<username>``` but in the first case, the ```-r``` option must be used to specify the realm  
&nbsp;&nbsp;&nbsp;&nbsp;```-n```: Specifies if the password is in fact an NT hash  
&nbsp;&nbsp;&nbsp;&nbsp;```-a```: Specifies if the password is in fact an AES key  
