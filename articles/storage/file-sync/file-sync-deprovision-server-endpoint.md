---
title: Azure File Sync サーバー エンドポイントのプロビジョニング解除 | Microsoft Docs
description: Azure File Sync サーバー エンドポイントのプロビジョニングを解除する方法のユース ケース別ガイダンス
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 4/23/2021
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 49eeef80dd93c8dd87668485db6def84a4d74427
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/28/2021
ms.locfileid: "110676218"
---
# <a name="deprovision-your-azure-file-sync-server-endpoint"></a>Azure File Sync サーバー エンドポイントのプロビジョニング解除

データ整合性と可用性を維持するためには、サーバー エンドポイントのプロビジョニングを解除する前に、いくつかの手順を実行する必要があります。 この記事では、いくつかのプロビジョニング解除方法と適切なガイダンスについて、シナリオ別に順番に説明します。 最も近いと思われるユース ケースの手順に従ってください。

現在同期しているデータが永久に失われても問題ない場合は、スキップして、サーバー エンドポイントのプロビジョニング解除にそのまま進んでも構いません。

> [!Important]
> サーバー エンドポイントのプロビジョニング解除によって同期の問題を解決しようとしないでください。 トラブルシューティングのヘルプについては、「[Azure File Sync のトラブルシューティング](./file-sync-troubleshoot.md)」を参照してください。サーバー側またはクラウド側のいずれかがもう一方と完全に同期していない状態でサーバー エンドポイントを削除した場合、データが永久に失われる場合があります。 

## <a name="scenario-1-you-intend-to-delete-your-server-endpoint-and-stop-using-your-local-servervm"></a>シナリオ 1: サーバー エンドポイントを削除してローカル サーバー/VM の使用を中止することを意図している

