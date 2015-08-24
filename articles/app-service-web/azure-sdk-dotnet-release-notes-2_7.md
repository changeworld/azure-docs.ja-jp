
<properties 
   pageTitle="Azure SDK for .NET 2.7 リリース ノート" 
   description="Azure SDK for .NET 2.7 リリース ノート" 
   services="app-service\web" 
   documentationCenter=".net" 
   authors="Juliako" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="08/09/2015"
   ms.author="juliako"/>


# Azure SDK for .NET 2.7 リリース ノート

このドキュメントには、Azure SDK for .NET 2.7 リリースのリリース ノートが含まれます。

Azure SDK 2.7 は、Visual Studio 2015 と Visual Studio 2013 でのみサポートされています。[Azure SDK 2.6](http://azure.microsoft.com/downloads/) は、Visual Studio 2012 でサポートされる最後の SDK です。

このリリースの詳細については、「[Azure SDK 2.7 アナウンスの投稿](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/)」をご覧ください。

##Visual Studio 2015 のサインインの機能強化

Visual Studio 2015 用の Azure SDK 2.7 は、Visual Studio 2015 の新しい ID 管理機能をサポートしています。これには、ロールベースのアクセス制御、クラウド ソリューション プロバイダー、DreamSpark や他のアカウント、サブスクリプションの種類を使用して Azure にアクセスするアカウント向けのサポートが含まれています。

このようなサインインの機能強化は、Visual Studio 2015 でのみ利用できます。Visual Studio 2013 向けのサポートは、今後の更新プログラムに含まれる予定です。


##Mobile SDK

**Mobile Apps** テンプレートは、最新の [NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/)とセットアップ プロセスを反映するように更新されました。

##Service Bus 

一般的なバグの修正と機能強化。更新プログラムと機能の詳細については、最新の [Service Bus NuGet](http://www.nuget.org/packages/WindowsAzure.ServiceBus/) のリリース ノートをご覧ください。

##HDInsight ツール 

このリリースでは、次の更新が行われました。これらの更新プログラムはプレビュー段階です。詳細については、[この投稿](http://go.microsoft.com/fwlink/?LinkId=619108)をご覧ください。

- Tez ジョブでの Hive 用の Hive グラフ
- 完全な Hive DML IntelliSense のサポート
- Pig テンプレートのサポート
- Azure サービス用の Storm テンプレート

###重大な変更

- このバージョンのツールを使用する場合は、以前の **Storm** プロジェクトをアップグレードする必要があります。詳細については、[この投稿](http://go.microsoft.com/fwlink/?LinkId=619108)をご覧ください。
- Visual Studio Web Express はサポートされなくなりました。詳細については、[この投稿](http://go.microsoft.com/fwlink/?LinkId=619108)をご覧ください。

##Azure App Service ツール

このリリースでは、Web ツールの拡張機能に対して次の更新が行われました。詳細については、[この投稿](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/)をご覧ください。

- DreamSpark アカウントのサポートの追加
- 新しい Azure リソース管理 API をサポートするための Azure ツールの全面的な変更
- [クラウド エクスプローラー](azure-sdk-dot-net-release-notes-2_7.md#cloud_explorer)に追加された Azure App Service のサポート

###既知の問題

Web アプリ デプロイ スロットのノードがサーバー エクスプローラーのスロット ノードに表示されません。また、Web アプリ デプロイ スロットの子ノードがクラウド エクスプローラーに読み込まれません。この問題は解決され、次の SDK リリースに向けて準備されています。


##<a id="cloud_explorer"></a>Visual Studio 2015 のクラウド エクスプローラー

Azure SDK 2.7 には、Azure リソースを表示し、そのプロパティを調べ、Visual Studio 内から開発者が重要な操作を実行できる、Visual Studio 2015 のクラウド エクスプローラーが含まれています。

クラウド エクスプローラーは、次をサポートしています。

- Azure リソースのリソース グループとリソースの種類の表示 
- 名前によるリソースの検索 (リソースの種類の表示で使用可能)
- ロール ベースのアクセス制御 (RBAC) が適用されたサブスクリプションとリソースのサポート 
- 選択したリソース固有の開発者向けの操作を表示する統合されたアクション パネル(例: リソース マネージャー スタックを使用して作成された仮想マシン用のリモート デバッガーのアタッチ、仮想マシン用の診断データの表示など)
- 開発やテスト時に一般的に必要な開発者向けのプロパティを表示する統合されたプロパティ パネル 
- リソースの列挙時に使用するアカウントの簡単な切り替え (ツールバーの [設定] コマンドを使用) 
- リソースの列挙時に使用するサブスクリプションのフィルター処理 (ツールバーの [設定] コマンドを使用) 
- リソースとリソース グループを管理するための Azure プレビュー ポータルへのディープ リンク 
 
 
##Azure Resource Manager ツール 

Azure リソース マネージャー ツールは、ロール ベースのアクセス制御 (RBAC) と新しいサブスクリプションの種類を使用するように更新されました。これらの変更には、従来のストレージに加えて、新しいストレージ アカウントを使用してデプロイ中にアーティファクトを格納する機能が含まれています。

SDK 2.7 を使用して以前のバージョンの SDK から Azure リソース グループのプロジェクトを使用している場合は、従来のストレージの代わりに新しいストレージ アカウントを使用してデプロイするために、新しいデプロイメント スクリプトが必要です。プロジェクトに変更を加えて新しいスクリプトを追加する前に、プロンプトが表示されます。古いスクリプトは名前が変更されます。新しいスクリプトに手動で変更を加える必要があります。
 
 
##ストレージ エクスプローラー ツール 

- 追加の BLOB を表示するためのサポート。詳細については、[このブログの投稿](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/13/introducing-azure-storage-append-blob.aspx)をご覧ください。 
- サーバー エクスプローラーから Premium Storage アカウントを表示するためのサポート。サーバー エクスプローラーには、Premium Storage アカウントをサポートする唯一の種類である、Premium Storage アカウントのページ BLOB のみが表示されます。

##Visual Studio の Azure Data Factory ツール 

Visual Studio の **Azure Data Factory ツール**について紹介します。次に、有効な機能を示します。詳細については、[この投稿](http://go.microsoft.com/fwlink/?LinkId=617530)をご覧ください。

- **テンプレートに基づく作成**: 使用事例ベースのテンプレート、データ移動のテンプレート、データ処理のテンプレートを選択して、エンド ツー エンドのデータ統合ソリューションをデプロイし、Data Factory を使用して簡単に作業を開始できます。 
- **Data Factory エンティティの作成とデプロイのためのソリューション エクスプローラーとの統合**: パイプラインと関連エンティティを Visual Studio プロジェクトとして作成し、デプロイします。 
- **作成時の視覚的相互作用のためのダイアグラム ビューとの統合**: ダイアグラム ビューの補助を使用してパイプラインとデータセットを視覚的に作成します。 
- **デプロイ済みのエンティティの参照と相互作用のためのサーバー エクスプローラーとの統合**: サーバー エクスプローラーを利用して、デプロイ済みの Data Factory と対応するエンティティを参照します。デプロイ済みの Data Factory や任意のエンティティ (パイプライン、リンクされたサービス、データセット) をプロジェクトにインポートします。 
- **スキーマ検証と豊富な Intellisense による JSON 編集**: 豊富な Intellisense とスキーマ検証を使用して Data Factory エンティティの JSON ドキュメントを効率的に構成し、編集します。 
- **マルチ環境の公開**: 開発環境、テスト環境、運用環境に対して、環境ごとに個別の構成ファイルを作成し、作成したパイプラインを公開します。
- **Pig、Hive、.Net ベースのデータ処理のサポート**: Data Factory プロジェクトにおける Pig スクリプトと Hive スクリプトをサポートします。.Net アクティビティを管理するための C# プロジェクトの参照をサポートします。

##関連トピック

[Azure SDK 2.7 アナウンスの投稿](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/)

[Azure SDK for .NET および API のサポートと提供終了に関する情報](https://msdn.microsoft.com/library/azure/dn479282.aspx/)

<!---HONumber=August15_HO7-->