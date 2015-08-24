<properties 
	pageTitle="Windows ユニバーサル アプリ SDK のアップグレード手順" 
	description="Azure Mobile Engagement 向け Windows ユニバーサル アプリ SDK のアップグレード手順" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="08/10/2015" 
	ms.author="piyushjo" />

#Windows ユニバーサル アプリ SDK のアップグレード手順

既にアプリケーションに以前のバージョンの Mobile Engagement を統合してある場合は、SDK をアップグレードするときに、次の点を考慮する必要があります。

SDK の一部のバージョンが不足している場合、いくつかの手順に従う必要があることがあります。たとえば、0.10.1 から 0.11.0 に移行する場合、まず「0.9.0から 0.10.1」への手順を実行してから「0.10.1 から 0.11.0」への手順を実行する必要があります。

##2\.0.0 から 3.0.0 に移行

### リソース
この手順では、カスタマイズされたリソースが問題になります。SDK 提供のリソース (html、画像、オーバーレイ) をカスタマイズしている場合、アップグレード前にそのリソースをバックアップし、アップグレードしたリソースにカスタマイズを再適用する必要があります。

##1\.1.1 から 2.0.0 に移行

Azure Mobile Engagement を使用するアプリに Capptain SAS によって提供される Capptain サービスから SDK の統合を移行する方法を次に示します。

> [Azure.IMPORTANT]Capptain と Mobile Engagement は、同じサービスではありません。次の手順では、クライアント アプリケーションを移行する方法についてのみ詳しく説明します。アプリで SDK を移行しても、データは Capptain サーバーから Mobile Engagement のサーバーに移行されません。

以前のバージョンから移行する場合は、Capptain web サイトをご覧のうえ、まず 1.1.1 に移行し、次の手順を適用してください。

### NuGet パッケージ

**Capptain.WindowsPhone** を **MicrosoftAzure.MobileEngagement** Nuget パッケージに置き換えます。

### Mobile Engagement の適用

SDK は `Engagement` という用語を使用します。この変更を一致させるためにプロジェクトをアップグレードする必要があります。

現在の Capptain NuGet パッケージをアンインストールする必要があります。[Capptain Resources] フォルダー内のすべての変更が削除されることを検討します。これらのファイルを保持する場合は、コピーを作成します。

そのあと、新しい Microsoft Azure Engagement NuGet パッケージをプロジェクトにインストールします。 [NuGet の Web サイト] またはここのインデックスで直接検索できます。この操作は、Engagement によって使用されるすべてのリソース ファイルを置換し、プロジェクトの参照に新しい Engagement の DLL を追加します。

Capptain DLL の参照を削除して、プロジェクトの参照をクリーンアップする必要があります。これを実行しない場合は、Capptain のバージョンが競合してエラーが発生します。

Capptain リソースをカスタマイズした場合、古いファイルの内容をコピーし、新しい Engagement ファイルに貼り付けます。xaml と cs ファイルの両方を更新する必要があることにご注意ください。

これらの手順が完了したら、新しい Engagement の参照で古い Capptain の参照を置き換える必要があります。

1. すべての Capptain 名前空間の更新が必要です。

	移行前:
	
		using Capptain.Agent;
		using Capptain.Reach;
	
	移行後:
	
		using Microsoft.Azure.Engagement;

2. "Capptain" が含まれているすべての Capptain クラスには、"Engagement" が含まれている必要があります。

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

3. xaml ファイルの Capptain 名前空間と属性も変更します。

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

4. オーバーレイ ページの変更
	> [AZURE.IMPORTANT]オーバーレイも変更します。その新しい名前空間は `Microsoft.Azure.Engagement.Overlay` です。xaml と cs ファイルの両方で使用する必要があります。さらに、`CapptainGrid` は `EngagementGrid` と名前が付けられ、`capptain_notification_content` と `capptain_announcement_content` は `engagement_notification_content` と `engagement_announcement_content` と名前が付けられます。
	
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

5. Capptain 画像や HTML ファイルなどその他のリソースについては、"Engagement" を使用して名前が変更されていることにもご注意ください。

### プロジェクトの宣言

Package.appxmanifest で `File Type Associations` が次から更新されています。

 -   capptain\_reach\_content to engagement\_reach\_content
 -   capptain\_log\_file to engagement\_log\_file

### アプリケーション ID / SDK キー

Engagement は、接続文字列を使用します。Mobile Engagement でアプリケーション ID と SDK キーを指定する必要はありません。指定する必要があるのは接続文字列のみです。接続文字列は、EngagementConfiguration ファイルで設定できます。

Engagement の構成は、プロジェクトの `Resources\EngagementConfiguration.xml` ファイルで設定できます。

このファイルを編集して、次の内容を指定します。

-   `<connectionString>` タグと `<\connectionString>` タグの間のアプリケーション接続文字列。

代わりに指定を実行時に行う場合は、Engagement エージェントを初期化する前に、次のメソッドを呼び出すことができます。

	/* Engagement configuration. */
	EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
	engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
	
	/* Initialize Engagement agent with above configuration. */
	EngagementAgent.Instance.Init(args, engagementConfiguration);

アプリケーションの接続文字列が Azure 管理ポータルに表示されます。

### 項目名の変更

*capptain* という名前の項目はすべて *engagement* という名前に変更されています。同様に、*Capptain* は *Engagement* に変更されています。

一般的に使用される Capptain 項目の例:

-   CapptainConfiguration は EngagementConfiguration という名前になりました
-   CapptainAgent は EngagementAgent という名前になりました
-   CapptainReach は EngagementReach という名前になりました
-   CapptainHttpConfig は EngagementHttpConfig という名前になりました
-   GetCapptainPageName は GetEngagementPageName という名前になりました

名前の変更はオーバーライドされたメソッドにも影響することにご注意ください。

 

<!---HONumber=August15_HO7-->