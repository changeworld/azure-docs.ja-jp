---
title: 管理ポータルで Azure Stack のバックアップを有効にする | Microsoft Docs
description: エラーが発生した場合に Azure Stack を復元できるように、管理ポータルでインフラストラクチャ バックアップ サービスを有効にします。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 56C948E7-4523-43B9-A236-1EF906A0304F
ms.service: azure-stack
ms.workload: naS
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: jeffgilb
ms.openlocfilehash: 08bce6284b672ae092e2cee3c26140e8c6049a34
ms.sourcegitcommit: d76d9e9d7749849f098b17712f5e327a76f8b95c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/25/2018
ms.locfileid: "39242854"
---
# <a name="enable-backup-for-azure-stack-from-the-administration-portal"></a>管理ポータルで Azure Stack のバックアップを有効にする
Azure Stack でバックアップを生成できるように、管理ポータルでインフラストラクチャ バックアップ サービスを有効にします。 [壊滅的な障害](.\azure-stack-backup-recover-data.md)が発生した場合、これらのバックアップとクラウドの復旧を使って環境を復元できます。 クラウドを復旧する目的は、復旧の完了後に、作業者とユーザーがポータルに再度ログインできるということを確認することです。 ユーザーのサブスクリプションが復元され、これにはロールベースのアクセス許可とロール、当初のプラン、オファー、および以前に定義されていたコンピューティング、ストレージ、およびネットワークのクォータが含まれます。

ただし、Infrastructure Backup サービスでは IaaS VM、ネットワーク構成、およびストレージ アカウント、BLOB、テーブルなどのストレージ リソースはバックアップされないので、クラウド復旧完了後にログインしたユーザーには、以前に存在していたリソースは表示されません。 サービスとしてのプラットフォーム (PaaS) のリソースとデータも、サービスではバックアップされません。 

管理者とユーザーは、IaaS および PaaS のリソースのバックアップと復元を、インフラストラクチャ バックアップ プロセスとは別に行う責任があります。 IaaS と PaaS のリソースのバックアップについては、次のリンクをご覧ください。

- [Virtual Machines](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-manage-vm-protect)
- [App Service](https://docs.microsoft.com/azure/app-service/web-sites-backup)
- [SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview)


> [!Note]  
> コンソールからバックアップを有効にする前に、バックアップ サービスを構成する必要があります。 PowerShell を使用して、バックアップ サービスを構成できます。 詳細については、「[PowerShell で Azure Stack のバックアップを有効にする](azure-stack-backup-enable-backup-powershell.md)」をご覧ください。

## <a name="enable-backup"></a>バックアップの有効化

1. Azure Stack 管理者ポータル ([https://adminportal.local.azurestack.external](https://adminportal.local.azurestack.external)) を開きます。
2. **[その他のサービス]** > **[Infrastructure backup]\(インフラストラクチャ バックアップ\)** の順に選択します。 **[Infrastructure backup]\(インフラストラクチャ バックアップ\)** ブレードで **[構成]** を選択します。

    ![Azure Stack - バックアップ コントローラーの設定](media\azure-stack-backup\azure-stack-backup-settings.png)が必要です。

3. **バックアップ ストレージの場所**のパスを入力します。 別のデバイスでホストされるファイル共有へのパスの場合、汎用名前付け規則 (UNC) の文字列を使用します。 UNC 文字列は、共有ファイルやデバイスといった、リソースの場所を指定します。 サービスの場合は、IP アドレスを使用できます。 障害発生後にバックアップ データを確実に利用できるようにするためには、保存デバイスは別の場所に配置する必要があります。
    > [!Note]  
    > 使用する環境で Azure Stack インフラストラクチャ ネットワークからエンタープライズ環境への名前解決がサポートされている場合は、IP ではなく FQDN を使用できます。
4. ファイルを読み書きするための十分なアクセス権を持つドメインとユーザー名を使用して**ユーザー名**を入力します。 たとえば、「`Contoso\backupshareuser`」のように入力します。
5. ユーザーの**パスワード**を入力します。
5. **[パスワードの確認]** にもう一度パスワードを入力します。
6. **[暗号化キー]** ボックスに事前共有キーを入力します。 バックアップ ファイルはこのキーを使用して暗号化されます。 このキーは安全な場所に保存してください。 初めて事前共有キーを設定する場合、または今後キーを交換する場合には、このインターフェイスでキーを表示することはできません。 事前共有キーの生成手順の詳細については、「[PowerShell で Azure Stack のバックアップを有効にする](azure-stack-backup-enable-backup-powershell.md)」でスクリプトをご覧ください。
7. **[OK]** を選択して、バックアップ コントローラーの設定を保存します。

バックアップを実行するには、Azure Stack ツールをダウンロードし、Azure Stack 管理ノードで PowerShell コマンドレット **Start-AzSBackup** を実行する必要があります。 詳細については、「[Back up Azure Stack (Azure Stack のバックアップ)](azure-stack-backup-back-up-azure-stack.md )」をご覧ください。

## <a name="next-steps"></a>次の手順

- バックアップの実行について学習します。 「[Azure Stack のバックアップ](azure-stack-backup-back-up-azure-stack.md )」をご覧ください。
- バックアップが実行されたことを確認する方法を学習します。 「[管理ポータルでバックアップの完了を確認する](azure-stack-backup-back-up-azure-stack.md)」をご覧ください。