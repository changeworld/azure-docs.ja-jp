<properties 
	pageTitle="モバイル分析の使用 | モバイル デベロッパー センター" 
	description="モバイル分析の使用" 
	documentationCenter="ios" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="06/03/2015" 
	ms.author="mahender"/>

# モバイル分析の使用 (Capptain)

<div class="dev-center-tutorial-selector sublanding">
<a href="/documentation/articles/mobile-services-ios-get-started-mobile-analytics" title="iOS" class="current">iOS</a>
</div>

このチュートリアルでは、[Capptain] を使用して、モバイル分析機能をアプリに追加します。モバイル分析によって、ユーザーがアプリとやり取りしている方法と、アクティビティが最大のセクションを判別できます。これらのデータの取得を開始するには、Capptain SDK を使用してアプリをインストルメントします。


>[AZURE.NOTE]マイクロソフトが所有する Capptain.com は、Azure Mobile Services Standard レベルのお客様を対象として、月間最大 100,000 アクティブ ユーザーにモバイル アプリの分析を無償で提供しています。このサービスを利用するための詳細な手順については、mobileservices@microsoft.com にお問い合わせください。以下のチュートリアルでは、Capptain.com 機能の概要と、それらの使用方法に関する手順を説明します。


このチュートリアルでは、次の基本的な手順について説明します。

1. [Capptain SDK の開始]
2. [UIViewController のオーバーロード]

このチュートリアルには、次のものが必要です。

* [Capptain] アカウント
* [Mobile Services Standard レベル]のアプリ

## <a name="initialize"></a>Capptain SDK の開始

1. Capptain に登録済みのアプリの **[アプリケーションの詳細]** ページに移動します。[SDK] タブをクリックして、パッケージをダウンロードします。

2. [XCode] で、プロジェクトを右クリックし、[ファイルの追加] を選択して、Capptain SDK をプロジェクトに追加します。 CapptainSDK フォルダーを選択します。

3. プロジェクトを選択します。**[フェーズの作成]** タブの下で、**[バイナリとライブラリをリンク]** を選択して、次のフレームワークを追加します。
    * AdSupport.framework - リンクをオプションとして設定します
    * SystemConfiguration.framework
    * CoreTelephony.framework
    * CFNetwork.framework
    * CoreLocation.framework
    * libxml2.dylib

    >[AZURE.NOTE]AdSupport フレームワークは削除してもかまいません。Capptain では、IDFA を収集するために、このフレームワークが必要となります。ただし、IDFA コレクションは、この ID に関する Apple ポリシーに準じて無効にできます。

4. アプリケーション デリゲート実装ファイルで、Capptain エージェントをインポートします。


        #import "CapptainAgent.h"


5. `applicationDidFinishLaunching:` または `application:didFinishLaunchingWithOptions:` 内で `registerapp:identifiedby:` にアプリ ID と SDK キーを渡して、Capptain エージェントを開始します。

         - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
          [...]
          [CapptainAgent registerApp:@"YOUR_APPID" identifiedBy:@"YOUR_SDK_KEY"];
          [...]
        }

## <a name="instrument"></a>UIViewController のオーバーロード

1. プロジェクト内で `UIViewController` の子を見つけ、それぞれが `CapptainViewController` から継承していることを確認します。

        #import <UIKit/UIKit.h>
        #import "CapptainViewController.h"

        // formerly @interface Tab1ViewController : UIViewController<UITextFieldDelegate>
        @interface Tab1ViewController : CapptainViewController<UITextFieldDelegate> {
          UITextField* myTextField1;
          UITextField* myTextField2;
        }

        @property (nonatomic, retain) IBOutlet UITextField* myTextField1;
        @property (nonatomic, retain) IBOutlet UITextField* myTextField2;

2. プロジェクト内で `UITableViewController` の子を見つけ、それぞれが `CapptainTableViewController` から継承していることを確認します。

    これで、アプリは分析データを Capptain に送信するように構成されました。

## 次のステップ
Capptain がアプリに対して実行できる機能の詳細については、[http://www.capptain.com](http://www.capptain.com) を参照してください。

<!-- Anchors. -->
[Capptain SDK の開始]: #initialize
[UIViewController のオーバーロード]: #instrument


<!-- URLs. -->
[Capptain]: http://www.capptain.com
[Mobile Services Standard レベル]: /pricing/details/mobile-services/
 

<!---HONumber=July15_HO4-->