<properties 
	pageTitle="Azure Mobile Engagement iOS SDK の統合" 
	description="iOS SDK for Azure Mobile Engagement の最新の更新プログラムと手順"
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />


<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/mobile-engagement-windows-store-integrate-engagement/" title="Windows Store">Windows ストア</a><a href="/documentation/articles/mobile-engagement-windows-phone-integrate-engagement/" title="Windows Phone">Windows Phone</a><a href="/documentation/articles/mobile-engagement-ios-integrate-engagement/" title="iOS" class="current">iOS</a><a href="/documentation/articles/mobile-engagement-android-integrate-engagement/" title="Android" >Android</a></div>


#Engagement を iOS に統合する方法

> [AZURE.IMPORTANT] Engagement SDK には iOS4 以降が必要です。アプリケーションの展開ターゲットは iOS 4 以降である必要があります。

この手順では、iOS アプリケーションで Engagement の分析機能、監視機能を有効化する最も簡単な方法を説明します。

次の手順では、ユーザー、セッション、アクティビティ、クラッシュ、技術に関するすべての統計の計算に必要なログのレポートを有効化できます。イベント、エラー、ジョブなど、その他の統計はアプリケーションに依存するため、その計算に必要なログのレポートは Engagement API (ios-sdk-engagement-advanced を参照してください) を使用して手動で実行する必要があります。

##Engagement SDK を iOS プロジェクトに埋め込む

Engagement SDK の iOS プロジェクトへの追加: Xcode 4 でプロジェクトを右クリックし、**[Add files to ...]** を選択して、 `EngagementSDK` フォルダーを選択します。

Engagement では追加のフレームワークを動作させる必要がある: プロジェクト エクスプローラーでプロジェクト ペインを開き、適切なターゲットを選択します。次に、**[Build phases]** タブを開き、**[Link Binary With Libraries]** メニューでこれらのフレームワークを追加します。

> - `AdSupport.framework` - リンクを `オプション`として設定します。
> -   `SystemConfiguration.framework`
> -   `CoreTelephony.framework`
> -   `CFNetwork.framework`
> -   `CoreLocation.framework`
> -   `libxml2.dylib`

> [AZURE.NOTE] AdSupport フレームワークは削除してもかまいません。Engagement では、IDFA の収集のためこのフレームワークが必要です。ただし、IDFA コレクションは、この ID に関する新しい Apple ポリシーに準じて無効にできます\<ios-sdk-engagement-idfa\>。

##Engagement SDK の初期化

アプリケーション デリゲートを変更する必要がある: 

-   実装ファイルの先頭で、Engagement エージェントをインポートします。

			[...]
			#import "EngagementAgent.h"

