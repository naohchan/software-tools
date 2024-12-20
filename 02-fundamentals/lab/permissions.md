# File permissions

Log in to your Debian VM for the following exercises.

## Create a user and a group

Create a new user with `sudo adduser NAME` - I'm going to be using `brian` as an example name in these notes. When it asks for a password, you can just use `brian` or something; it will complain about the password being too short but it will create the user anyway. You can skip the GECOS information asking for a full name and phone number---it's just to help an admin contact you if needed.

### Response
```sh
vagrant@debian12:~$ sudo adduser brian
Adding user `brian' ...
Adding new group `brian' (1001) ...
Adding new user `brian' (1001) with group `brian (1001)' ...
Creating home directory `/home/brian' ...
Copying files from `/etc/skel' ...
New password: 
Retype new password: 
passwd: password updated successfully
Changing the user information for brian
Enter the new value, or press ENTER for the default
	Full Name []: 
	Room Number []: 
	Work Phone []: 
	Home Phone []: 
	Other []: 
Is the information correct? [Y/n] y
Adding new user `brian' to supplemental / extra groups `users' ...
Adding user `brian' to group `users' ...
```

Check the user and group files with `tail /etc/passwd` and `tail /etc/group` to check that the new user has been created - `tail` displays the last 10 lines of a file by default; `tail -n N FILE` would display the last N lines. Your new user `brian` (or whatever you called them) should appear in both files. Also check with `ls -l /home` that the home directory for Brian exists and is set to the correct user and group.

```sh
vagrant@debian12:~$ tail /etc/passwd
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
systemd-network:x:998:998:systemd Network Management:/:/usr/sbin/nologin
systemd-timesync:x:997:997:systemd Time Synchronization:/:/usr/sbin/nologin
messagebus:x:100:107::/nonexistent:/usr/sbin/nologin
sshd:x:101:65534::/run/sshd:/usr/sbin/nologin
vagrant:x:1000:1000:vagrant,,,:/home/vagrant:/bin/bash
memcache:x:102:110:Memcached,,,:/nonexistent:/bin/false
postfix:x:103:112::/var/spool/postfix:/usr/sbin/nologin
vboxadd:x:999:2::/var/run/vboxadd:/sbin/nologin
brian:x:1001:1001:,,,:/home/brian:/bin/bash

vagrant@debian12:~$ tail /etc/group
_ssh:x:108:
vagrant:x:1000:
plocate:x:109:
memcache:x:110:
ssl-cert:x:111:
postfix:x:112:
postdrop:x:113:
vboxsf:x:996:
vboxdrmipc:x:995:
brian:x:1001:

vagrant@debian12:~$ tail -n 5 /etc/passwd
vagrant:x:1000:1000:vagrant,,,:/home/vagrant:/bin/bash
memcache:x:102:110:Memcached,,,:/nonexistent:/bin/false
postfix:x:103:112::/var/spool/postfix:/usr/sbin/nologin
vboxadd:x:999:2::/var/run/vboxadd:/sbin/nologin
brian:x:1001:1001:,,,:/home/brian:/bin/bash

vagrant@debian12:~$ tail -n 5 /etc/group
postfix:x:112:
postdrop:x:113:
vboxsf:x:996:
vboxdrmipc:x:995:
brian:x:1001:

vagrant@debian12:~$ ls -l /home
total 8
drwx------ 2 brian   brian   4096 Nov 17 11:51 brian
drwx------ 8 vagrant vagrant 4096 Nov 17 11:50 vagrant

```
Time to change user: `su brian` and enter the password. Notice that the prompt has changed to `brian@debian12:/home/vagrant$` (at least if you started off in that folder). So the user has changed, and because `/home/vagrant` is no longer the current user's home directory, it gets written out in full. Run `cd` to go home followed by `pwd` and check that you are now in `/home/brian` or whatever you called your new user.

```sh
vagrant@debian12:~$ su brian
Password: 
brian@debian12:/home/vagrant$ cd
brian@debian12:~$ pwd
/home/brian
```

Next, create a user `nigel` (or some other name) add both your two new users, but not `vagrant`, to the group `users` (which already exists) using the command `sudo usermod -aG GROUPNAME USERNAME`, where group and username are changed accordingly. Note: `brian` cannot use sudo, so you have to exit his terminal to get back to one running as the user `vagrant` for this.

```sh

