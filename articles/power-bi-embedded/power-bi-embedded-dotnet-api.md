<properties
   pageTitle="Power BI Embedded .NET API"
   description="Power BI Embedded .NET API"
   services="power-bi-embedded"
   documentationCenter=""
   authors="dvana"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="03/29/2016"
   ms.author="derrickv"/>

# Power BI Embedded .NET API

**Microsoft Power BI Embedded** プレビューでは、アプリケーションの開発に使用できるように、既存の Power BI API の機能の大半を Azure サービスの一部として公開することに重点を置いています。また、必要なリソースや Power BI コンテンツをプログラムによってプロビジョニング、開発、デプロイできます。

**Power BI API** を使用して、Power BI コンテンツ ワークスペースを作成し、管理できます。API では次のことが可能です。

  - キー ベースの認証を使用して、Power BI Desktop ファイル (PBIX) をワークスペースにインポートする。
  - データセットの接続文字列を変更する。
  - **レポート**を取得して独自のアプリケーションに統合する。
  - 適切なソースのデータソースと通信できるように、データセットの資格情報を設定する。
  - **Power BI API** の詳細については、「[Get started with Microsoft Power BI Embedded preview (Microsoft Power BI Embedded プレビューを使ってみる)](power-bi-embedded-get-started.md)」をご覧ください。

[MSDN の Power BI リファレンス](https://msdn.microsoft.com/library/mt669800.aspx)をご覧ください。

**Power BI Embedded のサンプル**で使用されているクラスとメソッドを以下に示します。

## Microsoft.PowerBI.Api.Beta 名前空間

### ReportsExtensions クラス
|名前|説明
|---|---
|[GetReports(IReports, String, String)](https://msdn.microsoft.com/library/microsoft.powerbi.api.beta.reportsextensions.getreports.aspx)|指定されたワークスペース内で使用可能なレポートのリストを取得します。
|[GetReportsAsync(IReports, String, String, CancellationToken)](https://msdn.microsoft.com/library/microsoft.powerbi.api.beta.reportsextensions.getreportsasync.aspx)|指定されたワークスペース内で使用可能なレポートのリストを取得します。

## Microsoft.PowerBI.Security 名前空間

### PowerBIToken メソッド
|名前| 説明
|---|---
|[CreateDevToken(String, Guid)](https://msdn.microsoft.com/library/mt670215.aspx)|Power BI プラットフォーム サービスへのアクセスに使用される、既定の有効期限が設定された開発者トークンを作成します。
|[CreateProvisionToken(String)](https://msdn.microsoft.com/library/mt670218.aspx)|ワークスペース コレクション内のワークスペースの管理に使用される、既定の有効期限が設定されたプロビジョニング トークンを作成します。

## 関連項目

- [What is Microsoft Power BI Embedded (Microsoft Power BI Embedded とは)](power-bi-embedded-what-is-power-bi-embedded.md)
- [Get started with Microsoft Power BI Embedded Preview (Microsoft Power BI Embedded プレビューを使ってみる)](power-bi-embedded-get-started.md)

<!---HONumber=AcomDC_0406_2016-->