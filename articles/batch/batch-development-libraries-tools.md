<properties 
	pageTitle="Azure Batch 開発ライブラリおよびツール" 
	description="Azure Batch と Batch Apps アプリケーションを開発するために必要なライブラリとツールを取得します。" 
	services="batch" 
	documentationCenter="" 
	authors="dlepow" 
	manager="timlt"
	editor="yidingz"/>

<tags 
	ms.service="batch" 
	ms.workload="big-compute" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="danlep"/>


# Azure Batch 開発ライブラリおよびツール 
<p>Azure Batch と Batch Apps アプリケーションを開発するために、これらのライブラリとツールを取得します。

## バッチ

+ [.NET 向け Batch クライアント ライブラリ](http://www.nuget.org/packages/Azure.Batch/) (NuGet) - Batch サービスを使用してジョブを実行するクライアント アプリケーションを開発します
+ [Batch 管理ライブラリ](http://www.nuget.org/packages/Microsoft.Azure.Management.Batch/) (NuGet) - Batch サービスのアカウントを管理します
+ [Batch Explorer](https://code.msdn.microsoft.com/windowsazure/Azure-Batch-Explorer-c1d37768) (MSDN) - Batch アカウント内の主要な要素 (ジョブとタスク、タスク仮想マシン (TVM) とプール、TVM 上のファイルなど) を参照、アクセス、更新するための GUI アプリケーションとサンプル。

> [AZURE.TIP]Batch Explorer は、Batch に慣れていない場合や、Batch のアクティビティを監視またはトラブルシューティングする場合に便利なツールです。これはコード サンプルであるため、Batch .NET API を使用して機能を実装する方法をソース コードに示します。[ブログの投稿](http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx)をご覧ください。

<p>
## Batch Apps

+ [Batch Apps Cloud SDK](http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps.Cloud/) (NuGet) - アプリケーションがジョブを Batch サービスにオフロードできるようにします
+ [Batch Apps Visual Studio Extension](https://visualstudiogallery.msdn.microsoft.com/8b294850-a0a5-43b0-acde-57a07f17826a) (Visual Studio ギャラリー) - Batch Apps Cloud SDK を使用した Visual Studio のクラウド対応アプリケーション
+ [Batch Apps Client SDK](http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps/) (NuGet) - ジョブを Batch サービスにオフロードできるアプリケーションを操作します
+ [Batch Apps Python クライアント](https://github.com/Azure/azure-batch-apps-python) (GitHub) - Batch Apps サービスで設定されたアプリケーションを操作するための Python クライアント モジュール
+ [Batch Apps Blender サンプル](https://github.com/Azure/azure-batch-apps-blender) (GitHub) - Batch Apps SDK と Python クライアントを使用してクラウド ベースのレンダリング プラットフォームを設定する、オープン ソースの Blender レンダリング ソフトウェアのアドオン


## その他のリソース

+ [コード サンプル](https://code.msdn.microsoft.com/site/search?f[0].Type=Topic&f[0].Value=Azure%20Batch&f[0].Text=Azure%20Batch) (MSDN)
+ [Azure Batch フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=azurebatch)
+ [.NET 向け Azure Batch ライブラリの概要](batch-dotnet-get-started.md)  

<!--Anchors-->
[Batch]: #batch
[Batch Apps]: #batch-apps
[Additional resources]: #additional-resources

<!---HONumber=58_postMigration-->