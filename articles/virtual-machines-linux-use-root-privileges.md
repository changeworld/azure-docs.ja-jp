<properties 
	pageTitle="Azure 上の Linux 仮想マシンでの root 権限の使用" 
	description="Azure 上の Linux 仮想マシンで root 権限を使用する方法について説明します。" 
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




# Azure 上の Linux 仮想マシンでの root 権限の使用

既定では、`root` ユーザーは Azure 上の Linux 仮想マシンで無効になっています。ユーザーは `sudo` コマンドを使用して、昇格した特権でコマンドを実行できます。ただし、システムがプロビジョニングされている方法によって操作が異なります。

1. **SSH キーとパスワードまたはパスワードのみ** - 仮想マシンは証明書(`.CER`ファイル)、SSH キーとパスワード、またはユーザー名とパスワードのみでプロビジョニングされた場合。この場合は、コマンドを実行する前に `sudo` からユーザーのパスワードの入力が求められます。

2. **SSH キーのみ** - 証明書 (`.cer`または `.pem` ファイル)、SSH キー (パスワードなし) でプロビジョニングされた場合。  この場合は、コマンドを実行する前に `sudo` からユーザーのパスワードの入力は**求められません**。


## SSH キーとパスワード、またはパスワードのみ

SSH キーまたはパスワード認証を使用して Linux 仮想マシンにログオンし、`sudo` を使用してコマンドを実行します。たとえば、次のようになります。

	# sudo <command>
	[sudo] password for azureuser:

この場合はパスワードの入力が求められます。パスワードを入力すると、`sudo` によってコマンドが `root` 権限で実行されます。

`/etc/sudoers.d/waagent` ファイルを編集して、パスワードなし sudo を有効にすることもできます。

	#/etc/sudoers.d/waagent
	azureuser (ALL) = (ALL) NOPASSWD: ALL

この変更によって、azureuser ユーザーはパスワードなしで sudo を実行できます。

## SSH キーのみ

SSH キー認証を使用して Linux 仮想マシンにログオンし、`sudo` を使用してコマンドを実行します。たとえば、次のようになります。

	# sudo <command>

この場合はパスワードの入力が**求められません**。`<enter>` キーを押すと、`sudo` によって `root` 権限でコマンドが実行されます。


<!--HONumber=45--> 
