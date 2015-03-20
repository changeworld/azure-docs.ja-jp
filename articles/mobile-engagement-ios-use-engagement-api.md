<properties 
	pageTitle="iOS でエンゲージメント API を使用する方法" 
	description="最新の iOS SDK - iOS でエンゲージメント API を使用する方法"
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kapiteir" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="01/24/2015" 
	ms.author="kapiteir" />


# iOS でエンゲージメント API を使用する方法

このドキュメントは、「iOS でエンゲージメントを統合する方法」を補足し、エンゲージメント API を使用してアプリケーションの統計情報を報告する方法について詳しく説明します。

エンゲージメントでアプリケーションのセッション、アクティビティ、クラッシュ、技術情報を報告するだけの場合、最も簡単な方法は、すべてのカスタムの  `UIViewController` オブジェクトを、対応する  `EngagementViewController` クラスから継承することです。

他の方法を行う場合、たとえば、アプリケーション固有のイベント、エラー、ジョブを報告する必要がある場合や、 `EngagementViewController` クラスの実装と異なる方法でアプリケーションのアクティビティを報告する必要がある場合は、エンゲージメント API を使用する必要があります。

エンゲージメント API は、 `EngagementAgent` クラスで指定されます。このクラスのインスタンスは、`[EngagementAgent shared]` 静的メソッドを呼び出すことで取得されます (返される  `EngagementAgent` オブジェクトは １ つです)。

すべての API 呼び出しの前に、メソッド `[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];` を呼び出して、 `EngagementAgent` オブジェクトを初期化する必要があります

## エンゲージメントの概念

次のパートでは、iOS プラットフォームの一般的な [モバイル エンゲージメントの概念](../mobile-engagement-concepts/) について説明します。

### `Session`と `Activity`

 *アクティビティ*は通常、アプリケーションの 1 つの画面に関連付けられます。つまり、 *アクティビティ*は画面が表示されると開始され、画面を閉じると停止します。この場合、エンゲージメント SDK は  `EngagementViewController` クラスを使用して統合されています。

一方、エンゲージメント API を使用して、手動で *アクティビティ*を制御することもできます。これにより、特定の画面をいくつかのサブ パートに分割して、この画面の使用状況の詳細情報を取得できます (たとえば、ダイアログがこの画面内で使用される頻度や時間を知ることができます)。

## アクティビティを報告する

### ユーザーが新しいアクティビティを開始する

			[[EngagementAgent shared] startActivity:@"MyUserActivity" extras:nil];

ユーザー アクティビティが変更されるたびに、 `startActivity()` を呼び出す必要があります。この関数への最初の呼び出しにより、新しいユーザー セッションが開始されます。

### ユーザーが現在のアクティビティを終了する

			[[EngagementAgent shared] endActivity];

> [AZURE.WARNING] 1 つの用途のアプリケーションを複数のセッションに分割する場合を除いて、**絶対に**自分でこの関数を呼び出さないでください。この関数を呼び出すと現在のセッションがすぐに終了するため、後続の  `startActivity()` への呼び出しでは新しいセッションが開始されます。この関数は、アプリケーションを閉じると、SDK によって自動的に呼び出されます。

## イベントを報告する

### セッション イベント

セッション イベントは通常、ユーザーがセッション中に実行したアクションを報告するために使用されます。

**余分なデータがない例:**

			@implementation MyViewController {
			   [...]
			   - (void)willRotateToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation duration:(NSTimeInterval)duration
			   {
			    [super willRotateToInterfaceOrientation:toInterfaceOrientation duration:duration];
			        ...
			    [[EngagementAgent shared] sendSessionEvent:@"will_rotate" extras:nil];
			        ...
			   }
			   [...]
			}

**余分なデータがある例:**

			@implementation MyViewController {
			   [...]
			   - (void)willRotateToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation duration:(NSTimeInterval)duration
			   {
			    [super willRotateToInterfaceOrientation:toInterfaceOrientation duration:duration];
			        ...
			    NSMutableDictionary* extras = [NSMutableDictionary dictionary];
			    [extras setObject:[NSNumber numberWithInt:toInterfaceOrientation] forKey:@"to_orientation_id"];
			    [extras setObject:[NSNumber numberWithDouble:duration] forKey:@"duration"];
			    [[EngagementAgent shared] sendSessionEvent:@"will_rotate" extras:extras];
			        ...
			   }
			   [...]
			}

### スタンドアロン イベント

セッション イベントとは異なり、スタンドアロン イベントはセッションのコンテキスト外で使用されます。

**例:**

			[[EngagementAgent shared] sendEvent:@"received_notification" extras:nil];

## エラーを報告する

### セッション エラー

セッション エラーは通常、ユーザーがセッション中に影響を受けたエラーを報告するために使用されます。

**例:**

			/** The user has entered invalid data in a form */
			@implementation MyViewController {
			  [...]
			  -(void)onMyFormSubmitted:(MyForm*)form {
			    [...]
			    /* The user has entered an invalid email address */
			    [[EngagementAgent shared] sendSessionError:@"sign_up_email" extras:nil]
			    [...]
			  }
			  [...]
			}

### スタンドアロン エラー

セッション エラーとは異なり、スタンドアロン エラーはセッションのコンテキスト外で使用できます。

**例:**

			[[EngagementAgent shared] sendError:@"something_failed" extras:nil];

## ジョブを報告する

**例:**

