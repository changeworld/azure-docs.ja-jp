<properties
	pageTitle="Azure Batch 開発ライブラリおよびツール | Microsoft Azure"
	description="Azure Batch アプリケーションを開発するために必要なライブラリとツールを取得します。"
	services="batch"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.workload="big-compute"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="07/24/2015"
	ms.author="danlep"/>


# Azure Batch 開発ライブラリおよびツール
<p>Azure Batch アプリケーションを開発するために、これらのライブラリとツールを取得します。

## Batch  


+ [.NET 向け Batch クライアント ライブラリ](http://www.nuget.org/packages/Azure.Batch/) (NuGet) - Batch サービスを使用してジョブを実行するクライアント アプリケーションを開発します
+ [Batch 管理ライブラリ](http://www.nuget.org/packages/Microsoft.Azure.Management.Batch/) (NuGet) – Batch のアカウントの管理
+ [Batch Explorer](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer) (GitHub) - Batch アカウント内の主要な要素 (ジョブとタスク、計算ノードとプール、計算ノード上のファイルなど) を参照、アクセス、更新するための GUI アプリケーションとサンプル。[ブログの投稿](http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx)をご覧ください。


## Batch Apps

+ [Batch Apps Cloud SDK](http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps.Cloud/1.1.1-preview) (NuGet) - アプリケーションがジョブを Batch サービスにオフロードできるようにします
+ [Batch Apps Visual Studio Extension](https://visualstudiogallery.msdn.microsoft.com/8b294850-a0a5-43b0-acde-57a07f17826a) (Visual Studio ギャラリー) - Batch Apps Cloud SDK を使用した Visual Studio のクラウド対応アプリケーション
+ [Batch Apps Client SDK](http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps/2.3.0-preview) (NuGet) - ジョブを Batch サービスにオフロードできるアプリケーションを操作します
+ [Batch Apps Python クライアント](https://github.com/Azure/azure-batch-apps-python) (GitHub) - Batch Apps サービスで設定されたアプリケーションを操作するための Python クライアント モジュール

>[AZURE.IMPORTANT]Azure は、プレビューの Batch Apps API のみをリリースします。テストまたは概念実証プロジェクトの開発だけに使用する必要があります。Batch Apps の主要な機能は、将来リリースされるサービスの Batch API に統合されます。

## その他のリソース

+ [コード サンプル](https://github.com/Azure/azure-batch-samples) (GitHub)
+ [Azure Batch フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=azurebatch)

<!--Anchors-->
[Batch]: #batch
[Batch Apps]: #batch-apps
[Additional resources]: #additional-resources

<!---HONumber=July15_HO5-->