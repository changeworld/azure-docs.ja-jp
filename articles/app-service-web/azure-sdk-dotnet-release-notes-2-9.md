<properties 
   pageTitle="Azure SDK for .NET 2.9 リリース ノート" 
   description="Azure SDK for .NET 2.9 リリース ノート" 
   services="app-service\web" 
   documentationCenter=".net" 
   authors="Juliako" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="07/18/2016"
   ms.author="juliako"/>

# Azure SDK for .NET 2.9 リリース ノート

##概要

このドキュメントには、Azure SDK for .NET 2.9 リリースのリリース ノートが含まれます。

このリリースの詳細については、「[Azure SDK 2.9 アナウンスの投稿](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/)」をご覧ください。

## Azure SDK 2.9 for Visual Studio 2015 Update 2 と Visual Studio "15" プレビュー
 
バグの修正:

- REST API クライアント生成で、生成されたコード内でコード生成フォルダーや名前空間の名前が "Unknown Type” として表示される問題が修正されました。
- スケジュールされた Web ジョブで、認証情報がスケジューラのプロビジョニング プロセスに渡されないという問題が修正されました。

新機能:

- App Service のプロビジョニング ダイアログの [サービス] タブで、セカンダリ App Services がサポートされるようになりました。

##Azure Data Lake Tools for Visual Studio 2015 Update 2
 
アップデートの内容:

- **Azure Data Lake Tools** for Visual Studio が Azure SDK for .NET リリースに統合されました。Azure SDK をインストールすると、Azure Data Lake Tools が自動的にインストールされます。

	このツールは頻繁に更新されます。最新版は[こちら](http://aka.ms/datalaketool)から取得してください。

- **サーバー エクスプローラー**で U-SQL メタデータ エンティティをすべて表示したり、一部を作成したりできるようになりました。詳細については、[このブログ](https://azure.microsoft.com/documentation/services/data-lake-analytics/)をご覧ください。


##HDInsight ツール 

**HDInsight Tools** for Visual Studio で HDInsight Version 3.3 がサポートされるようになりました。これには、Tez グラフの表示やその他の言語の修正が含まれています。


##Azure リソース マネージャー 

このリリースでは、ARM テンプレートの [KeyVault](../resource-manager-keyvault-parameter.md) サポートが追加されます。

##関連項目

[Azure SDK 2.9 の発表に関するブログ記事](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/)

<!---HONumber=AcomDC_0720_2016-->