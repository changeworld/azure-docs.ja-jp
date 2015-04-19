<properties 
	pageTitle="Azure モバイル エンゲージメント Windows Phone SDK 統合" 
	description="Azure モバイル エンゲージメント向け Windows Phone SDK の最新の更新プログラムと手順" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="lalathie" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />

#Azure モバイル エンゲージメント v2.0.0 向け Windows Phone SDK
ここから Azure モバイル エンゲージメントを Windows Phone アプリに統合する方法の詳細についての説明を開始します。初めての方へ: こちらは [15 分間のチュートリアル](mobile-engagement-windows-phone-get-started.md)です。

クリックして、[SDK コンテンツ](mobile-engagement-windows-phone-sdk-content.md)を表示する

##統合手順

1. ここから開始します。[Windows Phone アプリにモバイル エンゲージメントを統合する方法](mobile-engagement-windows-phone-integrate-engagement.md)

2. 通知:[Windows Phone アプリでリーチ (通知) を統合する方法](mobile-engagement-windows-phone-integrate-engagement-reach.md)

3. タグ付計画の実装:[Windows Phone アプリで高度なモバイル エンゲージメントのタグ付け API を使用する方法](mobile-engagement-windows-phone-use-engagement-api.md)

##リリース ノート

###2.0.0 (2015 年 2 月 17 日)
-   Azure モバイル エンゲージメントの最初のリリース
-   appId/sdkKey 構成は、接続文字列の構成で置き換えられます。
-   セキュリティの強化。

以前のバージョンについては、[完全なリリース ノート](mobile-engagement-windows-phone-release-notes.md) をご覧ください

##アップグレードの手順

既に古いバージョンの SDK をアプリケーションに統合している場合は、SDK をアップグレードする際に次の点を考慮する必要があります。

SDK のいくつかのバージョンがない場合は、次の手順に従う必要があります。完全な[アップグレード手順](mobile-engagement-windows-phone-upgrade-procedure/) をご覧ください。たとえば、0.10.1 から 0.11.0 へ移行する場合は、最初に「0.9.0 から 0.10.1」の手順に従い、次に「0.10.1 から 0.11.0」の手順に従う必要があります。

###1.1.1 から 2.0.0

Capptain SAS によって提供される Capptain サービスから Azure モバイル エンゲージメントを装備するアプリにSDK 統合を移行する方法を次に示します。 

>[Azure.IMPORTANT] Capptain と モバイル エンゲージメントは同じサービスではありません。また、以下に示す手順では、クライアント アプリを移行する方法についてのみ説明しています。アプリの SDK を移行しても、Capptain サーバーのデータはモバイル エンゲージメント サーバーには移行されません。

以前のバージョンから移行する場合は、 Capptain の Web サイトを参照して、最初に 1.1.1 に移行してから、次の手順を適用してください。

#### Nuget パッケージ

アーカイブ パッケージの lib フォルダー内で Capptain.WindowsPhone.nupkg を azuresdk-mobileengagement-windowsphone-VERSION.nupkg と置き換えます。

#### モバイル エンゲージメントの適用

SDK は  `Engagement` という用語を使用します。この変更と合致するようにプロジェクトを更新する必要があります。

現在の Capptain nuget パッケージをアンインストールする必要があります。Capptain リソース フォルダー内のすべての変更が削除されますので注意してください。これらのファイルを保持する場合は、コピーを作成します。

その後、プロジェクトに新しい Microsoft Azure エンゲージメント nuget パッケージをインストールします。[NuGet の Web サイト] か、またはここのインデックスで直接検索できます。この作業でエンゲージメントが使用するすべてのリソース ファイルが置き換わり、新しいエンゲージメント DLL がプロジェクト参照に追加されます。

Capptain DLL 参照を削除してプロジェクト参照をクリーンアップする必要があります。これを実行しないと、Capptain のバージョンの競合が発生し、エラーが発生します。

Capptain リソースをカスタマイズした場合は、古いファイルの内容をコピーし、新しいエンゲージメント ファイルに貼り付けます。xaml と cs ファイルの両方を更新する必要があることに注意してください。

これらの手順が完了した後は、古い Capptain 参照を新しいエンゲージメント参照に置き換える作業のみが必要となります。

すべての Capptain 名前空間の更新が必要です。

移行前:

			using Capptain.Agent;
			using Capptain.Reach;

After migration:

			using Microsoft.Azure.Engagement;

All Capptain classes that contain "Capptain" should contain "Engagement".

Before migration:

			public sealed partial class MainPage : CapptainPage
			{
			  protected override string GetCapptainPageName()
			  {
			    return "Capptain Demo";
			  }
			  ...
			}

移行後:

			public sealed partial class MainPage : EngagementPage
			{
			  protected override string GetEngagementPageName()
			  {
			    return "Engagement Demo";
			  }
			  ...
			}

xaml ファイルでは、Capptain 名前空間と属性も変更されます。

移行前:

			<capptain:CapptainPage
			...
			xmlns:capptain="clr-namespace:Capptain.Agent;assembly=Capptain.Agent.WP"
			...
			</capptain:CapptainPage>

移行後:

			<engagement:EngagementPage
			...
			xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
			...
			</engagement:EngagementPage>

Capptain 画像のようなその他のリソースも名前が変更されて、「Engagement」を使用しますので注意してください。

#### アプリケーション ID / SDK キー

エンゲージメントは接続文字列を使用します。モバイル エンゲージメントでアプリケーション ID と SDK キーを指定する必要はなく、接続文字列のみを指定します。これは、EngagementConfiguration ファイルで設定できます。

エンゲージメント構成は、プロジェクトの  `Resources\EngagementConfiguration.xml` ファイルで設定できます。

このファイルを編集して、次の内容を指定します。

-   タグ `<connectionString>` と `<\connectionString>` 間のアプリケーション接続文字列。

代わりに指定を実行時に行う場合は、エンゲージメント エージェントを初期化する前に、次のメソッドを呼び出すことができます。

			/* Engagement configuration. */
			EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
			engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
			
			/* Initialize Engagement angent with above configuration. */
			EngagementAgent.Instance.Init(engagementConfiguration);

アプリケーションの接続文字列が Azure ポータルに表示されます。

#### 項目の名前の変更

...capptain... という名前のすべての項目が ...engagement... という名前に変更されます。これは Capptain とエンゲージメントでも同様です。

一般的に使用されるCapptain の項目の例 :

> -   CapptainConfiguration は EngagementConfiguration という名前に変更されました
> -   CapptainAgent は EngagementAgent という名前に変更されました
> -   CapptainReach は EngagementReach という名前に変更されました
> -   CapptainHttpConfig は EngagementHttpConfig という名前に変更されました
> -   GetCapptainPageName は GetEngagementPageName という名前に変更されました

名前の変更はオーバーライドのメソッドにも影響することに注意してください。

### 古いバージョンからのアップグレード

[アップグレード手順](mobile-engagement-windows-phone-upgrades/) をご覧ください

<!--HONumber=47-->
