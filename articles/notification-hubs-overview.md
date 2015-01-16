<properties urlDisplayName="Azure Notification Hubs" pageTitle="Azure Notification Hubs" metaKeywords="Azure のプッシュ通知, Azure の通知ハブ, Azure のメッセージング" description="Azure でのプッシュ通知の使用方法について説明します。コード サンプルは .NET API を使用して C# で記述されています。" metaCanonical="" disqusComments="1" umbracoNaviHide="0" title="Azure Notification Hubs" authors="sethm" manager="dwrede" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="09/24/2014" ms.author="sethm" />


#Azure 通知ハブ


Azure でプッシュ通知がサポートされたことで、マルチプラットフォームに対応し、簡単に使用できる、スケールアウトされたプッシュ通知インフラストラクチャを利用できるようになりました。これにより、モバイル プラットフォーム向けアプリケーション (コンシューマー用途およびエンタープライズ用途) にプッシュ通知機能を実装する作業が大幅に簡略化されます。

<h2>プッシュ通知とは?</h2>

スマートフォンやタブレットでは、イベントの発生時にそれをユーザーに "知らせる" 機能があります。Windows ストア アプリケーションの場合、通知は_トースト_として表示されます。つまり、新しい通知を知らせるモードレス ウィンドウが表示され、効果音が鳴ります。Apple iOS デバイスでも同様に、ダイアログ ボックスが操作に割り込むように表示され、ユーザーは通知を表示するか、閉じるかを選択できます。**[View]** をタップすると、そのメッセージを受信するアプリケーションが開きます。

プッシュ通知は、モバイル デバイスの電力消費を抑えながら、最新情報を表示するために有効な機能です。プッシュ通知はコンシューマー アプリケーションの必須コンポーネントです。プッシュ通知を利用して、ユーザーをアプリケーションに惹き付け、使用率を向上させるためです。エンタープライズ アプリケーションでも、通知は役立ちます。従業員に最新情報を通知することで、ビジネス イベントに対する即応性を向上することができます。 

次に、モバイル デバイス関連のシナリオの具体例を示します。

1.  Windows 8 または Windows Phone のタイルに最新の財務情報を表示する。
2.  ワークフロー ベースのエンタープライズ アプリケーションで、ユーザーに作業項目が割り当てられたときに、トーストでユーザーに警告を表示する。
3.  CRM アプリケーション (Microsoft Dynamics CRM など) で、現在の潜在顧客の数を示すバッジを表示する。

<h2>プッシュ通知の動作</h2>

プッシュ通知は、プラットフォーム通知システム (PNS: Platform Notification System) と呼ばれるプラットフォーム独自のインフラストラクチャを利用して配信されます。PNS の機能は最小限であり、ブロードキャストや個人向け設定などはサポートされず、共通インターフェイスも用意されていません。たとえば、Windows ストア アプリに通知を送信するには、WNS (Windows Notification Service) にアクセスする必要があります。また、iOS デバイスに通知を送信するには、まず APNS (Apple Push Notification Service) にアクセスした後で、メッセージを送信する必要があります。

ただし、大まかに言えば、すべてのプラットフォームの通知システムが同じパターンに従って動作します。

1.  クライアント アプリが PNS にアクセスし、_ハンドル_を取得します。ハンドルの種類はシステムにより異なります。WNS の場合、ハンドルは URI または "通知チャネル" です。APNS の場合、ハンドルはトークンです。
2.  クライアント アプリは、このハンドルをアプリの_バックエンド_に格納し、後で使用できるようにします。WNS の場合、一般的にバックエンドはクラウド サービスになります。Apple の場合は、このシステムを_プロバイダー_と呼びます。
3.  プッシュ通知を送信する際には、アプリケーションのバックエンドがハンドルを使用して PNS にアクセスし、特定のクライアント アプリケーション インスタンスを対象として指定します。
4.  PNS はハンドルで指定されたデバイスに通知を転送します。

![][0]

<h2>プッシュ通知の課題</h2>

以上のシステムは高度な機能を備えていますが、ブロードキャストやユーザーへのプッシュ通知の送信などの一般的なプッシュ通知シナリオを実装するだけでも、アプリケーション開発者はさらに多くの課題を克服する必要があります。

プッシュ通知は、モバイル アプリケーション向けのクラウド サービスで最も需要の高い機能の 1 つです。それは、プッシュ通知を機能させるために必要なインフラストラクチャが非常に複雑であることに加え、プッシュ通知の機能がアプリケーションの主要なビジネス ロジックと無関係であることが多いためです。オンデマンド プッシュ インフラストラクチャを構築する際に課題となるのは、次のような点です。

- **プラットフォーム依存。**さまざまなプラットフォームのデバイスに通知を送信するには、複数のインターフェイスに対応するコードをバックエンドに用意する必要があります。詳細な仕様が異なるだけでなく、通知の表示方法 (タイル、トースト、バッジなど) もプラットフォームに依存します。このため、バックエンド コードは複雑化し、保守が困難になります。