ここでの目標は、クラウド エンドポイントでデータが最新であることの確認です。 代わりに、サーバー エンドポイントでファイルの完全なセットを最新にするには、「[シナリオ 2: サーバー エンドポイントを削除し、この特定の Azure ファイル共有の使用を中止することを意図している](#scenario-2-you-intend-to-delete-your-server-endpoint-and-stop-using-this-specific-azure-file-share)」を参照してください。

このカテゴリに分類されるユース ケースには、次のものがあります。
-   Azure ファイル共有への移行
-   サーバーレスへの移行
-   特定のサーバー エンドポイント パスの使用を中止するが、同期グループの残りの部分には手を付けない

このシナリオでは、サーバー エンドポイントを削除する前に、3 つの手順を実行する必要があります。具体的には、ユーザー アクセスを削除し、特別な VSS アップロード セッションを開始し、最終同期セッションの完了を待機します。

### <a name="remove-user-access-to-your-server-endpoint"></a>サーバー エンドポイントへのユーザー アクセスを削除する

サーバー エンドポイントのプロビジョニングを解除する前に、サーバーからのすべての変更をクラウドに同期できることを確認する必要があります。 クラウドでのキャッチアップを可能にするための最初の手順は、サーバー エンドポイント上のファイルやフォルダーに対する追加の変更の機会を排除することです。 

アクセスを削除すると、ダウンタイムが発生します。 ダウンタイムを減らすために、ユーザー アクセスをクラウド エンドポイントにリダイレクトすることも検討できます。 

自分自身のレコードに対するユーザー アクセスを削除した日時を記録してから、次のセクションに進みます。

### <a name="initiate-a-special-volume-snapshot-service-vss-upload-session"></a>特別なボリューム スナップショット サービス (VSS) アップロード セッションを開始する

Azure File Sync では、毎日、一時的な VSS スナップショットをサーバー上に作成して、開いているハンドルとの間でファイルの同期を行います。 最終同期セッションで最新のデータが確実にアップロードされるように、また、アイテムあたりのエラー数を減らすために、VSS アップロードのための特別なセッションを開始します。 これにより、スナップショットが作成されると開始される特別な同期アップロード セッションもトリガーされます。  

これを行うには、ローカル サーバーで **[タスク スケジューラ]** を開き、**Microsoft\StorageSync** にアクセスし、**VssSyncScheduledTask** タスクを右クリックして **[実行する]** を選択します。

> [!Important]
> この手順を完了した日時を書き留めます。 これは、次のセクションで必要になります。

![VSS アップロード セッションのスケジュール設定のスクリーンショット。](media/storage-sync-deprovision-server-endpoint/vss-task-scheduler.png)

### <a name="wait-for-a-final-sync-upload-session-to-complete"></a>最終同期アップロード セッションの完了を待機する

最新のデータがクラウドにあることを確実にするためには、最終同期アップロード セッションの完了を待機する必要があります。 

同期セッションの状態を確認するには、ローカル サーバーで **イベント ビューアー** を開きます。 利用統計情報イベント ログ **(アプリケーションとサービス\Microsoft\FileSync\Agent)** にアクセスします。 VSS アップロード セッションを手動で開始した後に、'sync direction' = upload、'HResult' = 0、'PerItemErrorCount' = 0 の 9102 イベントが発生していることを確認します。

![最終同期セッションが完了したかどうかの確認のスクリーンショット。](media/storage-sync-deprovision-server-endpoint/event-viewer.png)

'PerItemErrorCount' が 0 より大きい場合、ファイルの同期は失敗しています。**FileSyncErrorsReport.ps1** を使用して、同期が失敗しているファイルを確認してください。この PowerShell スクリプトは通常、Azure File Sync エージェントがインストールされているサーバーの次のパスにあります: **C:\Program Files\Azure\StorageSyncAgent\FileSyncErrorsReport.ps1**

これらのファイルが重要でない場合は、サーバー エンドポイントを削除できます。 これらのファイルが重要である場合は、エラーを修正し、'sync direction' = upload、'HResult' = 0、'PerItemErrorCount' = 0 の 9102 イベントがもう 1 回発生するまで待ってから、サーバー エンドポイントを削除します。

## <a name="scenario-2-you-intend-to-delete-your-server-endpoint-and-stop-using-this-specific-azure-file-share"></a>シナリオ 2: サーバー エンドポイントを削除し、この特定の Azure ファイル共有の使用を中止することを意図している

ここでの目標は、ローカル サーバー/VM でデータが最新であることの確認です。 代わりに、クラウド エンドポイントでファイルの完全なセットを最新にするには、「[シナリオ 1: サーバー エンドポイントを削除してローカル サーバー/VM の使用を中止することを意図している](#scenario-1-you-intend-to-delete-your-server-endpoint-and-stop-using-your-local-servervm)」を参照してください。

このシナリオでは、サーバー エンドポイントを削除する前に、4 つの手順を実行します。具体的には、クラウド階層化を無効にし、階層化されたファイルをリコールし、クラウドの変更の検出を開始し、最終同期セッションの完了を待機します。

### <a name="disable-cloud-tiering"></a>クラウド階層化を無効にする
プロビジョニングを解除してクラウド階層化を無効にするサーバー エンドポイントの **[サーバー エンドポイント プロパティ]** のクラウド階層化セクションにアクセスします。

### <a name="recall-all-tiered-files"></a>すべての階層化ファイルをリコールする
クラウド階層化が無効になっている場合でも、すべてのファイルがローカルに保存されていることを確認するために、すべての階層化ファイルをリコールする必要があります。

ファイルをリコールする前に、すべてのファイルを保存するための十分な空き容量がローカルにあることを確認してください。 クラウドの Azure ファイル共有のサイズから、サーバー上にキャッシュされているサイズを引いた程度の空き容量が必要です。

**Invoke-StorageSyncFileRecall** PowerShell コマンドレットを使用し、**SyncGroupName** パラメーターを指定してすべてのファイルをリコールします。 
```powershell
Invoke-StorageSyncFileRecall  -SyncGroupName “samplesyncgroupname”
```
このコマンドレットの実行が完了したら、次のセクションに進むことができます。

### <a name="initiate-cloud-change-detection"></a>クラウドの変更の検出を開始する
クラウドで変更の検出を開始すると、最新の変更が同期されていることの確認が行われます。

変更の検出は、Invoke-AzStorageSyncChangeDetection コマンドレットを使用して開始できます。 

```powershell
Invoke-AzStorageSyncChangeDetection -ResourceGroupName "myResourceGroup" -StorageSyncServiceName "myStorageSyncServiceName" -SyncGroupName "mySyncGroupName" -Path "Data","Reporting\Templates" 
```

この手順は完了に時間がかかる場合があります。 

> [!Important]
> 開始したこのクラウド変更検出スキャンが完了したら、完了した日時を書き留めます。 次のセクションで必要になります。

### <a name="wait-for-a-final-sync-session-to-complete"></a>最終同期セッションの完了を待機する
ローカル サーバー上でデータが最新であることを確認するには、最終同期アップロード セッションの完了を待機する必要があります。 

これを確認するには、ローカル サーバーで **イベント ビューアー** にアクセスします。 利用統計情報イベント ログ **(アプリケーションとサービス\Microsoft\FileSync\Agent)** にアクセスします。 クラウド変更検出の終了日時よりも後に、'sync direction' = download、'HResult' = 0、'PerItemErrorCount' = 0 の 9102 イベントが発生していることを確認します。

![最終同期セッションが完了したかどうかの確認のスクリーンショット。](media/storage-sync-deprovision-server-endpoint/event-viewer.png)

'PerItemErrorCount' が 0 より大きい場合、ファイルの同期は失敗しています。**FileSyncErrorsReport.ps1** を使用して、同期が失敗しているファイルを確認してください。この PowerShell スクリプトは通常、Azure File Sync エージェントがインストールされているサーバーの次のパスにあります: **C:\Program Files\Azure\StorageSyncAgent\FileSyncErrorsReport.ps1**

これらのファイルが重要でない場合は、サーバー エンドポイントを削除できます。 これらのファイルが重要である場合は、エラーを修正し、'sync direction' = download、'HResult' = 0、'PerItemErrorCount' = 0 の 9102 イベントがもう 1 回発生するまで待ってから、サーバー エンドポイントを削除します。

## <a name="next-steps"></a>次の手順
* [Azure File Sync トポロジの変更](./file-sync-modify-sync-topology.md)







