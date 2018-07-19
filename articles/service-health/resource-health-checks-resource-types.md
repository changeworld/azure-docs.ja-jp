---
title: Azure Resource Health でサポートされるリソースの種類 |Microsoft Docs
description: Azure Resource Health でサポートされるリソースの種類
services: Resource health
documentationcenter: ''
author: BernardoAMunoz
manager: ''
editor: ''
ms.assetid: 85cc88a4-80fd-4b9b-a30a-34ff3782855f
ms.service: service-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 10/09/2017
ms.author: BernardoAMunoz
ms.openlocfilehash: 633501b03a362fbd07e14e308e18488f03af5af5
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2018
ms.locfileid: "39069208"
---
# <a name="resource-types-and-health-checks-in-azure-resource-health"></a>Azure Resource Health で利用できるリソースの種類と正常性チェック
Resource Health で実行されるすべてのチェックをリソースの種類別にまとめた完全な一覧を次に示します。

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers
|実行されるチェック|
|---|
|<ul><li>サーバーが稼働しているか</li><li>サーバーのメモリが不足しているか</li><li>サーバーが起動しているか</li><li>サーバーが復旧しているか</li></ul>|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service
|実行されるチェック|
|---|
|<ul><li>API Management サービスが実行しているか</li></ul>|

## <a name="microsoftcacheredisredis"></a>Microsoft.CacheRedis/Redis
|実行されるチェック|
|---|
|<ul><li>すべてのキャッシュ ノードが稼働しているか</li><li>データセンターからキャッシュにアクセスできるか</li><li>キャッシュが接続の最大数に達しているか</li><li> キャッシュが使用可能なメモリを使い果たしているか </li><li>キャッシュで多数のページ フォールトが発生しているか</li><li>キャッシュに大きな負荷がかかっているか</li></ul>|

## <a name="microsoftcdnprofile"></a>Microsoft.CDN/profile
|実行されるチェック|
|---|
|<ul> <li>CDN 構成操作で補助ポータルにアクセスできるか</li><li>CDN エンドポイントに進行中の配信に関する問題があるか</li><li>ユーザーが CDN リソースの構成を変更できるか</li><li>構成の変更が期待されるレートで反映されるか</li><li>ユーザーが Azure Portal、PowerShell、または API を使用して CDN 構成を管理できるか</li> </ul>|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.classiccompute/virtualmachines
|実行されるチェック|
|---|
|<ul><li>ホスト サーバーが稼働しているか</li><li>ホスト OS の起動が完了しているか</li><li>仮想マシン コンテナーがプロビジョニングされ、オンになっているか</li><li>ホストとストレージ アカウント間のネットワーク接続が存在するか</li><li>ゲスト OS の起動が完了しているか</li><li>進行中の定期的なメンテナンスはあるか</li></ul>|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.cognitiveservices/accounts
|実行されるチェック|
|---|
|<ul><li>データセンターからアカウントにアクセスできるか</li><li>Cognitive Services リソース プロバイダーを利用できるか</li><li>Cognitive Services が適切な地域で利用できるか</li><li>リソースのメタデータを保持しているストレージ アカウントで読み取り操作を実行できるか</li><li>API 呼び出しのクォータに達しているか</li><li>API 呼び出しの読み取り制限に達しているか</li></ul>|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.compute/virtualmachines
|実行されるチェック|
|---|
|<ul><li>この仮想マシンをホストするサーバーは稼働しているか</li><li>ホスト OS の起動が完了しているか</li><li>仮想マシン コンテナーがプロビジョニングされ、オンになっているか</li><li>ホストとストレージ アカウント間のネットワーク接続が存在するか</li><li>ゲスト OS の起動が完了しているか</li><li>進行中の定期的なメンテナンスはあるか</li></ul>|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.datalakeanalytics/accounts
|実行されるチェック|
|---|
|<ul><li>ユーザーに Data Lake Analytics ジョブの送信またはリストに関する問題が発生しているか</li><li>Data Lake Analytics ジョブを完了できず、システム エラーが発生しているか</li></ul>|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.datalakestore/accounts
|実行されるチェック|
|---|
|<ul><li>ユーザーに Data Lake Store へのデータのアップロードに関する問題が発生しているか</li><li>ユーザーに Data Lake Store からのデータのダウンロードに関する問題が発生しているか</li></ul>|

## <a name="microsoftdevicesiothubs"></a>Microsoft.devices/iothubs

