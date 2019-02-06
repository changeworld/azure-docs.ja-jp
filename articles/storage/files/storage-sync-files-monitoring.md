---
title: Azure File Sync 監視する | Microsoft Docs
description: Azure File Sync の監視方法。
services: storage
author: jeffpatt24
ms.service: storage
ms.topic: article
ms.date: 01/28/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 032b39846d19e34f2eb87c1311feeb4bb890cb24
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2019
ms.locfileid: "55467460"
---
# <a name="monitor-azure-file-sync"></a>Azure File Sync を監視する

Azure File Sync を使用すると、オンプレミスのファイル サーバーの柔軟性、パフォーマンス、互換性を維持したまま Azure Files で組織のファイル共有を一元化できます。 Azure File Sync により、ご利用の Windows Server が Azure ファイル共有の高速キャッシュに変わります。 SMB、NFS、FTPS など、Windows Server 上で利用できるあらゆるプロトコルを使用して、データにローカルにアクセスできます。 キャッシュは、世界中にいくつでも必要に応じて設置することができます。

この記事では、Azure portal と Windows Server を使用して、Azure File Sync デプロイを監視する方法について説明します。

現在、以下の監視オプションを利用できます。

## <a name="azure-portal"></a>Azure ポータル

Azure portal では、登録済みサーバーの正常性、サーバー エンドポイントの正常性 (同期の正常性)、およびメトリックを表示できます。

### <a name="storage-sync-service"></a>ストレージ同期サービス

登録済みサーバーとサーバー エンドポイントの正常性を表示するには、Azure portal でストレージ同期サービスに移動します。 登録済みサーバーの正常性は、[登録済みサーバー] ブレードで表示できます。 サーバー エンドポイントの正常性は、[同期グループ] ブレードで表示できます。

登録済みサーバーの正常性
- 登録済みサーバーの状態が [オンライン] である場合、サーバーは正常にサービスと通信しています。
- 登録済みサーバーの状態が [オフラインのようです] である場合は、サーバーでストレージ同期モニター (AzureStorageSyncMonitor.exe) プロセスが実行されていることを確認します。 サーバーがファイアウォールまたはプロキシの内側にある場合は、[ドキュメント](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy)に従って、ファイアウォールとプロキシを構成します。

サーバー エンドポイントの正常性
- ポータルのサーバー エンドポイントの正常性は、サーバーのテレメトリ イベント ログに記録されている同期イベント (ID 9102 および 9302) に基づきます。 同期セッションが一時的なエラーにより失敗した場合 (エラーで取り消された場合など)、現在の同期セッションが進行中である限り、ポータルに同期の正常性が引き続き表示される可能性があります (ファイルが適用されているかどうかを判断するために、イベント ID 9302 が使用されます)。 詳細については、[同期の正常性](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync)と[同期の進行状況](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)に関するドキュメントを参照してください。
- 同期が進行していないために、同期エラーがポータルに表示される場合は、[トラブルシューティングに関するドキュメント](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors)を参考にしてください。

### <a name="azure-monitor"></a>Azure Monitor

[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) を使用して、同期、クラウドを使った階層化、サーバーの接続を監視します。 Azure File Sync のメトリックは既定で有効になっており、15 分ごとに Azure Monitor に送信されます。

Azure Monitor で Azure File Sync のメトリックを表示するには、ストレージ同期サービス リソースの種類を選択します。

Azure Monitor では、Azure File Sync の次のメトリックを使用できます。

| メトリックの名前 | 説明 |
|-|-|
| 同期したバイト数 | データ転送 (アップロードとダウンロード) のサイズ。<br><br>単位:Bytes<br>集計の種類:合計<br>適用可能なディメンション:サーバー エンドポイント名、同期の方向、同期グループ名 |
| クラウドを使った階層化の呼び戻し | 呼び戻されたデータのサイズ。<br><br>単位:Bytes<br>集計の種類:合計<br>適用可能なディメンション:サーバー名 |
| ファイルが同期していない | 同期に失敗しているファイルの数。<br><br>単位:Count<br>集計の種類:合計<br>適用可能なディメンション:サーバー エンドポイント名、同期の方向、同期グループ名 |
| 同期されたファイル数 | アップロードおよびダウンロードされたファイルの数。<br><br>単位:Count<br>集計の種類:合計<br>適用可能なディメンション:サーバー エンドポイント名、同期の方向、同期グループ名 |
| サーバーのハートビート | サーバーから受信したハートビートの数。<br><br>単位:Count<br>集計の種類:最大値<br>適用可能なディメンション:サーバー名 |
| 同期セッションの結果 | 同期セッションの結果 (1=成功した同期セッション; 0=失敗した同期セッション)<br><br>単位:Count<br>集計の種類:最大値<br>適用可能なディメンション:サーバー エンドポイント名、同期の方向、同期グループ名 |

## <a name="windows-server"></a>Windows Server

Windows Server では、クラウドを使った階層化、登録済みのサーバー、および同期の正常性を表示できます。

### <a name="event-logs"></a>イベント ログ

サーバーのテレメトリ イベント ログを使用して、登録済みサーバー、同期、およびクラウドを使った階層化の正常性を監視します。 テレメトリ イベント ログは、イベント ビューアーの Applications and Services\Microsoft\FileSync\Agent にあります。

