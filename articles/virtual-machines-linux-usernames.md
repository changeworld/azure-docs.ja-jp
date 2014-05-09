<properties linkid="services-linux-user-names" urlDisplayName="Linux でのユーザー名" pageTitle="Azure 上の Linux に対応するユーザー名の選択" metaKeywords="" description="Azure で Linux 仮想マシンに対応するユーザー名を選択する方法について説明します。" metaCanonical="" services="virtual-machines" documentationCenter="" title="Azure 上の Linux に対応するユーザー名の選択" authors="" solutions="" manager="" editor="" />





#Azure 上の Linux に対応するユーザー名の選択#

Azure 上で Linux イメージに対応する仮想マシン インスタンスを作成するときに、プロビジョニングするユーザー名を選択することができます。

Linux では、使用できないユーザー名のセットが定義されています。**これらの名前を使用しないこと**は非常に重要です。これらのユーザー名のいずれかを使用した場合は、イメージをプロビジョニングすることができません。

さらに、サービス管理 API から "ユーザー *xxxxxx* を作成できませんでした" というエラーが返されます。これは、イメージ内にユーザー名が既に存在している場合にも当てはまります。ユーザー名が既に存在する状況は、前回のキャプチャ操作で、イメージ内に既に作成されていたユーザー名をプロビジョニング解除しなかった場合に発生します。

次に、使用できないユーザー名を示します。



OpenSUSE
-------------------
- abrt
- adm
- audio
- bin
- bin
- cdrom
- cgred
- daemon
- dbus
- dialout
- dip
- disk
- floppy
- ftp
- games
- gopher
- haldaemon
- halt
- kmem
- lock
- lp
- mail
- man
- mem
- nfsnobody
- nobody
- ntp
- operator
- oprofile
- postdrop
- postfix
- qpidd
- root
- rpc
- rpcuser
- saslauth
- shutdown
- slocate
- sshd
- stapdev
- stapusr
- sync
- sys
- tape
- test
- tcpdump
- tty
- users
- utempter
- utmp
- uucp
- vcsa
- video
- wheel

SLES
------------------
- audio
- bin
- cdrom
- console
- daemon
- dialout
- disk
- floppy
- ftp
- ftp
- games
- haldaemon
- kmem
- lp
- lp
- mail
- maildrop
- man
- messagebus
- modem
- news
- news
- nobody
- nogroup
- polkituser
- postfix
- public
- root
- shadow
- sshd
- sys
- test
- trusted
- tty
- users
- utmp
- uucp
- uuidd
- video
- wheel
- www
- wwwrun
- xok

 
CentOS
-------------------
- abrt
- adm
- audio
- bin
- cdrom
- cgred
- daemon
- dbus
- dialout
- dip
- disk
- floppy
- ftp
- ftp
- games
- gopher
- haldaemon
- halt
- kmem
- lock
- lp
- mail
- man
- mem
- nfsnobody
- nobody
- ntp
- operator
- oprofile
- postdrop
- postfix
- qpidd
- root
- rpc
- rpcuser
- saslauth
- shutdown
- slocate
- sshd
- stapdev
- stapusr
- sync
- sys
- tape
- test
- tcpdump
- tty
- users
- utempter
- utmp
- uucp
- vcsa
- video
- wheel

UBUNTU
-------------------
- adm
- admin
- audio
- backup
- bin
- cdrom
- crontab
- daemon
- dialout
- dip
- disk
- fax
- floppy
- fuse
- games
- gnats
- irc
- kmem
- landscape
- libuuid
- list
- lp
- mail
- man
- messagebus
- mlocate
- netdev
- news
- nobody
- nogroup
- operator
- plugdev
- proxy
- root
- sasl
- shadow
- src
- ssh
- sshd
- staff
- sudo
- sync
- sys
- syslog
- tape
- tty
- users
- utmp
- uucp
- video
- voice
- whoopsie
- www-data