vagrant@debian12:/home$ ls
brian  nigel  vagrant

vagrant@debian12:/home$ cd vagrant

vagrant@debian12:~$ sudo adduser nigel
Adding user `nigel' ...
Adding new group `nigel' (1002) ...
Adding new user `nigel' (1002) with group `nigel (1002)' ...
Creating home directory `/home/nigel' ...
Copying files from `/etc/skel' ...
New password: 
Retype new password: 
passwd: password updated successfully
Changing the user information for nigel
Enter the new value, or press ENTER for the default
	Full Name []: 
	Room Number []: 
	Work Phone []: 
	Home Phone []: 
	Other []: 
Is the information correct? [Y/n] y
Adding new user `nigel' to supplemental / extra groups `users' ...
Adding user `nigel' to group `users' ...

vagrant@debian12:~$ sudo usermod -aG users brian

vagrant@debian12:~$ sudo usermod -aG users nigel


vagrant@debian12:/home$ groups nigel
nigel : nigel users
vagrant@debian12:/home$ id nigel
uid=1002(nigel) gid=1002(nigel) groups=1002(nigel),100(users)

vagrant@debian12:/home$ grep '^users:' /etc/group
users:x:100:vagrant,brian,nigel

vagrant@debian12:/home$ cat /etc/group
...
users:x:100:vagrant,brian,nigel
...


```
## Explore file permissions

As user `brian` (or whatever you called your first new user), set up your home directory using what you learnt in the videos so that

```sh
brian@debian12:/home$ chmod +040 /home/brian
brian@debian12:/home$ ls -la
total 20
drwxr-xr-x  5 root    root    4096 Nov 17 12:12 .
drwxr-xr-x 19 root    root    4096 Nov 16 13:36 ..
d---rw----  3 brian   users   4096 Nov 17 12:35 brian
drwx------  2 nigel   nigel   4096 Nov 17 12:12 nigel
drwx------  8 vagrant vagrant 4096 Nov 17 11:50 vagrant
brian@debian12:/home$ chmod +700 /home/brian
brian@debian12:/home$ ls -la
total 20
drwxr-xr-x  5 root    root    4096 Nov 17 12:12 .
drwxr-xr-x 19 root    root    4096 Nov 16 13:36 ..
drwxrw----  3 brian   users   4096 Nov 17 12:35 brian
drwx------  2 nigel   nigel   4096 Nov 17 12:12 nigel
drwx------  8 vagrant vagrant 4096 Nov 17 11:50 vagrant
brian@debian12:/home$ chmod +000 /home/brian
brian@debian12:/home$ ls -la
total 20
drwxr-xr-x  5 root    root    4096 Nov 17 12:12 .
drwxr-xr-x 19 root    root    4096 Nov 16 13:36 ..
drwxrw----  3 brian   users   4096 Nov 17 12:35 brian
drwx------  2 nigel   nigel   4096 Nov 17 12:12 nigel
drwx------  8 vagrant vagrant 4096 Nov 17 11:50 vagrant
brian@debian12:/home$ chmod -060 /home/brian
brian@debian12:/home$ ls -la
total 20
drwxr-xr-x  5 root    root    4096 Nov 17 12:12 .
drwxr-xr-x 19 root    root    4096 Nov 16 13:36 ..
drwx------  3 brian   users   4096 Nov 17 12:35 brian
drwx------  2 nigel   nigel   4096 Nov 17 12:12 nigel
drwx------  8 vagrant vagrant 4096 Nov 17 11:50 vagrant