- **拡張性。** このインフラストラクチャの拡張に関しては、次の 2 つの問題点があります。
1. PNS のガイドラインに従って、アプリケーションが起動されるたびにデバイス トークンを更新する必要があります。そのため、デバイス トークンを最新の状態に維持するためだけに膨大なトラフィック (およびそれに伴うデータベース アクセス) が発生します。デバイスの数が増加すると (数百万台に達する可能性があります)、このインフラストラクチャの作成と保守にかかるコストが無視できないものになります。
2.  ほとんどの PNS は、複数のデバイスに対するブロードキャストをサポートしていません。そのため、数百万台のデバイスに対してブロードキャストを実行すると、PNS に対して数百万回の呼び出しを実行することになります。これらの要求を減少させるのは、簡単なことではありません。一般的に、アプリケーション開発者は合計遅延時間をできるだけ少なくしようとするためです (たとえば、最終的にメッセージを受信するデバイスが通知の送信後 30 分経ってもメッセージを受信しなければ、ほとんどの場合、プッシュ通知を使用する意味がなくなります)。
- **ルーティング。**PNS は、デバイスにメッセージを送信する機能を備えています。しかし、ほとんどのアプリケーションでは通知の対象がユーザーまたは特定のグループです (特定の顧客アカウントに割り当てられた全従業員など)。そのため、通知を適切なデバイスにルーティングするには、特定のグループにデバイス トークンを関連付けるためのレジストリをアプリケーションのバックエンドで維持する必要があります。この対策を行うことで、市場投入までの開発期間が長引き、アプリケーションの保守コストも増大します。

<h2>Notification Hubs を使用する理由は?</h2>

通知ハブはすぐに利用できるプッシュ通知インフラストラクチャであり、次の機能がサポートされます。

- **複数のプラットフォーム。**通知ハブは、サポート対象のすべてのプラットフォームに通知を送信する共通インターフェイスを備えています。アプリケーションのバックエンドから、通知をプラットフォームの独自形式で送信することも、プラットフォームに依存しない形式で送信することもできます。通知ハブからプッシュ通知を送信できるのは、Windows ストア、iOS、Android、および Windows Phone のアプリケーションです。
- **パブリッシュ/サブスクライブ ルーティング。**各デバイスのハンドルを通知ハブに送信する際に、1 つ以上の_タグ_を指定することができます。タグの詳細については、以下のセクションを参照してください。タグは事前に定義したり、破棄したりする必要はありません。タグを使用して、通知を簡単にユーザーや対象グループに送信できます。タグにはアプリケーション独自の識別子 (ユーザー ID、グループ ID など) を含めることができるため、タグを使用することで、アプリケーションのバックエンドでデバイス ハンドルの格納や管理を行う必要がなくなります。
- **拡張性。** 通知ハブは再設計やシャーディングを行わなくても、数百万のデバイスに対応できます。

通知ハブはマルチプラットフォームに完全対応する、スケールアウトされたプッシュ通知インフラストラクチャを使用しており、アプリケーションのバックエンドで実行するプッシュ通知用コードを大幅に減らすことができます。通知ハブは、プッシュ通知インフラストラクチャのすべての機能を備えています。デバイス側の処理は PNS ハンドルを登録するだけであり、バックエンドによってプラットフォームに依存しないメッセージがユーザーまたは対象グループに送信されます。

![][1]

## 次のステップ

Notification Hubs の詳細については、次のトピックを参照してください。

+ **[ユーザーは Notification Hubs をどのように使用しているか]**

+ **[Notification Hubs のチュートリアルとガイド]** 

+ **Notification Hubs の使用チュートリアル** ([iOS], [Android], [Windows Universal], [Windows Phone], [Kindle], [Xamarin.iOS], [Xamarin.Android])

プッシュ通知の関連する .NET マネージ API リファレンスについては、次を参照してください。

+ [Microsoft.WindowsAzure.Messaging.NotificationHub]
+ [Microsoft.ServiceBus.Notifications] 


  [0]: ./media/notification-hubs-overview/SBPushNotifications1.gif
  [1]: ./media/notification-hubs-overview/SBPushNotifications2.gif
  [ユーザーは Notification Hubs をどのように使用しているか]: http://azure.microsoft.com/ja-jp/services/notification-hubs
  [Notification Hubs のチュートリアルとガイド]: http://azure.microsoft.com/ja-jp/documentation/services/notification-hubs
  [iOS]: http://azure.microsoft.com/ja-jp/documentation/articles/notification-hubs-ios-get-started
  [Android]: http://azure.microsoft.com/ja-jp/documentation/articles/notification-hubs-android-get-started
  [Windows ユニバーサル]: http://azure.microsoft.com/ja-jp/documentation/articles/notification-hubs-windows-store-dotnet-get-started
  [Windows Phone]: http://azure.microsoft.com/ja-jp/documentation/articles/notification-hubs-windows-phone-get-started
  [Kindle]: http://azure.microsoft.com/ja-jp/documentation/articles/notification-hubs-kindle-get-started
  [Xamarin.iOS]: http://azure.microsoft.com/ja-jp/documentation/articles/partner-xamarin-notification-hubs-ios-get-started
  [Xamarin.Android]: http://azure.microsoft.com/ja-jp/documentation/articles/partner-xamarin-notification-hubs-android-get-started
  [Microsoft.WindowsAzure.Messaging.NotificationHub]: http://msdn.microsoft.com/ja-jp/library/microsoft.windowsazure.messaging.notificationhub.aspx
  [Microsoft.ServiceBus.Notifications]: http://msdn.microsoft.com/ja-jp/library/microsoft.servicebus.notifications.aspx
  
