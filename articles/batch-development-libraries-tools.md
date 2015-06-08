<properties 
	pageTitle="Azure Batch 開発ライブラリおよびツール" 
	description="ライブラリと Azure のバッチとバッチのアプリのアプリケーションを開発するのに必要なツールを取得します。" 
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
<p> これらのライブラリと Azure のバッチとバッチのアプリのアプリケーションの開発ツールを取得します。

## バッチ

+ [.NET 用クライアント ライブラリをバッチ](http://www.nuget.org/packages/Azure.Batch/) (NuGet) – サービスとのバッチ ジョブを実行するクライアント アプリケーションの開発
+ [バッチ管理ライブラリ](http://www.nuget.org/packages/Microsoft.Azure.Management.Batch/) (NuGet)-バッチの管理サービス アカウント
+ [バッチ エクスプ ローラー](https://code.msdn.microsoft.com/windowsazure/Azure-Batch-Explorer-c1d37768) (MSDN) – GUI アプリケーションとサンプルを参照すると、アクセス、および、ジョブとタスク、タスク仮想マシン (Tvm) とプール、および TVM 上のファイルを含むバッチ、アカウント内での主要な要素を更新します。

> [AZURE.TIP]バッチのエクスプ ローラーはバッチに慣れていないかを監視したり、バッチのアクティビティをトラブルシューティングしたりする場合の便利なツールです。コード サンプルであるため、ソース コードに示す方法は、機能、バッチの .NET API を使用して実装します。参照してください、 [ブログの投稿](http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx)です。

<p>
## Batch Apps

+ [バッチ アプリのクラウド SDK](http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps.Cloud/) (NuGet)-バッチのサービスにジョブの負荷を軽減するアプリケーションを有効にします。
+ [バッチ アプリの Visual Studio 拡張機能](https://visualstudiogallery.msdn.microsoft.com/8b294850-a0a5-43b0-acde-57a07f17826a) (Visual Studio ギャラリー)-バッチのアプリのクラウドの SDK を使用して Visual Studio でのクラウドを有効にするアプリケーション
+ [バッチ アプリ クライアント SDK](http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps/) (NuGet) – バッチ サービスへのジョブの負荷を軽減するには有効になっているアプリケーションとの対話
+ [バッチ アプリ Python クライアント](https://github.com/Azure/azure-batch-apps-python) (GitHub) - バッチのアプリケーション サービスで設定されているアプリケーションと対話するクライアントのモジュールを Python
+ [バッチ アプリ Blender サンプル](https://github.com/Azure/azure-batch-apps-blender) (GitHub) - Blender にアドオンを開くソース表示ソフトウェアをバッチのアプリの SDK と Python クライアントを使用して、クラウド ベースのレンダリング プラットフォームを設定するには


## その他のリソース

+ [コード サンプルは](https://code.msdn.microsoft.com/site/search?f[0].Type=Topic&f[0].Value=Azure%20Batch&f[0].Text=Azure%20Batch) (MSDN)
+ [バッチの azure フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=azurebatch)
+ [.NET の Azure のバッチのライブラリを開始します。](batch-dotnet-get-started.md)  

<!--Anchors-->
[Batch]: #batch
[Batch Apps]: #batch-apps
[Additional resources]: #additional-resources

<!---HONumber=GIT-SubDir-->