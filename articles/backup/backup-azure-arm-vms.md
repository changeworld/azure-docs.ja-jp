---
title: "Recovery Services コンテナーへの Azure VM のバックアップ | Microsoft Docs"
description: "Azure 仮想マシン バックアップの手順で Azure 仮想マシンを検出および登録して Recovery Services コンテナーにバックアップします。"
services: backup
documentationcenter: 
author: markgalioto
manager: cfreeman
editor: 
keywords: "仮想マシンのバックアップ; バックアップ、仮想マシン; バックアップと障害復旧; ARM VM のバックアップ"
ms.assetid: 5c68481d-7be3-4e68-b87c-0961c267053e
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2016
ms.author: trinadhk; jimpark; markgal;
translationtype: Human Translation
ms.sourcegitcommit: ac8df40db8ddcc84a0a6221dddd0f17fecbe6586
ms.openlocfilehash: e80d4fdb6f189bf46096422602508b0827f41a67


---
# <a name="back-up-azure-vms-to-a-recovery-services-vault"></a>Recovery Services コンテナーへの Azure VM のバックアップ
> [!div class="op_single_selector"]
> * [Recovery Services コンテナーへの VM のバックアップ](backup-azure-arm-vms.md)
> * [バックアップ コンテナーへの VM のバックアップ](backup-azure-vms.md)
>
>

この記事では、Azure VM (Resource Manager デプロイとクラシック デプロイの両方) を Recovery Services コンテナーにバックアップする手順について説明します。 VM をバックアップするための作業のほとんどがその準備です。 VM をバックアップまたは保護する前に、 [前提条件](backup-azure-arm-vms-prepare.md) の作業を行って、VM を保護するために環境を準備する必要があります。 前提条件の作業が完了したら、バックアップ操作を開始して VM のスナップショットを作成できます。


[!INCLUDE [learn about backup deployment models](../../includes/backup-deployment-models.md)]

詳細については、「[Azure における VM バックアップ インフラストラクチャの計画を立てる](backup-azure-vms-introduction.md)」と [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/) に関するページを参照してください。

## <a name="triggering-the-backup-job"></a>バックアップ ジョブのトリガー
Recovery Services コンテナーに関連付けられているバックアップ ポリシーでは、バックアップ操作を実行する頻度とタイミングを定義します。 既定では、スケジュールされた最初のバックアップが初回バックアップとなります。 初回バックアップが実行されるまで、**[バックアップ ジョブ]** ブレードの [前回のバックアップの状態] には、**[警告 (初回のバックアップが保留中)]** と表示されます。

![Backup pending](./media/backup-azure-vms-first-look-arm/initial-backup-not-run.png)

初回バックアップがすぐに開始される予定でない場合は、 **[今すぐバックアップ]**を実行することをお勧めします。 以下の手順は、コンテナー ダッシュボードから開始します。 この手順では、すべての前提条件を完了した後で最初のバックアップ ジョブを実行します。 最初のバックアップ ジョブが既に実行されている場合、この手順は使用できません。 関連付けられているバックアップ ポリシーで、次のバックアップ ジョブが決定されます。  

最初のバックアップ ジョブを実行するには:

1. コンテナー ダッシュボードの **[バックアップ]** タイルで、**[Azure Virtual Machines]** をクリックします。 <br/>
    ![[設定] アイコン](./media/backup-azure-vms-first-look-arm/rs-vault-in-dashboard-backup-vms.png)

    **[バックアップ項目]** ブレードが開きます。
2. **[バックアップ項目]** ブレードで、バックアップするコンテナーを右クリックし、**[今すぐバックアップ]** をクリックします。

    ![Settings icon](./media/backup-azure-vms-first-look-arm/back-up-now.png)

    バックアップ ジョブがトリガーされます。 <br/>

    ![Backup job triggered](./media/backup-azure-vms-first-look-arm/backup-triggered.png)
3. 初回バックアップが完了したことを確認するには、コンテナー ダッシュボードの **[バックアップ ジョブ]** タイルで **[Azure Virtual Machines]** をクリックします。

    ![Backup Jobs tile](./media/backup-azure-vms-first-look-arm/open-backup-jobs.png)

    [バックアップ ジョブ] ブレードが開きます。
4. **[バックアップ ジョブ]** ブレードでは、すべてのジョブの状態を確認できます。

    ![Backup Jobs tile](./media/backup-azure-vms-first-look-arm/backup-jobs-in-jobs-view.png)

   > [!NOTE]
   > バックアップ操作中に、各仮想マシンのバックアップ拡張機能ですべての書き込みがフラッシュされ、一貫性のあるスナップショットが取得されます。
   >
   >

    バックアップ ジョブが完了すると、状態は *[完了]*になります。

## <a name="troubleshooting-errors"></a>エラーのトラブルシューティング
仮想マシンのバックアップ中に問題が発生した場合は、[仮想マシンのトラブルシューティングに関する記事](backup-azure-vms-troubleshoot.md)を参照してください。

## <a name="next-steps"></a>次のステップ
VM を保護したので、VM の管理タスクおよび VM の復元方法の詳細については、次の記事を参照してください。

* [仮想マシンの管理と監視](backup-azure-manage-vms.md)
* [仮想マシンの復元](backup-azure-arm-restore-vms.md)



<!--HONumber=Nov16_HO3-->