```


  * You can do everything (rwx).
  * Members of the `users` group can list files and change to your home directory, but not add/remove files. You will need to change the group of your home directory to `users` for this, using the command `chgrp -R GROUPNAME DIRECTORY`.
  * Everyone else cannot do anything with your home directory.

Create a file in your home directory, e.g. `nano readme.txt` then add some content.

Check, by using `su USERNAME` to log in as the different users, that:
  * `nigel` can view Brian's home directory but not create files there; 
  * `nigel` can view but not edit Brian's readme file; 
  * `vagrant` cannot list files in or enter Brian's home directory at all. What happens when you try?

```sh
brian@debian12:/home$ chmod +050 /home/brian

brian@debian12:/home$ ls -la
total 20
drwxr-xr-x  5 root    root    4096 Nov 17 12:12 .
drwxr-xr-x 19 root    root    4096 Nov 16 13:36 ..
drwxr-x---  3 brian   users   4096 Nov 17 12:35 brian
drwx------  3 nigel   nigel   4096 Nov 17 15:14 nigel
drwx------  8 vagrant vagrant 4096 Nov 17 11:50 vagrant

nigel@debian12:/home/brian$ ls
readme.txt  test_1.txt  test_2.txt  test_3.txt  test_4.txt  test_5.txt
nigel@debian12:/home/brian$ nano readme.txt

vagrant@debian12:/home$ cd brian
bash: cd: brian: Permission denied

vagrant@debian12:/$ sudo su -
root@debian12:~# cd /home/brian
root@debian12:/home/brian# ls
readme.txt  test_1.txt  test_2.txt  test_3.txt  test_4.txt  test_5.txt
root@debian12:/home/brian# nano readme.txt

```

_Of course, vagrant can use sudo to get around all these restrictions. Permissions do not protect you from anyone who can become root._

Also as `brian`, make a `private` subdirectory in your home folder that no-one but you can access (read, write or execute). Create a file `secret.txt` in there with `nano private/secret.txt` as user `brian` from Brian's home directory, and put something in it. Do not change any permissions on `secret.txt` itself.

Check as Nigel that you can see the folder itself, but not cd into it nor list the file. Check that even knowing the file name (`cat /home/brian/private/secret.txt`) as Nigel doesn't work.

Using `ls -l` as Brian in both `~` and `~/private`, compare the entries for the files `~/readme.txt`, `~/private/secret.txt` and the folder `~/private`. Why do the groups of the two files differ?

Note that, even though the secret file has read permissions for everyone by default, Nigel cannot read it. The rule is that you need permissions on the whole path from `/` to a file to be able to access it.

```c
brian@debian12:~$ ls -la
total 52
drwxr-x--- 4 brian users 4096 Nov 17 16:10 .
drwxr-xr-x 5 root  root  4096 Nov 17 12:12 ..
-rw-r--r-- 1 brian users  220 Nov 17 11:51 .bash_logout
-rw-r--r-- 1 brian users 3520 Nov 17 11:51 .bashrc
drwxr-xr-x 3 brian users 4096 Nov 17 12:30 .local
drwxr-xr-x 2 brian brian 4096 Nov 17 16:14 private
-rw-r--r-- 1 brian users  807 Nov 17 11:51 .profile
-rw-r--r-- 1 brian users   26 Nov 17 15:59 readme.txt
-rwxr--r-- 1 brian users    6 Nov 17 12:30 test_1.txt
-rw-r--r-- 1 brian users    6 Nov 17 12:30 test_2.txt
-rw-r--r-- 1 brian users    6 Nov 17 12:31 test_3.txt
-rw-r--r-- 1 brian users    6 Nov 17 12:31 test_4.txt
-rw-r--r-- 1 brian users    6 Nov 17 12:31 test_5.txt
brian@debian12:~$ chmod -011 /home/brian/private

