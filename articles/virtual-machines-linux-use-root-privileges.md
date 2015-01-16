<properties urlDisplayName="Use root privileges" pageTitle="Azure 上の Linux 仮想マシンでの root 権限の使用" metaKeywords="" description="Azure 上の Linux 仮想マシンで root 権限を使用する方法について説明します。" metaCanonical="" services="virtual-machines" documentationCenter="" title="Using root privileges on Linux virtual machines in Azure" authors="szarkos" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="11/18/2014" ms.author="szark" />




# Azure 上の Linux 仮想マシンでの root 権限の使用

既定では、`root` ユーザーは Azure 上の Linux 仮想マシンで無効になっています。`sudo` コマンドを使用すると、Linux 仮想マシンの昇格した特権でコマンドを実行できます。ただし、システムがプロビジョニングされている方法によって操作が異なります。

1. **SSH キーとパスワード、またはパスワードのみ** - 仮想マシンが証明書 (`.CER` ファイル) とパスワード、またはユーザー名とパスワードだけでプロビジョニングされている場合。この場合は、コマンドを実行する前に `sudo` からユーザーのパスワードの入力が求められます。

2. **SSH のキーのみ** - 仮想マシンが証明書 (`.cer` ファイルまたは `.pem` ファイル) または SSH キー使用してプロビジョニングされ、パスワードがない場合。この場合は、コマンドを実行する前に `sudo` からユーザーのパスワードの入力が求められることは**ありません**。


## SSH キーとパスワード、またはパスワードのみ

SSH キーまたはパスワード認証を使用して Linux 仮想マシンにログオンし、`sudo` を使用してコマンドを実行します。たとえば、次のようになります。

	# sudo <command>
	[sudo] password for azureuser:

この場合はパスワードの入力が求められます。パスワードを入力した後 `sudo` によってコマンドが `root` 権限で実行されます。


## SSH キーのみ

SSH キー認証を使用して Linux 仮想マシンにログオンし、`sudo` を使用してコマンドを実行します。たとえば、次のようになります。

	# sudo <command>

この場合はパスワードの入力が**求められません**。`<enter>` キーを押した後、`sudo` によってコマンドが `root` 権限で実行されます。


<!--HONumber=35.1-->
