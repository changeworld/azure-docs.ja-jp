<properties 
	pageTitle="Azure 上の Linux に対応するユーザー名の選択" 
	description="Azure で Linux 仮想マシンに対応するユーザー名を選択する方法について説明します。" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="szarkos" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/18/2014" 
	ms.author="szark"/>



#Azure 上の Linux に対応するユーザー名の選択#

Linux 仮想マシンを作成するときに、ユーザー名の名前を選択するか、既定の *azureuser* を使用することができます。ほとんどの場合、この新規ユーザーは基本イメージ上に存在せず、プロビジョニング プロセス中に作成されます。VM ユーザーが既に基本 VM イメージ上に存在する場合、Azure Linux エージェントは VM の作成時に渡された情報に基づいて、そのユーザーのパスワードや SSH キーを構成します。

**ただし**、Linux では、使用しない方がよいユーザー名が定められています。UID 0 ～ 99 で定義されている既存のシステム ユーザーを使用して Linux VM をプロビジョニングしようとすると、プロビジョニング プロセスは**失敗**します。代表的な例は、UID が 0 である `root` ユーザーです。

 - 関連項目:[Linux 標準ベース - ユーザー ID の範囲](http://refspecs.linuxfoundation.org/LSB_4.1.0/LSB-Core-generic/LSB-Core-generic/uidrange.html)

Linux 仮想マシンのプロビジョニング時に使用しない方がよいユーザー名を次に示します。使用するとプロビジョニング プロセスが失敗するおそれがあるため、**これらのユーザー名を使用しない**ことをお勧めします。


## openSUSE
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
- テスト
- tcpdump
- tty
- users
- utempter
- utmp
- uucp
- vcsa
- video
- wheel


## SLES
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
- テスト
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

 
## CentOS
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
- テスト
- tcpdump
- tty
- users
- utempter
- utmp
- uucp
- vcsa
- video
- wheel


## Ubuntu
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


<!--HONumber=45--> 