同期の正常性
- 同期セッションが完了すると、イベント ID 9102 がログに記録されます。 同期セッションが正常に完了している (HResult = 0) かどうか、および項目単位の同期エラーがあるかどうかを確認する場合は、このイベントを使用する必要があります。 詳細については、[正常性の同期](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync)と[項目単位のエラー](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)に関するドキュメントを参照してください。

  > [!Note]  
  > 同期セッション全体が失敗するか、または PerItemErrorCount が 0 以外であるのに、セッションが進行して一部のファイルが正常に同期される場合もあります。 このことは、成功しているセッションの数を示す、Applied フィールド (AppliedFileCount、AppliedDirCount、AppliedTombstoneCount、および AppliedSizeBytes) で確認できます。 行内に失敗している同期セッションが複数あるのに Applied の数が増えている場合は、サポート チケットを開く前に、時間をとって同期の再試行を待つ必要があります。

- アクティブな同期セッションがある場合は、5 分から 10 分ごとにイベント ID 9302 がログに記録されます。 現在の同期セッションが進行中 (AppliedItemCount が 0 より大きい) かどうかを確認するには、このイベントを使用する必要があります。 同期が進行していない場合、同期セッションは最終的に失敗するはずです。イベント ID 9102 はエラーと共にログに記録されます。 詳細については、[同期の進行状況](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)に関するドキュメントを参照してください

登録済みサーバーの正常性
- サーバーでサービスのジョブについてクエリが実行されている場合、30 秒ごとにイベント ID 9301 がログに記録されます。 GetNextJob が 0 の状態で完了した場合、サーバーはサービスと通信できます。 GetNextJob がエラーで完了した場合は、[トラブルシューティングに関するドキュメント](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors)を参考にしてください。

クラウドを使った階層化の正常性
- サーバー上の階層化アクティビティを監視するには、(イベント ビューアーの Applications and Services\Microsoft\FileSync\Agent にある) テレメトリ イベント ログのイベント ID 9003、9016、および 9029 を使用します。

  - イベント ID 9003 は、サーバー エンドポイントのエラー分布を示します。 たとえば、Total Error Count、ErrorCode などです。エラー コードごとに 1 つのイベントがログ記録されます。
  - イベント ID 9016 は、ボリュームの非実体化の結果を示します。 たとえば、空き領域の割合、セッション内の非実体化されたファイルの数、非実体化が失敗したファイルの数などです。
  - イベント ID 9029 では、サーバー エンドポイントの非実体化セッション情報が提供されます。 たとえば、セッション内の試行されたファイルの数、セッション内の階層化されたファイルの数、既に階層化されているファイルの数などです。
  
- サーバー上の呼び戻しアクティビティを監視するには、(イベント ビューアーの Applications and Services\Microsoft\FileSync\Agent にある) テレメトリ イベント ログのイベント ID 9005、9006、9009、9059 を使用します。

  - イベント ID 9005 は、サーバー エンドポイントの呼び戻しの信頼性を示します。 たとえば、アクセスされた一意のファイルの合計数、アクセスが失敗した一意のファイルの合計数などです。
  - イベント ID 9006 は、サーバー エンドポイントの呼び戻しエラーの分布を示します。 たとえば、失敗した要求の合計数、ErrorCode などです。エラー コードごとに 1 つのイベントがログ記録されます。
  - イベント ID 9009 では、サーバー エンドポイントの呼び戻しセッション情報が提供されます。 たとえば、DurationSeconds、CountFilesRecallSucceeded、CountFilesRecallFailed などです。
  - イベント ID 9059 では、サーバー エンドポイントのアプリケーション呼び戻し分布が提供されます。 たとえば、ShareId、Application Name、TotalEgressNetworkBytes です。

### <a name="performance-counters"></a>パフォーマンス カウンター

サーバーで Azure File Sync のパフォーマンス カウンターを使用して、同期アクティビティを監視します。

サーバーで Azure File Sync のパフォーマンス カウンターを表示するには、パフォーマンス モニター (Perfmon.exe) を起動します。カウンターは、AFS Bytes Transferred および AFS Sync Operations オブジェクトの下にあります。

パフォーマンス モニターでは、Azure File Sync の以下のパフォーマンス カウンターを使用できます。

| パフォーマンス オブジェクト\カウンター名 | 説明 |
|-|-|
| AFS Bytes Transferred\Downloaded Bytes/sec | 1 秒あたりのダウンロードされたバイト数。 |
| AFS Bytes Transferred\Uploaded Bytes/sec | 1 秒あたりのアップロードされたバイト数。 |
| AFS Bytes Transferred\Total Bytes/sec | 1 秒あたりの合計バイト数 (アップロードとダウンロード)。 |
| AFS Sync Operations\Downloaded Sync Files/sec | 1 秒あたりのダウンロードされたファイル数。 |
| AFS Sync Operations\Uploaded Sync Files/sec | 1 秒あたりのアップロードされたファイル数。 |
| AFS Sync Operations\Total Sync File Operations/sec | 同期されたファイルの合計数 (アップロードとダウンロード)。 |

## <a name="next-steps"></a>次の手順
- [Azure File Sync のデプロイの計画](storage-sync-files-planning.md)
- [ファイアウォールとプロキシの設定の考慮事項](storage-sync-files-firewall-and-proxy.md)
- [Azure File Sync をデプロイする](storage-sync-files-deployment-guide.md)
- [Azure File Sync のトラブルシューティング](storage-sync-files-troubleshoot.md)
- [Azure Files についてよく寄せられる質問 (FAQ)](storage-files-faq.md)