brian@debian12:~$ ls -la
total 52
drwxr-x--- 4 brian users 4096 Nov 17 16:10 .
drwxr-xr-x 5 root  root  4096 Nov 17 12:12 ..
-rw-r--r-- 1 brian users  220 Nov 17 11:51 .bash_logout
-rw-r--r-- 1 brian users 3520 Nov 17 11:51 .bashrc
drwxr-xr-x 3 brian users 4096 Nov 17 12:30 .local
drwxr--r-- 2 brian brian 4096 Nov 17 16:14 private
-rw-r--r-- 1 brian users  807 Nov 17 11:51 .profile
-rw-r--r-- 1 brian users   26 Nov 17 15:59 readme.txt
-rwxr--r-- 1 brian users    6 Nov 17 12:30 test_1.txt
-rw-r--r-- 1 brian users    6 Nov 17 12:30 test_2.txt
-rw-r--r-- 1 brian users    6 Nov 17 12:31 test_3.txt
-rw-r--r-- 1 brian users    6 Nov 17 12:31 test_4.txt
-rw-r--r-- 1 brian users    6 Nov 17 12:31 test_5.txt
```

```sh
brian@debian12:~$ ls -l ~/private/secret.txt
-rw-r--r-- 1 brian brian 26 Nov 17 16:14 /home/brian/private/secret.txt

brian@debian12:~$ ls -l ~/readme.txt
-rw-r--r-- 1 brian users 26 Nov 17 15:59 /home/brian/readme.txt

brian@debian12:~$ ls -l ~/private
total 4
-rw-r--r-- 1 brian brian 26 Nov 17 16:14 secret.txt

nigel@debian12:/home/brian$ ls
private  readme.txt  test_1.txt  test_2.txt  test_3.txt  test_4.txt  test_5.txt

nigel@debian12:/home/brian$ cd private
bash: cd: private: Permission denied

nigel@debian12:/home/brian$ cd ..

nigel@debian12:/home$ cat /home/brian/private/secret.txt
cat: /home/brian/private/secret.txt: Permission denied

nigel@debian12:/home$ cd

nigel@debian12:~$ cat /home/brian/private/secret.txt
cat: /home/brian/private/secret.txt: Permission denied
```
_This is another reminder that if you want to store private files on a lab machine, then put it in a folder that is only accessible to you. Other students can read your home directory by default, and they would be able to look at your work. This has led to plagiarism problems in the past, but good news: we keep logs and can usually figure out what happened! `:-)`._

_Altenatively you could remove permissions from everyone else on your home directory there, but this prevents you from being able to share files in specific folders that you do want to share with other students._

## Setuid

We are going to create a file to let Nigel (and others in the users group) send Brian messages which go in a file in his home directory.

As Brian, create a file `message-brian.c` in your home directory and add the following lines:

```C
#include <stdio.h>
#include <stdlib.h>

const char *filename ="/home/brian/messages.txt";

int main(int argc, char **argv) {
  if (argc != 2) {
    puts("Usage: message-brian MESSAGE");
    return 1;
  }
  FILE *file = fopen(filename, "a");
  if (file == NULL) {
    puts("Error opening file");
    return 2;
  }
  int r = fputs(argv[1], file);
  if (r == EOF) {
    puts("Error writing message");
    return 2;
  }
  r = fputc('\n', file);
  if (r == EOF) {
    puts("Error writing newline");
    return 2;
  }
  fclose(file);
  return 0;
}
```

Compile it with `gcc -Wall message-brian.c -o message-brian` (you should not get any warnings) and check with `ls -l`, you will see a line like

    -rwxr-xr-x    1 brian     brian         19984 Oct 28 13:26 message-brian

These are the default permissions for a newly created executable file; note that gcc has set the three `+x` bits for you. Still as Brian, run `chmod u+s message-brian` and check the file again: you should now see `-rwsr-xr-x` for the file permissions. The `s` is the setuid bit.

As Nigel (`su nigel`), go into Brian's home directory and run `./message-brian "Hi from Nigel!"`. The quotes are needed here because the program accepts only a single argument.

Now run `ls -l` and notice that a `messages.txt` has appeared with owner and group `brian`. Check the contents with `cat messages.txt`. Although Nigel cannot create and edit files in Brian's home directory himself (he can't edit `messages.txt` for example, although he can read it), the program `message-brian` ran as Brian, which let it create the file. Nigel can send another message like this (`./message-brian "Hi again!"`), which gets appended to the file: try this out.


```sh
brian@debian12:~$ ls -l
total 48
-rwxr-xr-x 1 brian brian 16184 Nov 17 16:52 message-brian
-rw-r--r-- 1 brian brian   542 Nov 17 16:51 message-brian.c
drwxr--r-- 2 brian brian  4096 Nov 17 16:14 private
-rw-r--r-- 1 brian users    26 Nov 17 15:59 readme.txt
-rwxr--r-- 1 brian users     6 Nov 17 12:30 test_1.txt
-rw-r--r-- 1 brian users     6 Nov 17 12:30 test_2.txt
-rw-r--r-- 1 brian users     6 Nov 17 12:31 test_3.txt
-rw-r--r-- 1 brian users     6 Nov 17 12:31 test_4.txt
-rw-r--r-- 1 brian users     6 Nov 17 12:31 test_5.txt

