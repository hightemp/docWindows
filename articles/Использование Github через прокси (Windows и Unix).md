# Использование Github через прокси (Windows и Unix)

 Вот довольно стандартный сценарий в большинстве корпораций:   
  
\- Весь доступ к интернету ограничен прокси
\- Прокси разрешает подключения только через порты 80 и 443
\- метод CONNECT включен только для 443
\- Требуется проверка подлинности прокси (NTLM или Basic)  
  
 Мне нравится использовать среды Windows и Unix. В Unix туннелирование на Github немного проще, потому что в него включено множество инструментов.  
  
 Юникс
  
 1\. Скачать Git. В то время, когда я писал это, я использовал Ubuntu, поэтому я просто сделал apt-get install git-core
  
 2\. Загрузите и установите  corkscrew(штопор) (http://www.agroman.net/corkscrew/). Это инструмент для туннелирования SSH через HTTP прокси.
  
 3\. Отредактируйте или создайте файл \~/.ssh/config и добавьте следующее:   

```
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
```  
  

* ProxyCommand вызывается, когда ssh необходимо установить соединение. Мы говорим ssh использовать /usr/bin/corkscrew. Это сторонняя программа, которая устанавливает сокет через HTTP-прокси.
* Программа /usr/bin/corkscrew принимает в качестве 5-го аргумента файл, содержащий учетные данные для вашего HTTP-прокси. Не все прокси-серверы требуют аутентификации, но если вы просто поместите в файл однострочное имя пользователя: пароль.
* Хост github.com указывает ssh, что если мы подключаемся к github.com, чтобы использовать эти конкретные настройки. Здесь нет ничего особенного, за исключением того, что мы указываем расположение закрытого ключа, которое соответствует открытому ключу, который мы использовали в http://www.github.com/
* Обратите внимание, у нас есть еще одна запись под названием «Host ssh.github.com». Это позволяет обойти прокси, которые разрешают команду CONNECT только через 443 (действительно заблокированные). Чтобы обойти эту настройку github, нужно создать отдельный хост, который прослушивает порт 443. Здесь мы добавляем обе записи, так как они действительны. 
    

 4 \. Если все настроено правильно, вы сможете запустить:  

```console
# ssh github.com   
```

Привет тачанг! Вы успешно прошли аутентификацию, но GitHub не предоставляет доступ к оболочке.
 Соединение с github.com закрыто.
  
 Если это не работает, вы можете запустить  

```console
# ssh ssh.github.com   
```

 И получить то же самое. Если первая команда не сработала, это означает, что вы используете прокси, который блокирует CONNECT на порту 22. Почти никакие прокси не блокируют CONNECT на порту 443, потому что это нужно для SSL.
  
  
 Мы получаем сообщение об отсутствии доступа к оболочке от github, потому что мы пытаемся получить оболочку, а в github она отключена. Однако это инди


**********
[ssh](/tags/ssh.md)
[github](/tags/github.md)
[git](/tags/git.md)
