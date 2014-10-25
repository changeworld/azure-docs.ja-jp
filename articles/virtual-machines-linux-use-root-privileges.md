<properties linkid="manage-linux-common-tasks-user-root-privileges" urlDisplayName="Use root privileges" pageTitle="Use root privileges on Linux virtual machines in Azure" metaKeywords="" description="Learn how to use root privileges on a Linux virtual machine in Azure." metaCanonical="" services="virtual-machines" documentationCenter="" title="Using root privileges on Linux virtual machines in Azure" authors="timlt" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timlt"></tags>

# Azure 上の Linux 仮想マシンでの root 権限の使用

`sudo` コマンドを使用すると、Linux 仮想マシンの昇格した特権でコマンドを実行できます。ただし、システムがプロビジョニングされている方法によって操作が異なります。

1.  **SSH キーとパスワード、またはパスワードのみ** - 仮想マシンが証明書 (`.CER` ファイル) とパスワード、またはユーザー名とパスワードだけでプロビジョニングされている場合。この場合は、コマンドを実行する前に `sudo` からユーザーのパスワードの入力が求められます。

2.  **SSH のキーのみ** - 仮想マシンが証明書 (`.cer` ファイルまたは `.pem` ファイル) を使用してプロビジョニングされ、パスワードがない場合。この場合は、コマンドを実行する前に `sudo` からユーザーのパスワードの入力は**求められません**。

## SSH キーとパスワード、またはパスワードのみ

SSH キーまたはパスワード認証を使用して Linux 仮想マシンにログオンし、`sudo` を使用してコマンドを実行します。たとえば、次のようになります。

    # sudo <command>
    [sudo] password for azureuser:

この場合はパスワードの入力が求められます。パスワードの入力後、`sudo` によってコマンドが `root` 権限で実行されます。

## SSH キーのみ

SSH キー認証を使用して Linux 仮想マシンにログオンし、`sudo` を使用してコマンドを実行します。たとえば、次のようになります。

    # sudo <command>

この場合はパスワードの入力が**求められません**。`<enter>` を押した後、`sudo` によってコマンドが `root` 権限で実行されます。