brian@debian12:~$ chmod u+s message-brian

brian@debian12:~$ ls -l
total 48
-rwsr-xr-x 1 brian brian 16184 Nov 17 16:52 message-brian
-rw-r--r-- 1 brian brian   542 Nov 17 16:51 message-brian.c
drwxr--r-- 2 brian brian  4096 Nov 17 16:14 private
-rw-r--r-- 1 brian users    26 Nov 17 15:59 readme.txt
-rwxr--r-- 1 brian users     6 Nov 17 12:30 test_1.txt
-rw-r--r-- 1 brian users     6 Nov 17 12:30 test_2.txt
-rw-r--r-- 1 brian users     6 Nov 17 12:31 test_3.txt
-rw-r--r-- 1 brian users     6 Nov 17 12:31 test_4.txt
-rw-r--r-- 1 brian users     6 Nov 17 12:31 test_5.txt

brian@debian12:~$ su nigel
Password:

nigel@debian12:/home/brian$ ls
message-brian    private     test_1.txt  test_3.txt  test_5.txt
message-brian.c  readme.txt  test_2.txt  test_4.txt

nigel@debian12:/home/brian$ ./message-brian "Hi from Nigel!"

nigel@debian12:/home/brian$ ls -l
total 52
-rwsr-xr-x 1 brian brian 16184 Nov 17 16:52 message-brian
-rw-r--r-- 1 brian brian   542 Nov 17 16:51 message-brian.c
-rw-r--r-- 1 brian nigel    15 Nov 17 16:54 messages.txt
drwxr--r-- 2 brian brian  4096 Nov 17 16:14 private
-rw-r--r-- 1 brian users    26 Nov 17 15:59 readme.txt
-rwxr--r-- 1 brian users     6 Nov 17 12:30 test_1.txt
-rw-r--r-- 1 brian users     6 Nov 17 12:30 test_2.txt
-rw-r--r-- 1 brian users     6 Nov 17 12:31 test_3.txt
-rw-r--r-- 1 brian users     6 Nov 17 12:31 test_4.txt
-rw-r--r-- 1 brian users     6 Nov 17 12:31 test_5.txt

nigel@debian12:/home/brian$ cat messages.txt
Hi from Nigel!

nigel@debian12:/home/brian$ nano messages.txt

nigel@debian12:/home/brian$ ./message-brian "Hi again!"