|実行されるチェック|
|---|
|<ul><li>IoT ハブが稼働しているか</li></ul>|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.documentdb/databaseAccounts
|実行されるチェック|
|---|
|<ul><li>Azure Cosmos DB サービスが利用できないために処理されていないデータベースまたはコレクションの要求があるか</li><li>Azure Cosmos DB サービスが利用できないために処理されていないドキュメントの要求があるか</li></ul>|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults
|実行されるチェック|
|---|
|<ul><li>Azure KeyVault プラットフォームの問題が原因でキー コンテナーへの要求が失敗するか</li><li>顧客によって行われた要求が多すぎるためにキー コンテナーへの要求がスロットルされているか</li></ul>|

## <a name="microsoftnetworkconnections"></a>Microsoft.network/connections
|実行されるチェック|
|---|
|<ul><li>VPN トンネルが接続されているか</li><li>接続に構成の競合があるか</li><li>事前共有キーは正しく構成されているか</li><li>VPN でオンプレミスのデバイスにアクセスできるか</li><li>IPSec/IKE セキュリティ ポリシーに不一致があるか</li><li>S2S VPN 接続が正しくプロビジョニングされているか、障害が発生した状態にあるか</li><li>VNET-to-VNET 接続が正しくプロビジョニングされているか、障害が発生した状態にあるか</li></ul>|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.network/virtualNetworkGateways
|実行されるチェック|
|---|
|<ul><li>インターネットから VPN Gateway にアクセスできるか</li><li>VPN Gateway がスタンバイ モードか</li><li>VPN サービスがゲートウェイで実行されているか</li></ul>|

## <a name="microsoftnotificationhubsnamespace"></a>Microsoft.NotificationHubs/namespace
|実行されるチェック|
|---|
|<ul><li> 名前空間で、登録、インストール、送信などのランタイム操作を実行できるか</li></ul>|

## <a name="microsoftpowerbiworkspacecollections"></a>Microsoft.PowerBI/workspaceCollections
|実行されるチェック|
|---|
|<ul><li>ホスト OS が稼働しているか</li><li>データ センターの外部から workspaceCollection にアクセスできるか</li><li>PowerBI リソース プロバイダーを利用できるか</li><li>PowerBI サービスが適切な地域で利用できるか</li></ul>|

## <a name="microsoftsearchsearchservices"></a>Microsoft.search/searchServices
|実行されるチェック|
|---|
|<ul><li>クラスターで診断操作を実行できるか</li></ul>|

## <a name="microsoftsqlserverdatabase"></a>Microsoft.SQL/Server/database
|実行されるチェック|
|---|
|<ul><li> データベースにログインしたことがあるか</li></ul>|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts
|実行されるチェック|
|---|
|<ul><li>Azure Storage プラットフォームの問題が原因で、Storage アカウントからのデータの読み取り要求が失敗しているか</li><li>Azure Storage プラットフォームの問題が原因で、Storage アカウントへのデータの書き込み要求が失敗しているか</li><li>Storage アカウントが存在する Storage クラスターが利用不可になっているか</li></ul>|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs
|実行されるチェック|
|---|
|<ul><li>ジョブが実行されるホストがすべて稼働しているか</li><li>ジョブを開始できなかったか</li><li>進行中のランタイムのアップグレードはあるか</li><li>ジョブが期待されている状態になっているか (たとえば、顧客によって実行中または停止されている)</li><li>ジョブでメモリ不足例外が発生しているか</li><li>スケジュールされたコンピューティングの進行中の更新はあるか</li><li>実行マネージャー (コントロール プラン) を利用できるか</li></ul>|

## <a name="microsoftwebserverfarms"></a>Microsoft.web/serverFarms
|実行されるチェック|
|---|
|<ul><li>ホスト サーバーが稼働しているか</li><li>インターネット インフォメーション サービスが実行されているか</li><li>ロード バランサーが実行されているか</li><li>データセンターから App Service プランにアクセスできるか</li><li>serverFarm のサイト コンテンツをホストしているストレージ アカウントを利用できるか</li></ul>|

## <a name="microsoftwebsites"></a>Microsoft.web/sites
|実行されるチェック|
|---|
|<ul><li>ホスト サーバーが稼働しているか</li><li>インターネット インフォメーション サーバーが実行されているか</li><li>ロード バランサーが実行されているか</li><li>データセンターから Web アプリにアクセスできるか</li><li>サイト コンテンツをホストしているストレージ アカウントを利用できるか</li></ul>|

# <a name="next-steps"></a>次の手順
-  [Azure Service Health ダッシュボードの概要](service-health-overview.md)に関するページと [Azure Resource Health の概要](resource-health-overview.md)に関するページを読んで、それらについての理解を深めます。 
-  [Azure Resource Health に関してよく寄せられる質問](resource-health-faq.md)
- アラートをセットアップして、正常性に関する問題について通知を受け取ります。 詳細については、[サービス正常性イベントのアラートの構成](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md)に関するページを参照してください。 
