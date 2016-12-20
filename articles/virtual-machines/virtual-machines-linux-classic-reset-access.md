---
title: "CLI からの Linux VM のパスワードと SSH キーのリセット | Microsoft Docs"
description: "Azure コマンド ライン インターフェイス (CLI) から VMAccess 拡張機能を使用して、Linux VM のパスワードまたは SSH キーをリセットし、SSH 構成を修正し、ディスクの整合性チェックを実行する方法について説明します。"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: d975eb70-5ff1-40d1-a634-8dd2646dcd17
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 06/14/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: b820e3011a1cd5472604e8cfa98fc22f9a4ebf77


---
# <a name="how-to-reset-a-linux-vm-password-or-ssh-key-fix-the-ssh-configuration-and-check-disk-consistency-using-the-vmaccess-extension"></a>VMAccess 拡張機能を使用して、Linux VM のパスワードまたは SSH キーをリセットし、SSH 構成を修正し、ディスクの整合性チェックを実行する方法について説明します。
パスワードを忘れたため、Secure Shell (SSH) キーが正しくないため、または SSH 構成に問題があるために、Azure の Linux 仮想マシンに接続できない場合は、Azure CLI で VMAccessForLinux 拡張機能を使用して、パスワードまたは SSH キーのリセット、SSH 構成の修正、ディスクの整合性のチェックを行います。 

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[Resource Manager モデルを使用してこれらの手順を実行する](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess)方法について説明します。

Azure CLI を使用すると、コマンド ライン インターフェイス (Bash、ターミナル、コマンド プロンプト) から **azure vm extension set** コマンドを使用してコマンドにアクセスできます。 拡張機能の詳しい使用方法については、 **azure help vm extension set** を実行します。

Azure CLI を使用すると、次のタスクを実行できます。

