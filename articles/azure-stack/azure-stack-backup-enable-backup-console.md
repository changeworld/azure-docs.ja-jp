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
ms.date: 11/05/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.openlocfilehash: f5610f03cb613c7da2f89da1a38b6bf058a4e0cc
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53714408"
---
# <a name="enable-backup-for-azure-stack-from-the-administration-portal"></a>管理ポータルで Azure Stack のバックアップを有効にする
Azure Stack でバックアップを生成できるように、管理ポータルでインフラストラクチャ バックアップ サービスを有効にします。 [壊滅的な障害](./azure-stack-backup-recover-data.md)が発生した場合、これらのバックアップとクラウドの復旧を使って環境を復元できます。 クラウドを復旧する目的は、復旧の完了後に、作業者とユーザーがポータルに再度ログインできるということを確認することです。 ユーザーのサブスクリプションが復元され、これにはロールベースのアクセス許可とロール、当初のプラン、オファー、および以前に定義されていたコンピューティング、ストレージ、およびネットワークのクォータが含まれます。

ただし、Infrastructure Backup サービスでは IaaS VM、ネットワーク構成、およびストレージ アカウント、BLOB、テーブルなどのストレージ リソースはバックアップされないので、クラウド復旧完了後にログインしたユーザーには、以前に存在していたリソースは表示されません。 サービスとしてのプラットフォーム (PaaS) のリソースとデータも、サービスではバックアップされません。 

管理者とユーザーは、IaaS および PaaS のリソースのバックアップと復元を、インフラストラクチャ バックアップ プロセスとは別に行う責任があります。 IaaS と PaaS のリソースのバックアップについては、次のリンクをご覧ください。

- [Virtual Machines](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-manage-vm-protect)
- [App Service](https://docs.microsoft.com/azure/app-service/manage-backup)
- [SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview)


## <a name="enable-or-reconfigure-backup"></a>バックアップの有効化または再構成

1. [Azure Stack 管理者ポータル](azure-stack-manage-portals.md)を開きます。
2. **[すべてのサービス]** を選択し、**[管理]** カテゴリで **[インフラストラクチャ バックアップ]** を選びます。 **[Infrastructure backup]\(インフラストラクチャ バックアップ\)** ブレードで **[構成]** を選択します。
3. **バックアップ ストレージの場所**のパスを入力します。 別のデバイスでホストされるファイル共有へのパスの場合、汎用名前付け規則 (UNC) の文字列を使用します。 UNC 文字列は、共有ファイルやデバイスといった、リソースの場所を指定します。 サービスの場合は、IP アドレスを使用できます。 障害発生後にバックアップ データを確実に利用できるようにするためには、保存デバイスは別の場所に配置する必要があります。

    > [!Note]  
    > 使用する環境で Azure Stack インフラストラクチャ ネットワークからエンタープライズ環境への名前解決がサポートされている場合は、IP ではなく FQDN を使用できます。
    
4. ファイルを読み書きするための十分なアクセス権を持つドメインとユーザー名を使用して**ユーザー名**を入力します。 たとえば、「 `Contoso\backupshareuser` 」のように入力します。
5. ユーザーの**パスワード**を入力します。
6. **[パスワードの確認]** にもう一度パスワードを入力します。
7. **頻度 (時間単位)** はバックアップの作成頻度を決定します。 既定値は 12 です。 Scheduler では、最大値 12 から最小値 4 までをサポートします。 
8. **保有期間 (日単位)** は、バックアップが外部の保存場所に保持される日数を決定します。 既定値は 7 です。 Scheduler では、最大値 14 から最小値 2 までをサポートします。 保有期間より前のバックアップは、外部の保存場所から自動的に削除されます。

    > [!Note]  
    > 保有期間より前のバックアップをアーカイブする場合は、スケジューラがバックアップを削除する前に必ずファイルをバックアップしてください。 バックアップの保有期間を短縮すると (たとえば、7 日を 5 日にする)、スケジューラは新しい保有期間より前のすべてのバックアップを削除します。 この値を更新する前に、バックアップが削除されても問題がないことをご確認ください。 

9. **[暗号化キー]** ボックスに事前共有キーを入力します。 バックアップ ファイルはこのキーを使用して暗号化されます。 このキーは安全な場所に保存してください。 初めて事前共有キーを設定する場合、または今後キーを交換する場合には、このインターフェイスでキーを表示することはできません。 キーを作成するには、次の Azure Stack PowerShell コマンドを実行します。
    ```powershell
    New-AzsEncryptionKeyBase64
    ```
10. **[OK]** を選択して、バックアップ コントローラーの設定を保存します。

    ![Azure Stack - バックアップ コントローラーの設定](media/azure-stack-backup/backup-controller-settings.png)

## <a name="start-backup"></a>バックアップの開始
バックアップを開始するには、**[今すぐバックアップ]** をクリックして、オンデマンド バックアップを開始します。 オンデマンド バックアップを実行しても、次回のスケジュール済みバックアップの時間は変更されません。 タスクの完了後、**[基本]** で設定を確認できます。

![Azure Stack - オンデマンド バックアップ](media/azure-stack-backup/scheduled-backup.png)

また、Azure Stack 管理コンピューターで PowerShell コマンドレット **Start-AzsBackup** を実行することもできます。 詳細については、「[Back up Azure Stack (Azure Stack のバックアップ)](azure-stack-backup-back-up-azure-stack.md)」をご覧ください。

## <a name="enable-or-disable-automatic-backups"></a>自動バックアップの有効化または無効化
バックアップを有効にすると、バックアップが自動的にスケジュールされます。 **[基本]** で次回のスケジュールのバックアップ時間を確認できます。 

![Azure Stack - オンデマンド バックアップ](media/azure-stack-backup/on-demand-backup.png)

スケジュールされた将来のバックアップを無効にする必要がある場合は、**[自動バックアップを無効にする]** をクリックします。 自動バックアップを無効にしてもバックアップ設定の構成は保たれ、バックアップ スケジュールは保持されます。 このアクションは、単に将来のバックアップをスキップするようスケジューラに指示するに過ぎません。 

![Azure Stack - スケジュールされたバックアップの無効化](media/azure-stack-backup/disable-auto-backup.png)

**[基本]** で、スケジュールされた将来のバックアップが無効にされたことを確認します。

![Azure Stack - バックアップが無効にされたことの確認](media/azure-stack-backup/confirm-disable.png)

**[自動バックアップを有効にする]** をクリックして、スケジュールされた時刻に将来のバックアップを開始するようにスケジューラに通知します。 

![Azure Stack - スケジュールされたバックアップの有効化](media/azure-stack-backup/enable-auto-backup.png)


> [!Note]  
> 1807 に更新する前にインフラストラクチャのバックアップを構成した場合、自動バックアップは無効になります。 このように、Azure Stack によって開始されたバックアップは、外部タスク スケジュール エンジンによって開始されたバックアップとは競合しません。 任意の外部タスク スケジューラを無効にしたら、**[自動バックアップを有効にする]** をクリックします。


## <a name="next-steps"></a>次の手順

- バックアップの実行について学習します。 「[Azure Stack のバックアップ](azure-stack-backup-back-up-azure-stack.md )」をご覧ください。
- バックアップが実行されたことを確認する方法を学習します。 「[管理ポータルでバックアップの完了を確認する](azure-stack-backup-back-up-azure-stack.md)」をご覧ください。
