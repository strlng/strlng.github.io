---
layout: post
title: LDAP Authentication using PHP
date: '2005-03-16 16:09:00'
---

Authenticating against an LDAP server consists of just connecting to the server then binding using your credentials. Here’s a couple functions I use to authenticate again LDAP. The first function, ldapauthenticated, takes your uid and password and attempts to bind to the LDAP server. It returns a bool, true you were successful, false you weren’t. Pretty simple. There are 2 constants I use in the function, LDAP\_SERVER and LDAP\_BASE\_DN. These would need to be defined. LDAP\_SERVER is your server’s host name. LDAP\_BASE\_DN would be something like "dc=something, dc=company, dc=com”

```php
function ldapauthenticated($uid, $password) {  
/_ldap will bind anonymously, make sure we have some credentials_/  
if ($uid != ”) {  
$ldap = @ldap\_connect(LDAP\_SERVER);  
$prot3 = @ldap\_set\_option($ldap,LDAP\_OPT\_PROTOCOL\_VERSION,3);  
if (isset($ldap) && $ldap != ” && $prot3) {  
/\* search for pid dn _/  
$result = @ldap\_search($ldap, LDAP\_BASE\_DN, ‘uid=’.$uid, array(‘dn’));  
if ($result != 0) {  
$entries = @ldap\_get\_entries($ldap, $result);  
$principal = $entries[0][‘dn’];  
if (isset($principal)) {  
/_ bind as this user \*/  
if (@ldap\_bind($ldap, $principal, $password)) {  
// Authenticate success  
return true;  
} else {  
// Authenticate failure  
return false;  
}  
} else {// User not found in LDAP  
return false;  
} // end: else: if (isset($principal))  
ldap\_free\_result($result);  
} else { // Error occured searching the LDAP  
return false;  
}  
ldap\_close($ldap);  
} else { // Could not connect to LDAP  
return false;  
}  
} else {  
return false;  
}  
return false;  
}
```

The second function, userauthenticated, does some setup if the user is authenticated against the LDAP server. You can see that if ldapauthenticated is successful a object (which I normally would not like to use) is setup. It’s a class I created for the specific system this code was taken from. Anyway, the most important function is the first one. The second one just shows how you can do a little setup for your users once they authenticate.

```php
function userauthenticated ($uid, $password) {  
if (ldapauthenticated ($uid, $password)) {  
$user = new User ($uid);  
if (!$user-\>id) {  
$\_SESSION[‘messages’] = “User/Password combination not found.”;  
return false;  
} else {  
$\_SESSION[‘user\_id’] = $user-\>id;  
$\_SESSION[‘messages’] = “You are now logged in.”;  
return true;  
}  
} else {  
$\_SESSION[‘messages’] = “LDAP authentication failed.”;  
return false;  
}  
}
```