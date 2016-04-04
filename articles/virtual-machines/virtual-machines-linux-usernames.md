<properties 
	pageTitle="Linux に対応するユーザー名の選択 | Microsoft Azure" 
	description="Azure で Linux 仮想マシンに対応するユーザー名を選択する方法について説明します。" 
	services="virtual-machines-linux" 
	documentationCenter="" 
	authors="szarkos" 
	manager="timlt" 
	editor=""
	tags="azure-service-management,azure-resource-manager" />

<tags 
	ms.service="virtual-machines-linux" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/17/2015" 
	ms.author="szark"/>



#Azure 上の Linux に対応するユーザー名の選択#

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Azure に Linux 仮想マシンをプロビジョニングするとき、後で VM へのログインに使用できる、ルート以外のユーザー名を指定する必要があります。新規ユーザーの名前を選択するか、Azure クラシック ポータルでプロビジョニングする場合は既定の名前である "azureuser" を使用できます。

ほとんどの場合、新規ユーザーは基本イメージ上に存在せず、プロビジョニング プロセス中に作成されます。VM ユーザーが既に基本 VM イメージ上に存在する場合、Azure Linux エージェントは VM の作成時に渡された情報に基づいて、そのユーザーのパスワードや SSH キーを構成します。

**ただし**、Linux では、使用しない方がよいユーザー名が定められています。UID 0 ～ 99 で定義されている既存のシステム ユーザーを使用して Linux VM をプロビジョニングしようとすると、プロビジョニング プロセスは**失敗**します。代表的な例は、UID が 0 である `root` ユーザーです。

 - [Linux 標準ベース - ユーザー ID の範囲](http://refspecs.linuxfoundation.org/LSB_4.1.0/LSB-Core-generic/LSB-Core-generic/uidrange.html)を参照してください。

以下のリストは、Linux 仮想マシンのプロビジョニング時に使用を避けるべきユーザー名を示します。使用するとプロビジョニング プロセスが失敗するおそれがあるため、**これらのユーザー名を使用しない**ことをお勧めします。


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

 

<!---HONumber=AcomDC_0323_2016-->