-   メソッド '**applicationDidFinishLaunching:**' または '**application:didFinishLaunchingWithOptions:**' 内で Engagement を初期化します。

			- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
			{
			  [...]
			  [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
			  [...]
			}

##基本レポート

### 推奨される方法:  `UIViewController` クラスをオーバーロードします

Engagement でのユーザー、セッション、アクティビティ、クラッシュ、技術に関する統計の計算に必要なすべてのログのレポートを有効化するには、単にすべての  `UIViewController` サブクラスを  `EngagementViewController` クラスから継承するように設定します ( `UITableViewController` -\> `EngagementTableViewController` と同じルール)。

**Engagement なし:**

			#import <UIKit/UIKit.h>
			
			@interface Tab1ViewController : UIViewController<UITextFieldDelegate> {
			  UITextField* myTextField1;
			  UITextField* myTextField2;
			}
			
			@property (nonatomic, retain) IBOutlet UITextField* myTextField1;
			@property (nonatomic, retain) IBOutlet UITextField* myTextField2;

**Engagement あり:**

			#import <UIKit/UIKit.h>
			#import "EngagementViewController.h"
			
			@interface Tab1ViewController : EngagementViewController<UITextFieldDelegate> {
			  UITextField* myTextField1;
			  UITextField* myTextField2;
			}
			
			@property (nonatomic, retain) IBOutlet UITextField* myTextField1;
			@property (nonatomic, retain) IBOutlet UITextField* myTextField2;

### 別の方法:  `startActivity()` を手動で呼び出します

 `UIViewController` クラスをオーバーロードできないか、オーバーロードしたくない場合は、 `EngagementAgent` のメソッドを直接呼び出して、活動を開始できます。

> [AZURE.IMPORTANT] iOS SDK は、アプリケーションが閉じられると、 `endActivity()` メソッドを自動的に呼び出します。このため、ユーザーのアクティビティが変更された場合は常に  `startActivity` メソッドを呼び出し、 `endActivity` メソッドは *決して*呼び出さないことを *強く*お勧めします。これは、このメソッドを呼び出すと、現在のセッションが強制的に終了されるためです。

##場所レポート

Apple の規約では、統計の目的に限定されたアプリケーションでの場所の追跡の使用は認められていません。このため、アプリケーションが他の目的でも場所の追跡機能を使用する場合にのみ、場所レポートを有効にすることをお勧めします。

iOS 8 以降では、アプリケーションの Info.plist ファイルにキー [NSLocationWhenInUseUsageDescription] または [NSLocationAlwaysUsageDescription] の文字列を設定して、アプリケーションで場所サービスをどのように使用するかの説明を提供する必要があります。Engagement を使って背景で場所をレポートするには、キー NSLocationAlwaysUsageDescription を追加します。それ以外の場合は、キー NSLocationWhenInUseUsageDescription を追加します。

### 遅延エリアの場所レポート

遅延エリアの場所レポートでは、デバイスに関連付けられた国、リージョン、地域をレポートできます。このタイプの場所レポートでは、セル ID または WIFI に基づいたネットワークの場所のみを使用します。デバイス エリアがセッションごとに最大 1 回レポートされます。GPS を使用しないため、このタイプの場所レポートでは、まったくとは言わないまでも、電力消費量にほとんど影響がありません。

報告されたエリアを基に、ユーザー、セッション、イベント、エラーに関する地理的な統計を計算します。また、リーチ キャンペーンの条件としても使用されます。デバイスに関する最新の既知のエリアは[デバイス API] を利用して取得できます。

遅延エリアの場所レポートを有効にするには、Engagement エージェントを初期化した後、次の行を追加します。

			- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
			{
			  [...]
			  [[EngagementAgent shared] setLazyAreaLocationReport:YES];
			  [...]
			}

### リアル タイム場所レポート

リアル タイム場所レポートでは、デバイスに関連付けられた緯度と経度をレポートできます。既定では、このタイプの場所レポートでは (セル ID または WIFI に基づいた) ネットワークの場所のみが使用されます。レポートは、アプリケーションを前景で実行した場合 (セッション中) にのみアクティブになります。

リアル タイム場所レポートは、統計の計算に使用することは *できません*。このレポートの目的は、リーチ キャンペーンでリアル タイムの 
geo-fencing \<Reach-Audience-geofencing\> 条件を使用できるようにすることのみです。

リアル タイム場所レポートを有効にするには、Engagement エージェントの初期化後に、次の行を追加します。

			[[EngagementAgent shared] setRealtimeLocationReport:YES];

#### GPS ベースのレポート

既定では、リアル タイム場所レポートでは、ネットワーク ベースの場所のみを使用します。GPS ベースの場所 (正確性が格段に優れています) を使用できるようにするには、以下を追加します。

			[[EngagementAgent shared] setFineRealtimeLocationReport:YES];

#### 背景レポート

既定では、リアル タイム場所レポートは、アプリケーションを前景で実行した場合 (セッション中) にのみアクティブになります。バックグウランドでのレポートも有効にするには、以下を追加します。

			[[EngagementAgent shared] setBackgroundRealtimeLocationReport:YES withLaunchOptions:launchOptions];

> [AZURE.NOTE] アプリケーションを背景で実行した場合、GPS を有効にしても、ネットワーク ベースの場所のみがレポートされます。

この機能を実装すると、アプリケーションが背景になったときに、[startMonitoringSignificantLocationChanges] が呼び出されます。これにより、新しい場所イベントが発生すると、アプリケーションが背景に自動的に再起動されることに注意してください。

##詳細レポート

オプションとして、アプリケーション固有のイベント、エラー、ジョブをレポートする必要がある場合は、 `EngagementAgent` クラスのメソッドを通じて Engagement API を使用します。このクラスのオブジェクトを取得するには、`[EngagementAgent shared]` 静的メソッドを呼び出します。

Engagement API では、すべての Engagement の高度な機能を使用でき、
iOS で Engagement API を使用する方法において詳細な情報が提供されます (また、 `EngagementAgent` クラスの技術文書においても)。

##IDFA コレクションの無効化

既定では、Engagement は [IDFA] を使用して、ユーザーを一意に特定します。ただし、アプリの他の場所で広告を使用していない場合、App Store のレビュー プロセスで拒否される場合があります。IDFA コレクションは、プリプロセッサのマクロ  `ENGAGEMENT_DISABLE_IDFA` を pch ファイル (またはアプリケーションの  `[Build Settings]` に追加することによって無効にできます。これにより、アプリケーション ビルドの  `ASIdentifierManager`、 `advertisingIdentifier`、 `isAdvertisingTrackingEnabled` への参照がないことを確認できます。

**prefix.pch** ファイルでの統合:

			#define ENGAGEMENT_DISABLE_IDFA
			...

IDFA コレクションがアプリケーション内で適切に無効化されていることを確認するには、Engagement テスト ログをチェックします。詳細については、統合テスト\<ios-sdk-engagement-test-idfa\>に関するドキュメントを参照してください。

##ログ レポートの無効化

### メソッド呼び出しを使用した場合

Engagement でログの送信を停止したい場合は、以下を呼び出します。

			[[EngagementAgent shared] setEnabled:NO];

この呼び出しは永続的です:  `NSUserDefaults` を使用して情報を格納します。

ログ レポートを有効にするには、 `YES` で同じ機能を呼び出します。

### 設定バンドルでの統合

この機能を呼び出す代わりに、この設定を既存の  `Settings.bundle` ファイルで直接統合することもできます。文字列  `engagement_agent_enabled` は優先識別子として使用し、トグル スイッチ (`PSToggleSwitchSpecifier`) に関連付ける必要があります。

次の  `Settings.bundle` の例は、その実装方法を示しています。

			<dict>
			    <key>PreferenceSpecifiers</key>
			    <array>
			        <dict>
			            <key>DefaultValue</key>
			            <true/>
			            <key>Key</key>
			            <string>engagement_agent_enabled</string>
			            <key>Title</key>
			            <string>Log reporting enabled</string>
			            <key>Type</key>
			            <string>PSToggleSwitchSpecifier</string>
			        </dict>
			    </array>
			    <key>StringsTable</key>
			    <string>Root</string>
			</dict>

<!-- URLs. -->
[デバイス API]: http://go.microsoft.com/?linkid=9876094
[NSLocationWhenInUseUsageDescription]:https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26
[NSLocationAlwaysUsageDescription]:https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW18
[startMonitoringSignificantLocationChanges]:http://developer.apple.com/library/IOs/#documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html#//apple_ref/occ/instm/CLLocationManager/startMonitoringSignificantLocationChanges
[IDFA]:https://developer.apple.com/library/ios/documentation/AdSupport/Reference/ASIdentifierManager_Ref/ASIdentifierManager.html#//apple_ref/occ/instp/ASIdentifierManager/advertisingIdentifier

<!--HONumber=47-->