nigel@debian12:/home/brian$ ls -la
total 76
drwxr-x--- 4 brian users  4096 Nov 17 16:54 .
drwxr-xr-x 5 root  root   4096 Nov 17 12:12 ..
-rw-r--r-- 1 brian users   220 Nov 17 11:51 .bash_logout
-rw-r--r-- 1 brian users  3520 Nov 17 11:51 .bashrc
drwxr-xr-x 3 brian users  4096 Nov 17 12:30 .local
-rwsr-xr-x 1 brian brian 16184 Nov 17 16:52 message-brian
-rw-r--r-- 1 brian brian   542 Nov 17 16:51 message-brian.c
-rw-r--r-- 1 brian nigel    25 Nov 17 17:02 messages.txt
drwxr--r-- 2 brian brian  4096 Nov 17 16:14 private
-rw-r--r-- 1 brian users   807 Nov 17 11:51 .profile
-rw-r--r-- 1 brian users    26 Nov 17 15:59 readme.txt
-rwxr--r-- 1 brian users     6 Nov 17 12:30 test_1.txt
-rw-r--r-- 1 brian users     6 Nov 17 12:30 test_2.txt
-rw-r--r-- 1 brian users     6 Nov 17 12:31 test_3.txt
-rw-r--r-- 1 brian users     6 Nov 17 12:31 test_4.txt
-rw-r--r-- 1 brian users     6 Nov 17 12:31 test_5.txt

nigel@debian12:/home/brian$ nano messages.txt

nigel@debian12:/home/brian$ su brian
Password:

brian@debian12:~$ ls -la
total 76
drwxr-x--- 4 brian users  4096 Nov 17 16:54 .
drwxr-xr-x 5 root  root   4096 Nov 17 12:12 ..
-rw-r--r-- 1 brian users   220 Nov 17 11:51 .bash_logout
-rw-r--r-- 1 brian users  3520 Nov 17 11:51 .bashrc
drwxr-xr-x 3 brian users  4096 Nov 17 12:30 .local
-rwsr-xr-x 1 brian brian 16184 Nov 17 16:52 message-brian
-rw-r--r-- 1 brian brian   542 Nov 17 16:51 message-brian.c
-rw-r--r-- 1 brian nigel    25 Nov 17 17:02 messages.txt
drwxr--r-- 2 brian brian  4096 Nov 17 16:14 private
-rw-r--r-- 1 brian users   807 Nov 17 11:51 .profile
-rw-r--r-- 1 brian users    26 Nov 17 15:59 readme.txt
-rwxr--r-- 1 brian users     6 Nov 17 12:30 test_1.txt
-rw-r--r-- 1 brian users     6 Nov 17 12:30 test_2.txt
-rw-r--r-- 1 brian users     6 Nov 17 12:31 test_3.txt
-rw-r--r-- 1 brian users     6 Nov 17 12:31 test_4.txt
-rw-r--r-- 1 brian users     6 Nov 17 12:31 test_5.txt

brian@debian12:~$ ./message-brian
Usage: message-brian MESSAGE

brian@debian12:~$ ls -la
total 76
drwxr-x--- 4 brian users  4096 Nov 17 16:54 .
drwxr-xr-x 5 root  root   4096 Nov 17 12:12 ..
-rw-r--r-- 1 brian users   220 Nov 17 11:51 .bash_logout
-rw-r--r-- 1 brian users  3520 Nov 17 11:51 .bashrc
drwxr-xr-x 3 brian users  4096 Nov 17 12:30 .local
-rwsr-xr-x 1 brian brian 16184 Nov 17 16:52 message-brian
-rw-r--r-- 1 brian brian   542 Nov 17 16:51 message-brian.c
-rw-r--r-- 1 brian nigel    25 Nov 17 17:02 messages.txt
drwxr--r-- 2 brian brian  4096 Nov 17 16:14 private
-rw-r--r-- 1 brian users   807 Nov 17 11:51 .profile
-rw-r--r-- 1 brian users    26 Nov 17 15:59 readme.txt
-rwxr--r-- 1 brian users     6 Nov 17 12:30 test_1.txt
-rw-r--r-- 1 brian users     6 Nov 17 12:30 test_2.txt
-rw-r--r-- 1 brian users     6 Nov 17 12:31 test_3.txt
-rw-r--r-- 1 brian users     6 Nov 17 12:31 test_4.txt
-rw-r--r-- 1 brian users     6 Nov 17 12:31 test_5.txt

