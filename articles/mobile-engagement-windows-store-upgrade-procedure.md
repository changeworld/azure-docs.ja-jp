<properties 
	pageTitle="Azure モバイル エンゲージメントの Windows ストア SDK のアップグレード手順" 
	description="Azure モバイル エンゲージメントの Windows ストア SDK の最新更新プログラムと手順について" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />

# アップグレードの手順

既にアプリケーションに以前のバージョンのモバイル エンゲージメントを統合してある場合は、SDK をアップグレードするときに、次の点を考慮する必要があります。

SDK の一部のバージョンが不足している場合、いくつかの手順に従う必要があることがあります。たとえば、0.10.1 から 0.11.0 に移行する場合、まず「0.9.0から 0.10.1」への手順を実行してから「0.10.1 から 0.11.0」への手順を実行する必要があります。

## 1.1.1 から 2.0.0 に移行

Azure モバイル エンゲージメントを使用するアプリに Capptain SAS によって提供される Capptain サービスから SDK の統合を移行する方法を次に示します。 

>[Azure.IMPORTANT] Capptain とモバイル エンゲージメントは、同じサービスではありません。次の手順では、クライアント アプリケーションを移行する方法についてのみ詳しく説明します。アプリで SDK を移行しても、データは Capptain サーバーからモバイル エンゲージメントのサーバーに移行されません。

以前のバージョンから移行する場合は、Capptain web サイトをご覧のうえ、まず 1.1.1 に移行し、次の手順を適用してください。

### NuGet パッケージ

アーカイブ パッケージの lib フォルダーの「Capptain.WindowsPhone.nupkg」を「azuresdk-mobileengagement-windows-VERSION.nupkg」で置き換えます。

### モバイル エンゲージメントの適用

SDK では、 `Engagement` という項目を使用します。この変更を一致させるためにプロジェクトをアップグレードする必要があります。

現在の Capptain NuGet パッケージをアンインストールする必要があります。[Capptain Resources] フォルダー内のすべての変更が削除されることを検討します。これらのファイルを保持する場合は、コピーを作成します。

そのあと、新しい Microsoft Azure エンゲージメント NuGet パッケージをプロジェクトにインストールします。[NuGet の Web サイト]または、ここのインデックスで直接検索できます。この操作は、エンゲージメントによって使用されるすべてのリソース ファイルを置換し、プロジェクトの参照に新しいエンゲージメントの DLL を追加します。

Capptain DLL の参照を削除して、プロジェクトの参照をクリーンアップする必要があります。これを実行しない場合は、Capptain のバージョンが競合してエラーが発生します。

Capptain リソースをカスタマイズした場合、古いファイルの内容をコピーし、新しいエンゲージメントのファイルに貼り付けます。xaml と cs ファイルの両方を更新する必要があることにご注意ください。

これらの手順が完了したら、新しいエンゲージメントの参照で古い Capptain の参照を置き換える必要があります。

すべての Capptain 名前空間の更新が必要です。

移行前:

			using Capptain.Agent;
			using Capptain.Reach;

移行後:

			using Microsoft.Azure.Engagement;

"Capptain" が含まれているすべての Capptain クラスには、"Engagement" が含まれている必要があります。

移行前:

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

xaml ファイルの Capptain 名前空間と属性も変更します。

移行前:

			<capptain:CapptainPage
			...
			xmlns:capptain="using:Capptain.Agent"
			...
			</capptain:CapptainPage>

移行後:

			<engagement:EngagementPage
			...
			xmlns:engagement="using:Microsoft.Azure.Engagement"
			...
			</engagement:EngagementPage>

> [AZURE.IMPORTANT] オーバーレイも変更します。その新しい名前空間は  `Microsoft.Azure.Engagement.Overlay` です。xaml と cs ファイルの両方で使用する必要があります。さらに、 `CapptainGrid` は  `EngagementGrid` と名前が付けられ、 `capptain_notification_content` と  `capptain_announcement_content` は  `engagement_notification_content` と  `engagement_announcement_content` と名前が付けられます。

オーバーレイについて:

			<capptain:CapptainPageOverlay
			  xmlns:capptain="using:Capptain.Overlay"
			  ...
			</capptain:CapptainPageOverlay>

次のようになります:

			<EngagementPageOverlay
			  engagement="using:Microsoft.Azure.Engagement.Overlay"
			  ...
			</engagement:EngagementPageOverlay>

Capptain 画像や HTML ファイルなどその他のリソースについては、"Engagement" を使用して名前が変更されていることにもご注意ください。

### プロジェクトの宣言

Package.appxmanifest で  `File Type Associations` が次から更新されています。

> -   capptain\_reach\_content to engagement\_reach\_content
> -   capptain\_log\_file to engagement\_log\_file

### アプリケーション ID / SDK キー

エンゲージメントは、接続文字列を使用します。モバイル エンゲージメントでアプリケーション ID と SDK キーを指定する必要はありません。指定する必要があるのは接続文字列のみです。接続文字列は、EngagementConfiguration ファイルで設定できます。

エンゲージメントの構成は、プロジェクトの  `Resources\EngagementConfiguration.xml` ファイルで設定できます。

このファイルを編集して次を指定します。

-    `<connectionString>` と `<\connectionString>` タグ間のアプリケーション接続文字列。

代わりに実行時に指定する場合、エンゲージメント エージェントの初期化前に次のメソッドを呼び出すことができます。

			/* Engagement configuration. */
			EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
			engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
			
			/* Initialize Engagement agent with above configuration. */
			EngagementAgent.Instance.Init(args, engagementConfiguration);

アプリケーションの接続文字列が Azure ポータルに表示されます。

### 項目名の変更

...capptain... という名前の項目は ...engagement... と名前が変更されています。Capptain と Engagement についても同様です。

一般的に使用される Capptain 項目の例:

> -   CapptainConfiguration は EngagementConfiguration という名前になりました
> -   CapptainAgent は EngagementAgent という名前になりました
> -   CapptainReach は EngagementReach という名前になりました
> -   CapptainHttpConfig は EngagementHttpConfig という名前になりました
> -   GetCapptainPageName は GetEngagementPageName という名前になりました

名前の変更はオーバーライドされたメソッドにも影響することにご注意ください。

[NuGet の web サイト]:http://www.nuget.org/packages/Capptain.WindowsCS


<!--HONumber=47-->
