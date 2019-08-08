# Использование Github через прокси (Windows и Unix)

 Here is a pretty standard scenario at most corporations:   
  
 \- All access to the internet is restricted to a proxy   
 \- The proxy only allows connections out on port 80 and 443   
 \- CONNECT method is only enabled for 443   
 \- Proxy Authentication is required (NTLM or Basic)   
  
 I like to use both Windows and Unix environments. On Unix tunneling to Github is a bit easier because lots of tools are included.   
  
 Unix   
  
 1\. Download Git. At the time I was writing this I was using Ubuntu so I simply did apt-get install git-core   
  
 2\. Download and install corkscrew (http://www.agroman.net/corkscrew/). This is a tool for tunneling SSH through HTTP proxies.   
  
 3\. Edit or create the file \~/.ssh/config and put the following:   
  
 ProxyCommand /usr/bin/corkscrew proxy.example.com 443 %h %p \~/.ssh/myauth   
  
 Host github.com   
 User git   
 Port 22   
 Hostname github.com   
 IdentityFile "/media/truecrypt1/Keys/GitHubKey.private"   
 TCPKeepAlive yes   
 IdentitiesOnly yes   
  
 Host ssh.github.com   
 User git   
 Port 443   
 Hostname ssh.github.com   
 IdentityFile "/media/truecrypt1/Keys/GitHubKey.private"   
 TCPKeepAlive yes   
 IdentitiesOnly yes   
  
  

*   The ProxyCommand is invoked when ssh needs to make a connection. We are telling ssh to use /usr/bin/corkscrew . This is a 3rd party program that sets up a socket through the HTTP proxy.
*   The program /usr/bin/corkscrew takes as its 5th argument a file containing credentials for your HTTP proxy. Not all proxies need authentication but if you do just put in the file a single line formatted username:password.
*   The Host github.com indicates to ssh that if we are connecting to github.com to use these specific settings. There is nothing special here except we specify the location of the private key that corresponds to the public key we had over in http://www.github.com/
*   Notice we have another entry titled " Host ssh.github.com" . This is to get around proxies that only allow the CONNECT command over 443 (the truly locked down ones). To get around this github setup a whole separate host that listens on port 443. We add both entries here since they are both valid.  
    

 4\. If everything is setup correctly you should be able to run:   
 \# ssh github.com   
  
 Hi tachang! You've successfully authenticated, but GitHub does not provide shell access.   
 Connection to github.com closed.   
  
 If this doesn't work you can run   
 \# ssh ssh.github.com   
  
 And get the exact same thing. If the first command didn't work it means you are using a proxy that blocks CONNECT on port 22. Almost no proxies block CONNECT on port 443 because you need that for SSL.   
  
  
 We get a no shell access message from github because we are trying to obtain a shell and github has it disabled. However this indi

**********
[ssh](/tags/ssh.md)
[github](/tags/github.md)
[git](/tags/git.md)