brian@debian12:~$ nano messages.txt

brian@debian12:~$ su nigel
Password:

nigel@debian12:/home/brian$ nano messages.txt

nigel@debian12:/home/brian$ ./message-brian "Hi xd!"

nigel@debian12:/home/brian$ nano messages.txt

nigel@debian12:/home/brian$ su brian
Password:

brian@debian12:~$ ls -la
total 76
drwxr-x--- 4 brian users  4096 Nov 17 17:05 .
drwxr-xr-x 5 root  root   4096 Nov 17 12:12 ..
-rw-r--r-- 1 brian users   220 Nov 17 11:51 .bash_logout
-rw-r--r-- 1 brian users  3520 Nov 17 11:51 .bashrc
drwxr-xr-x 3 brian users  4096 Nov 17 12:30 .local
-rwsr-xr-x 1 brian brian 16184 Nov 17 16:52 message-brian
-rw-r--r-- 1 brian brian   542 Nov 17 16:51 message-brian.c
-rw-r--r-- 1 brian nigel    35 Nov 17 17:05 messages.txt
drwxr--r-- 2 brian brian  4096 Nov 17 16:14 private
-rw-r--r-- 1 brian users   807 Nov 17 11:51 .profile
-rw-r--r-- 1 brian users    26 Nov 17 15:59 readme.txt
-rwxr--r-- 1 brian users     6 Nov 17 12:30 test_1.txt
-rw-r--r-- 1 brian users     6 Nov 17 12:30 test_2.txt
-rw-r--r-- 1 brian users     6 Nov 17 12:31 test_3.txt
-rw-r--r-- 1 brian users     6 Nov 17 12:31 test_4.txt
-rw-r--r-- 1 brian users     6 Nov 17 12:31 test_5.txt

both can use it!

```

This shows how setuid programs can be used to allow other users to selectively perform specific tasks under a different user account.

**Warning**: writing your own setuid programs is extremely dangerous if you don't know the basics of secure coding and hacking C programs, because a bug in such a program could let someone take over your user account. The absolute minimum you should know is the contents of our security units up to and including 4th year.

A general task for a security analyst might be finding all files with the setuid bit set on a system. You can try this yourself, but return to a vagrant shell first so that you're allowed to use sudo:

    sudo find / -perm /4000

You might get some errors relating to `/proc` files, which you can ignore: these are subprocesses that find uses to look at individual files.

Apart from `message-brian`, you'll find a few files by default: `sudo`, `mount`, `umount` and `su`. The first one you already know; look up what the next two do and think about why they are setuid. Specifically, what kinds of (un)mounting are non-root users allowed to do according to the manual pages?

### Response
Why mount and umount are setuid:
Purpose of mount and umount:

mount: This command is used to attach file systems (e.g., disks, partitions, or network shares) to the file system hierarchy.
umount: This command detaches file systems from the hierarchy.
Why are they setuid?

setuid allows a program to execute with the privileges of the file owner (usually root), regardless of who runs it. This is essential because mounting and unmounting filesystems typically require elevated privileges.
Non-root user permissions:

Non-root users can mount and unmount certain filesystems if the system is configured to allow it.
From the manual pages (man mount):
Users can mount filesystems if the filesystem entry in /etc/fstab includes the user or users option.
user: Allows a non-root user to mount a specific filesystem.
users: Any user can mount and unmount the filesystem.
Example entry in /etc/fstab:
```sh
/dev/sdb1 /mnt/usb vfat user,noauto 0 0
```
This allows non-root users to mount a USB drive with mount /mnt/usb.

Look up the `passwd` program in the manual pages.  Why might that program need to be setuid?

### Response
Why passwd is setuid:
Purpose of passwd:

The passwd command allows users to change their own passwords or administrators to change the password of other users.
Why does it need setuid?

Passwords are stored in a secure system file (/etc/shadow) that is only readable and writable by root.
When a regular user runs passwd to change their password:
The setuid bit allows the command to execute with root privileges temporarily to modify /etc/shadow.
Without setuid, regular users wouldn’t have the permissions to update their own passwords.
Security concerns:

Programs with setuid are a common target for exploitation, so their implementation must be carefully written to avoid vulnerabilities like buffer overflows or privilege escalations.

Summary:
mount and umount: Setuid ensures non-root users can safely perform limited mounting/unmounting operations as allowed by system configurations.
passwd: Setuid allows secure modification of the password database (/etc/shadow) by regular users.

## Sudo

Make sure your terminal is running as `brian` and try a `sudo ls`. You will see a general message, you will be asked for your password, and then you will get the error `brian is not in the sudoers file.  This incident will be reported.` (This means that an entry has been logged in `/var/log/messages`.)

So, `brian` can currently not use sudo. Switch back to `vagrant` and run the command `sudo cat /etc/sudoers`. Everything is commented out except `root ALL=(ALL) ALL` and the last line `#includedir /etc/sudoers.d` (this is not a comment!) which contains a single file `vagrant` with the line `vagrant ALL=(ALL) NOPASSWD: ALL` which is why vagrant can use sudo in the first place.