ログイン プロセスの実行時間を報告する場合を想定します。

			[...]
			-(void)signIn 
			{
			  /* Start job */
			  [[EngagementAgent shared] startJob:@"sign_in" extras:nil];
			
			  [... sign in ...]
			
			  /* End job */
			  [[EngagementAgent shared] endJob:@"sign_in"];
			}
			[...]

### ジョブ中のエラーを報告する

エラーは、現在のユーザー セッションではなく、実行中のジョブに関連付けることができます。

**例:**

ログイン プロセス中にエラーを報告する場合を想定します。

			[...]
			-(void)signin
			{
			  /* Start job */
			  [[EngagementAgent shared] startJob:@"sign_in" extras:nil];
			
			  BOOL success = NO;
			  while (!success) {
			    /* Try to sign in */
			    NSError* error = nil;
			    [self trySigin:&error];
			    success = error == nil;
			
			    /* If an error occured report it */
			    if(!success)
			    {
			      [[EngagementAgent shared] sendJobError:@"sign_in_error"
			                     jobName:@"sign_in"
			                      extras:[NSDictionary dictionaryWithObject:[error localizedDescription] forKey:@"error"]];
			
			      /* Retry after a moment */
			      [NSThread sleepForTimeInterval:20];
			    }
			  }
			
			  /* End job */
			  [[EngagementAgent shared] endJob:@"sign_in"];
			};
			[...]

### ジョブ中のイベント

イベントは、現在のユーザー セッションではなく、実行中のジョブに関連付けることができます。

**例:**

ソーシャル ネットワークを持っていて、ジョブを使用して、ユーザーがサーバーに接続している合計時間を報告する場合を想定します。ユーザーは友達からメッセージを受信できます。これがジョブ イベントです。

			[...]
			- (void) signin
			{
			  [...Sign in code...]
			  [[EngagementAgent shared] startJob:@"connection" extras:nil];
			}
			[...]
			- (void) signout
			{
			  [...Sign out code...]
			  [[EngagementAgent shared] endJob:@"connection"];
			}
			[...]
			- (void) onMessageReceived 
			{
			  [...Notify user...]
			  [[EngagementAgent shared] sendJobEvent:@"connection" jobName:@"message_received" extras:nil];
			}
			[...]

## 追加のパラメーター

任意のデータをイベント、エラー、アクティビティ、ジョブに添付できます。

このデータは構造化することができ、iOS の NSDictionary クラスを使用します。

これには、 `arrays(NSArray, NSMutableArray)`、 `numbers(NSNumber class)`、 `strings(NSString, NSMutableString)`, `urls(NSURL)`, `data(NSData, NSMutableData)`、他の  `NSDictionary` インスタンスが含まれます。

> [AZURE.NOTE] 追加のパラメーターは、JSON でシリアル化されます。上記以外の別のオブジェクトを渡す場合は、クラス内に次のメソッドを実装する必要があります。
>
			 -(NSString*)JSONRepresentation; 
>
> このメソッドは、JSON 表記のオブジェクトを返す必要があります。

### 例

			NSMutableDictionary* extras = [NSMutableDictionary dictionaryWithCapacity:2];
			[extras setObject:[NSNumber numberWithInt:123] forKey:@"video_id"];
			[extras setObject:@"http://foobar.com/blog" forKey:@"ref_click"];
			[[EngagementAgent shared] sendEvent:@"video_clicked" extras:extras];

### 制限

#### キー

 `NSDictionary` の各キーは、次の正規表現と一致する必要があります。

`^[a-zA-Z][a-zA-Z_0-9]*`

キーに使用できるのは、文字、数字、またはアンダースコア (\_) で、先頭は英字にします。

#### サイズ

追加は、1 回の呼び出しにつき **1024** 文字に制限されます (エンゲージメント エージェントによって JSON でエンコードされた場合)。

前の例では、サーバーに送信される JSON は 58 文字です。

			{"ref_click":"http:\/\/foobar.com\/blog","video_id":"123"}

## アプリケーション情報を報告する

 `sendAppInfo:` 関数を使用して、追跡情報 (または他のアプリケーション固有の情報) を手動で報告できます。

特定のキーの最新の値のみを特定のデバイスに保存することで、これらの情報の増分を送信できます。

イベントの追加と同様に、`NSDictionary` クラスはアプリケーション情報の要約に使用されます。配列またはサブディクショナリが (JSON のシリアル化を使用して) 単純な文字列として処理されます。

**例:**

			NSMutableDictionary* appInfo = [NSMutableDictionary dictionaryWithCapacity:2];
			[appInfo setObject:@"female" forKey:@"gender"];
			[appInfo setObject:@"1983-12-07" forKey:@"birthdate"]; // December 7th 1983
			[[EngagementAgent shared] sendAppInfo:appInfo];

### 制限

#### キー

 `NSDictionary` の各キーは、次の正規表現と一致する必要があります。

`^[a-zA-Z][a-zA-Z_0-9]*`

キーに使用できるのは、文字、数字、またはアンダースコア (\_) で、先頭は英字にします。

#### サイズ

アプリケーション情報は、1 回の呼び出しにつき **1024** 文字に制限されます (エンゲージメント エージェントによって JSON でエンコードされた場合)。

前の例では、サーバーに送信される JSON は 44 文字です。

			{"birthdate":"1983-12-07","gender":"female"}


<!--HONumber=47-->
