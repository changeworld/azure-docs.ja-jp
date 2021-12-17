---
title: App Service データの監視のリファレンス
description: App Service を監視する際に必要となる重要なリファレンス資料
author: msangapu-msft
ms.topic: reference
ms.author: msangapu
ms.service: app-service
ms.custom: subject-monitoring
ms.date: 04/16/2021
ms.openlocfilehash: 914587d60b5932d2f9af6eef400a1a5067974652
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132290554"
---
# <a name="monitoring-app-service-data-reference"></a>App Service データの監視のリファレンス

このリファレンスは、App Service を監視するための Azure Monitor の使用に適用されます。 App Service の監視データの収集と分析の詳細については、「[App Service の監視](monitor-app-service.md)」を参照してください。

## <a name="metrics"></a>メトリック

このセクションでは、App Service 用に自動的に収集されるすべてのプラットフォーム メトリックを示します。  

|メトリックの種類 | リソース プロバイダー/種類の名前空間<br/> および個々のメトリックへのリンク |
|-------|-----|
| App Service プラン | [Microsoft.Web/serverfarms](../azure-monitor/essentials/metrics-supported.md#microsoftwebserverfarms)
| Web アプリ | [Microsoft.Web/sites](../azure-monitor/essentials/metrics-supported.md#microsoftwebsites) |
| ステージング スロット | [Microsoft.Web/sites/slots](../azure-monitor/essentials/metrics-supported.md#microsoftwebsitesslots) 
| App Service 環境 | [Microsoft.Web/hostingEnvironments](../azure-monitor/essentials/metrics-supported.md#microsoftwebhostingenvironments)
| App Service Environment フロントエンド | [Microsoft.Web/hostingEnvironments/multiRolePools](../azure-monitor/essentials/metrics-supported.md#microsoftwebhostingenvironmentsmultirolepools)


詳細については、[Azure Monitor でサポートされているすべてのプラットフォーム メトリック](../azure-monitor/essentials/metrics-supported.md)の一覧を参照してください。


## <a name="metric-dimensions"></a>メトリック ディメンション

メトリック ディメンションの詳細については、「[多次元メトリック](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics)」を参照してください。

App Service には、ディメンションを含むメトリックがありません。

## <a name="resource-logs"></a>リソース ログ

このセクションでは、App Service 用に収集できるリソース ログの種類を示します。 

| ログのタイプ | Windows | Windows コンテナー | Linux | Linux コンテナー | 説明 |
|-|-|-|-|-|-|
| AppServiceConsoleLogs | Java SE および Tomcat | はい | Yes | はい | 標準出力と標準エラー |
| AppServiceHTTPLogs | はい | Yes | Yes | はい | Web サーバー ログ |
| AppServiceEnvironmentPlatformLogs | はい | 該当なし | はい | はい | App Service Environment: スケーリング、構成変更、および状態ログ|
| AppServiceAuditLogs | はい | Yes | Yes | はい | FTP および Kudu 経由のログイン アクティビティ |
| AppServiceFileAuditLogs | はい | はい | TBA | TBA | サイト コンテンツに行われたファイルの変更。**Premium レベル以上でのみ使用可能** |
| AppServiceAppLogs | ASP.NET | ASP.NET | Java SE & Tomcat Images <sup>1</sup> | Java SE & Tomcat Blessed Images <sup>1</sup> | アプリケーション ログ |
| AppServiceIPSecAuditLogs  | はい | Yes | Yes | はい | IP ルールからの要求 |
| AppServicePlatformLogs  | TBA | はい | Yes | はい | コンテナーの操作ログ |
| AppServiceAntivirusScanAuditLogs | はい | Yes | Yes | Yes | Microsoft Defender for Cloud を使用する [ウイルス対策のスキャン ログ](https://azure.github.io/AppService/2020/12/09/AzMon-AppServiceAntivirusScanAuditLogs.html)。**Premium レベルでのみ使用可能** | 

<sup>1</sup> Java SE アプリの場合は、アプリ設定に "$WEBSITE_AZMON_PREVIEW_ENABLED" を追加し、それを 1 または true に設定します。

[Azure Monitorでサポートされているすべてのリソース ログのカテゴリの種類](../azure-monitor/essentials/resource-logs-schema.md)の一覧も参照してください。

## <a name="azure-monitor-logs-tables"></a>Azure Monitor ログ テーブル

Azure App Service の場合、Azure Monitor ログの Kusto テーブルが使用されます。 これらのテーブルに対して Log Analytics を使用してクエリを実行できます。 Kusto によって使用される App Service テーブルの一覧については、「[Azure Monitor ログ テーブルのリファレンス - App Service テーブル](/azure/azure-monitor/reference/tables/tables-resourcetype#app-services)」を参照してください。 

## <a name="activity-log"></a>アクティビティ ログ

次の表は、アクティビティ ログで作成される可能性がある App Service に関連する一般的な操作を示しています。 この一覧は完全ではありません。

| 操作 | 説明 |
|:---|:---|
|Web アプリの作成または更新| アプリが作成または更新された|
|Web アプリの削除| アプリが削除された |
|Web アプリのバックアップの作成| アプリのバックアップ|
|Web アプリの発行プロファイルの取得| 発行プロファイルのダウンロード |
|Web アプリの発行| アプリがデプロイされた |
|Web アプリの再起動| アプリが再起動した|
|Web アプリの開始| アプリが開始された |
|Web アプリの停止| アプリが停止した|
|Web アプリ スロットのスワップ| スロットがスワップされた|
|Web アプリ スロットの違いを取得| スロットの違い|
|Web アプリの構成の適用| 適用された構成変更|
|Web アプリの構成のリセット| 構成変更のリセット|
|プライベート エンドポイント接続を承認します| 承認されたプライベート エンドポイント接続|
|Web アプリのネットワーク トレース| 開始されたネットワーク トレース|
|Web アプリの Newpassword| 新しいパスワードが作成された |
|Web アプリの zip 圧縮されたコンテナーのログの取得| コンテナー ログを取得する |
|バックアップ BLOB から Web アプリを復元する| バックアップから復元されたアプリ|

アクティビティ ログ エントリのスキーマの詳細については、[アクティビティ ログのスキーマ](../azure-monitor/essentials/activity-log-schema.md)に関する記事を参照してください。 

## <a name="see-also"></a>参照

- Azure App Service の監視の詳細については、「[Azure App Service の監視](monitor-app-service.md)」を参照してください。
- Azure リソースの監視の詳細については、「[Azure Monitor を使用した Azure リソースの監視](../azure-monitor/essentials/monitor-azure-resource.md)」を参照してください。