However, note the commented lines such as

    # %wheel ALL=(ALL) NOPASSWD: ALL
    # %sudo ALL=(ALL) ALL

If uncommented, the first one would let everyone in group wheel run commands using sudo (this is the default on some other Linux distributions), whereas the second one would allow everyone in the group `sudo` to do this, but would prompt for their own password beforehand.

Let's allow people in the users group to reboot the machine. Open a root shell with `sudo su` as user `vagrant`; this is so we don't get locked out if we break sudo.

Edit the sudoers file with `visudo` as root, and add the following line:

    %users ALL=(ALL) /sbin/reboot

and save the sudoers file.

###Response

```c

brian@debian12:~$ sudo ls
[sudo] password for brian: 
brian is not in the sudoers file.
This incident has been reported to the administrator.

brian@debian12:~$ su vagrant
Password:

vagrant@debian12:/home/brian$ sudo cat /etc/sudoers
#
# This file MUST be edited with the 'visudo' command as root.
#
# Please consider adding local content in /etc/sudoers.d/ instead of
# directly modifying this file.
.
.
.

vagrant@debian12:/home/brian$ cd

vagrant@debian12:~$ cd /etc/sudoers.d

vagrant@debian12:/etc/sudoers.d$ ls
README  vagrant

vagrant@debian12:/etc/sudoers.d$ nano vagrant

vagrant@debian12:/etc/sudoers.d$ sudo nano vagrant

vagrant@debian12:/etc/sudoers.d$ sudo cat /etc/sudoers
#
# This file MUST be edited with the 'visudo' command as root.
#
.
.
.

vagrant@debian12:/etc/sudoers.d$ sudo visudo /etc/sudoers


```


**Warning:**: Never edit `/etc/sudoers` directly and *always* use `visudo` instead.  If you make a mistake and add a syntax error to the file then `sudo` will refuse to work.  If your root account doesn't have a password (some people don't like that as a security precaution) then you'll have to spend the next half-hour figuring out how to break into your own computer and wrestle back control.  There is almost always a command to check a config file before replacing the current one: the same advice also applies to the ssh config files.  If you break them you might have to travel to wherever the server is with a keyboard and a monitor.

You can now switch back to `brian` (check the prompt to make sure you are Brian) and do `sudo reboot`. After asking for Brian's password, the virtual machine will now reboot, which you notice because you get kicked out of your ssh connection. Another `vagrant ssh` after a few seconds will get you back in again.

(Note: After rebooting, your `/shared` shared folder might not work. In this case, log out and do `vagrant halt` then `vagrant up` and `vagrant ssh` again on the host machine. When vagrant boots your VM, it automatically sets up the shared folder, but this doesn't always work if you reboot the VM yourself.)
