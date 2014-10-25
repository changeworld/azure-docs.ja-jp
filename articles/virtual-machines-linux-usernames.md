<properties linkid="services-linux-user-names" urlDisplayName="User Names in Linux" pageTitle="Selecting User Names for Linux on Azure" metaKeywords="" description="Learn how to select user names for a Linux virtual machine in Azure." metaCanonical="" services="virtual-machines" documentationCenter="" title="Selecting User Names for Linux on Azure" authors="szark" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="szark"></tags>

# Azure 上の Linux に対応するユーザー名の選択

Azure 上で Linux 仮想マシン インスタンスを作成するときに、プロビジョニングするユーザー名を選択することができます (既定のユーザー名は *azureuser*)。ほとんどの場合、この新規ユーザーは基本イメージ上に存在せず、プロビジョニング プロセス中に作成されます。ユーザーがすでに基本 VM イメージ上に存在する場合、Azure Linux エージェントは VM の作成時に渡されたプロビジョニング構成情報に基づいて、そのユーザーのパスワードや SSH キーを設定します。

**ただし**、Linux では新規ユーザーの作成時に使用すべきでない一連のユーザー名が定められています。UID 0～99で定義されている既存のシステム ユーザーを使用して Linux VM をプロビジョニングしようとすると、プロビジョニング プロセスは**失敗**します。典型例は`root` ユーザー (UID 0) です。

-   関連項目:[Linux 標準ベース - ユーザー ID の範囲][Linux 標準ベース - ユーザー ID の範囲]

以下のリストは、Linux 仮想マシンのプロビジョニング時に使用を避けるべきユーザー名を示します。念のため、Linux VMをプロビジョニングするときは**これらのユーザー名を使用しない**ことをお勧めします。これを使用するとプロセスが失敗する恐れがあります。

## openSUSE

-   abrt
-   adm
-   audio
-   bin
-   bin
-   cdrom
-   cgred
-   daemon
-   dbus
-   dialout
-   dip
-   disk
-   floppy
-   ftp
-   games
-   gopher
-   haldaemon
-   halt
-   kmem
-   lock
-   lp
-   mail
-   man
-   mem
-   nfsnobody
-   nobody
-   ntp
-   operator
-   oprofile
-   postdrop
-   postfix
-   qpidd
-   root
-   rpc
-   rpcuser
-   saslauth
-   shutdown
-   slocate
-   sshd
-   stapdev
-   stapusr
-   sync
-   sys
-   tape
-   テスト
-   tcpdump
-   tty
-   users
-   utempter
-   utmp
-   uucp
-   vcsa
-   video
-   wheel

## SLES

-   audio
-   bin
-   cdrom
-   console
-   daemon
-   dialout
-   disk
-   floppy
-   ftp
-   ftp
-   games
-   haldaemon
-   kmem
-   lp
-   lp
-   mail
-   maildrop
-   man
-   messagebus
-   modem
-   news
-   news
-   nobody
-   nogroup
-   polkituser
-   postfix
-   public
-   root
-   shadow
-   sshd
-   sys
-   テスト
-   trusted
-   tty
-   users
-   utmp
-   uucp
-   uuidd
-   video
-   wheel
-   www
-   wwwrun
-   xok

## CentOS

-   abrt
-   adm
-   audio
-   bin
-   cdrom
-   cgred
-   daemon
-   dbus
-   dialout
-   dip
-   disk
-   floppy
-   ftp
-   ftp
-   games
-   gopher
-   haldaemon
-   halt
-   kmem
-   lock
-   lp
-   mail
-   man
-   mem
-   nfsnobody
-   nobody
-   ntp
-   operator
-   oprofile
-   postdrop
-   postfix
-   qpidd
-   root
-   rpc
-   rpcuser
-   saslauth
-   shutdown
-   slocate
-   sshd
-   stapdev
-   stapusr
-   sync
-   sys
-   tape
-   テスト
-   tcpdump
-   tty
-   users
-   utempter
-   utmp
-   uucp
-   vcsa
-   video
-   wheel

## Ubuntu

-   adm
-   admin
-   audio
-   backup
-   bin
-   cdrom
-   crontab
-   daemon
-   dialout
-   dip
-   disk
-   fax
-   floppy
-   fuse
-   games
-   gnats
-   irc
-   kmem
-   landscape
-   libuuid
-   list
-   lp
-   mail
-   man
-   messagebus
-   mlocate
-   netdev
-   news
-   nobody
-   nogroup
-   operator
-   plugdev
-   proxy
-   root
-   sasl
-   shadow
-   src
-   ssh
-   sshd
-   staff
-   sudo
-   sync
-   sys
-   syslog
-   tape
-   tty
-   users
-   utmp
-   uucp
-   video
-   voice
-   whoopsie
-   www-data

  [Linux 標準ベース - ユーザー ID の範囲]: http://refspecs.linuxfoundation.org/LSB_4.1.0/LSB-Core-generic/LSB-Core-generic/uidrange.html
