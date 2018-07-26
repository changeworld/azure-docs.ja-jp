---
title: Azure Site Recovery でプロセス サーバーを管理する | Microsoft Docs
description: この記事では、Azure Site Recovery で VMware VM および物理サーバーのレプリケーションのために設定されたプロセス サーバーを管理する方法について説明します。
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/21/2018
ms.author: ramamill
ms.openlocfilehash: f8368aab1bc979492143d77a191a31afef754c4c
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213012"
---
# <a name="manage-process-servers"></a>プロセス サーバーの管理

既定では、VMware VM または物理サーバーを Azure にレプリケートするときに使用されるプロセス サーバーはオンプレミスの構成サーバー マシンにインストールされます。 個別のプロセス サーバーを設定することが必要な例がいくつか存在します。

- 大規模なデプロイでは、容量を拡張するためにオンプレミスのプロセス サーバーの追加が必要になる場合があります。
- フェールバックには、Azure で設定された一時的なプロセス サーバーが必要です。 この VM は、フェールバックが完了したら削除できます。 

この記事では、これらの追加のプロセス サーバーのための標準的な管理タスクを要約しています。

## <a name="upgrade-a-process-server"></a>プロセス サーバーをアップグレードする

オンプレミスで、または (フェールバックの目的で) Azure で実行されているプロセス サーバーを次のようにアップグレードします。

[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]

> [!NOTE]
  通常、フェールバックの目的で Azure でプロセス サーバーを作成するために Azure ギャラリー イメージを使用する場合、そのサーバーは使用可能な最新バージョンを実行しています。 Site Recovery チームは修正プログラムや拡張機能を定期的にリリースしているため、プロセス サーバーを最新の状態に維持することをお勧めします。



## <a name="reregister-a-process-server"></a>プロセス サーバーを再登録する

オンプレミスで、または Azure で実行されているプロセス サーバーを構成サーバーに再登録する必要がある場合は、次を実行します。

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

設定を保存した後、次を実行します。

1. プロセス サーバーで、管理者のコマンド プロンプトを開きます。
2. フォルダー **%PROGRAMDATA%\ASR\Agent** を参照し、次のコマンドを実行します。

    ```
    cdpcli.exe --registermt
    net stop obengine
    net start obengine
    ```

## <a name="modify-proxy-settings-for-an-on-premises-process-server"></a>オンプレミスのプロセス サーバーのプロキシ設定を変更する

プロセス サーバーがプロキシを使用して Azure の Site Recovery に接続しているとき、既存のプロキシ設定を変更する必要がある場合は、この手順を使用します。

1. プロセス サーバー マシンにログオンします。 
2. 管理者の PowerShell コマンド ウィンドウを開き、次のコマンドを実行します。
  ```
  $pwd = ConvertTo-SecureString -String MyProxyUserPassword
  Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
  net stop obengine
  net start obengine
  ```
2. フォルダー **%PROGRAMDATA%\ASR\Agent** を参照し、次のコマンドを実行します。
  ```
  cmd
  cdpcli.exe --registermt

  net stop obengine

  net start obengine

  exit
  ```


## <a name="remove-a-process-server"></a>プロセス サーバーを削除する

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]