* [パスワードのリセット](#pwresetcli)
* [SSH キーのリセット](#sshkeyresetcli)
* [パスワードと SSH キーのリセット](#resetbothcli)
* [新しい sudo ユーザー アカウントの作成](#createnewsudocli)
* [SSH 構成のリセット](#sshconfigresetcli)
* [ユーザーの削除](#deletecli)
* [VMAccess 拡張機能の状態の表示](#statuscli)
* [追加されたディスクの整合性のチェック](#checkdisk)
* [Linux VM での追加されたディスクの修復](#repairdisk)

## <a name="prerequisites"></a>前提条件
次の手順を実行する必要があります。

* [Azure CLI をインストール](../xplat-cli-install.md)して、ログオンし、アカウントに関連付けられている Azure のリソースを使用する[サブスクリプションに接続](../xplat-cli-connect.md)する必要があります。
* コマンド プロンプトで以下の内容を入力して、クラシック デプロイメント モデルの正しいモードを設定します。
  
        azure config mode asm
* 新しいパスワードまたは一連の SSH キー (いずれかをリセットする場合)。 SSH の構成をリセットする場合、これらは必要ありません。

## <a name="a-namepwresetcliareset-the-password"></a><a name="pwresetcli"></a>パスワードのリセット
1. 次の内容を含む PrivateConf.json という名前のファイルを作成します。 ブラケットおよび &#60;placeholder&#62の値を手元の情報に置き換えます。
   
        {
        "username":"<currentusername>",
        "password":"<newpassword>",
        "expiration":"<2016-01-01>"
        }
2. &#60;vmname&#62; を使用中の仮想マシンの名前に置き換えて、次のコマンドを実行します。
   
        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* –-private-config-path PrivateConf.json

## <a name="a-namesshkeyresetcliareset-the-ssh-key"></a><a name="sshkeyresetcli"></a>SSH キーのリセット
1. 次の内容を含む PrivateConf.json という名前のファイルを作成します。 ブラケットおよび &#60;placeholder&#62の値を手元の情報に置き換えます。
   
        {
        "username":"<currentusername>",
        "ssh_key":"<contentofsshkey>"
        }
2. &#60;vmname&#62; を使用中の仮想マシンの名前に置き換えて、次のコマンドを実行します。
   
        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="a-nameresetbothcliareset-both-the-password-and-the-ssh-key"></a><a name="resetbothcli"></a>パスワードと SSH キーのリセット
1. 次の内容を含む PrivateConf.json という名前のファイルを作成します。 ブラケットおよび &#60;placeholder&#62の値を手元の情報に置き換えます。
   
        {
        "username":"<currentusername>",
        "ssh_key":"<contentofsshkey>",
        "password":"<newpassword>"
        }
2. &#60;vmname&#62; を使用中の仮想マシンの名前に置き換えて、次のコマンドを実行します。
   
        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="a-namecreatenewsudocliacreate-a-new-sudo-user-account"></a><a name="createnewsudocli"></a>新しい sudo ユーザー アカウントの作成
自身のユーザー名を忘れた場合は、VMAccess を使用して、sudo 権限を持つ新しいユーザーを作成できます。 この場合、既存のユーザー名とパスワードは変更されません。

パスワードを使用したアクセス権を持つ新しい sudo ユーザーを作成するには、「 [パスワードのリセット](#pwresetcli) 」のスクリプトを使用して、新しいユーザー名を指定します。

SSH キーを使用したアクセス権を持つ新しい sudo ユーザーを作成するには、「 [SSH キーのリセット](#sshkeyresetcli) 」のスクリプトを使用して、新しいユーザー名を指定します。

[パスワードと SSH キーをリセット](#resetbothcli) して、パスワードと SSH キーの両方を使用したアクセス権を持つ新しいユーザーを作成することもできます。

## <a name="a-namesshconfigresetcliareset-the-ssh-configuration"></a><a name="sshconfigresetcli"></a>SSH 構成のリセット
SSH の構成が望ましい状態でない場合は、VM にアクセスできなくなる可能性もあります。 VMAccess 拡張機能を使用して、構成を既定の状態にリセットすることができます。 そのために必要なのは、"reset_ssh" キーを "True" に設定することだけです。 拡張機能によって SSH サーバーが再起動し、VM 上の SSH ポートが開いて、SSH 構成が既定値にリセットされます。 ユーザー アカウント (名前、パスワード、または SSH キー) は変更されません。

> [!NOTE]
> リセットされる SSH の構成ファイルは、/etc/ssh/sshd_config にあります。
> 
> 

1. 次の内容を含む PrivateConf.json という名前のファイルを作成します。
   
        {
        "reset_ssh":"True"
        }
2. &#60;vmname&#62; を使用中の仮想マシンの名前に置き換えて、次のコマンドを実行します。 
   
        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="a-namedeletecliadelete-a-user"></a><a name="deletecli"></a>ユーザーの削除
VM にログインせずに直接ユーザー アカウントを削除するには、このスクリプトを使用できます。

1. ユーザー名を置き換えて、#60; usernametoremove &#62; を削除して、次の内容を含む PrivateConf.json という名前のファイルを作成します。 
   
        {
        "remove_user":"<usernametoremove>"
        }
2. &#60;vmname&#62; を使用中の仮想マシンの名前に置き換えて、次のコマンドを実行します。 
   
        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="a-namestatuscliadisplay-the-status-of-the-vmaccess-extension"></a><a name="statuscli"></a>VMAccess 拡張機能の状態の表示
VMAccess 拡張機能の状態を表示するには、次のコマンドを実行します。

        azure vm extension get

## <a name="a-namecheckdiskacheck-consistency-of-added-disks"></a><a name='checkdisk'<</a>追加されたディスクの整合性のチェック
Linux 仮想マシンのすべてのディスクに対して fsck を実行するには、次の手順を実行する必要があります。

1. 次の内容を含む PublicConf.json という名前のファイルを作成します。 チェック ディスクは、仮想マシンに接続されているディスクをチェックするかどうかを表すブール値を受け取ります。 
   
        {   
        "check_disk": "true"
        }
2. &#60;vmname&#62; を使用中の仮想マシンの名前に置き換えて、次のコマンドを実行します。
   
        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json 

## <a name="a-namerepairdiskarepair-disks"></a><a name='repairdisk'></a>ディスクの修復
マウントされていないディスクまたはマウント構成エラーが発生したディスクを修復するには、VMAccess 拡張機能を使用して Linux 仮想マシンでマウント構成をリセットします。 ディスクの名前を &#60;yourdisk&#62; に置き換えます。

1. 次の内容を含む PublicConf.json という名前のファイルを作成します。 
   
        {
        "repair_disk":"true",
        "disk_name":"<yourdisk>"
        }
2. &#60;vmname&#62; を使用中の仮想マシンの名前に置き換えて、次のコマンドを実行します。
   
        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json

## <a name="next-steps"></a>次のステップ
* Azure PowerShell コマンドレットまたは Azure Resource Manager テンプレートを使用して、パスワードまたは SSH キーをリセットし、SSH 構成を修正してディスクの整合性のチェックを行う場合、GitHub の [VMAccess 拡張機能に関するドキュメント](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess)を参照してください。 
* クラシック デプロイメント モデルでデプロイされた Linux VM のパスワードまたは SSH キーをリセットする場合は、 [Azure ポータル](https://portal.azure.com) を使用することもできます。 リソース マネージャーのデプロイ モデルでデプロイされた Linux VM のパスワードまたは SSH キーをリセットする場合は、現在ポータルを使用することができません。
* Azure 仮想マシンに VM 拡張機能を使用する方法の詳細については、「[仮想マシンの拡張機能とその機能について](virtual-machines-linux-extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。




<!--HONumber=Nov16_HO3-->


