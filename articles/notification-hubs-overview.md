<properties 
	pageTitle="Azure Notification Hubs" 
	description="Azure でのプッシュ通知の使用方法について説明します。コード サンプルは .NET API を使用して C# で記述されています。" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services="notification-hubs" 
	documentationCenter=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="02/24/2015" 
	ms.author="wesmc"/>


#Azure Notification Hubs

##概要

Azure でプッシュ通知がサポートされたことで、マルチプラットフォームに対応し、簡単に使用できる、スケールアウトされたプッシュ通知インフラストラクチャを利用できるようになりました。これにより、モバイル プラットフォーム向けアプリケーション (コンシューマー用途およびエンタープライズ用途) にプッシュ通知機能を実装する作業が大幅に簡略化されます。

##プッシュ通知とは?

スマートフォンやタブレットでは、イベントの発生時にそれをユーザーに "知らせる" 機能があります。Windows ストア アプリケーションでは、_トースト_で通知される場合があります。つまり、新しい通知を知らせるモードレス ウィンドウが表示され、効果音が鳴ります。Apple iOS デバイスでも同様に、ダイアログ ボックスが操作に割り込むように表示され、ユーザーは通知を表示するか、閉じるかを選択できます。**[View]** をタップすると、そのメッセージを受信するアプリケーションが開きます。

プッシュ通知は、モバイル デバイスの電力消費を抑えながら、最新情報を表示するために有効な機能です。プッシュ通知はコンシューマー アプリケーションの必須コンポーネントです。プッシュ通知を利用して、ユーザーをアプリケーションに惹き付け、使用率を向上させるためです。エンタープライズ アプリケーションでも、通知は役立ちます。従業員に最新情報を通知することで、ビジネス イベントに対する即応性を向上することができます。 

次に、モバイル デバイス関連のシナリオの具体例を示します。

1.  Windows 8 または Windows Phone のタイルに最新の財務情報を表示する。
2.  ワークフロー ベースのエンタープライズ アプリケーションで、ユーザーに作業項目が割り当てられたときに、トーストでユーザーに警告を表示する。
3.  CRM アプリケーション (Microsoft Dynamics CRM など) で、現在の潜在顧客の数を示すバッジを表示する。

##プッシュ通知の動作

プッシュ通知は、プラットフォーム通知システム (PNS: Platform Notification System) と呼ばれるプラットフォーム独自のインフラストラクチャを利用して配信されます。PNS の機能は最小限であり、ブロードキャストや個人向け設定などはサポートされず、共通インターフェイスも用意されていません。たとえば、Windows ストア アプリに通知を送信するには、WNS (Windows Notification Service) にアクセスする必要があります。また、iOS デバイスに通知を送信するには、まず APNS (Apple Push Notification Service) にアクセスした後で、メッセージを送信する必要があります。

ただし、大まかに言えば、すべてのプラットフォームの通知システムが同じパターンに従って動作します。

1.  クライアント アプリケーションが PNS にアクセスし、_ハンドル_を取得します。ハンドルの種類はシステムにより異なります。WNS の場合、ハンドルは URI または "通知チャネル" です。APNS の場合、ハンドルはトークンです。
2.  クライアント アプリは、このハンドルをアプリの「バックエンド」に格納し、後で使用できるようにします。WNS の場合、一般的にバックエンドはクラウド サービスになります。Apple の場合は、このシステムを「プロバイダー」と呼びます。
3.  プッシュ通知を送信する際には、アプリケーションのバックエンドがハンドルを使用して PNS にアクセスし、特定のクライアント アプリケーション インスタンスを対象として指定します。
4.  PNS はハンドルで指定されたデバイスに通知を転送します。

![][0]

##プッシュ通知の課題

以上のシステムは高度な機能を備えていますが、ブロードキャストやユーザーへのプッシュ通知の送信などの一般的なプッシュ通知シナリオを実装するだけでも、アプリケーション開発者はさらに多くの課題を克服する必要があります。

プッシュ通知は、モバイル アプリケーション向けのクラウド サービスで最も需要の高い機能の 1 つです。それは、プッシュ通知を機能させるために必要なインフラストラクチャが非常に複雑であることに加え、プッシュ通知の機能がアプリケーションの主要なビジネス ロジックと無関係であることが多いためです。オンデマンド プッシュ インフラストラクチャを構築する際に課題となるのは、次のような点です。

- **プラットフォームへの依存。**異なるプラットフォーム上のデバイスに通知を送信するには、バックエンドで複数のインターフェイスをコーディングする必要があります。低レベルの詳細が異なるだけでなく、通知の表示方法 (タイル、トースト、バッジ) もプラットフォームに依存します。バックエンドでのコーディングが複雑で保守が困難になる原因は、これらの違いによるものです。

- **拡張性。**このインフラストラクチャの拡張に関しては、次の 2 つの問題点があります。
1. PNS のガイドラインに従って、アプリが起動するたびにデバイス トークンを更新する必要があります。そのため、デバイス トークンを最新の状態に維持するためだけに膨大なトラフィック (およびそれに伴うデータベース アクセス) が発生します。デバイスの数が増加すると (数百万台に達する可能性があります)、このインフラストラクチャの作成と保守にかかるコストが無視できないものになります。
2.  ほとんどの PNS は、複数のデバイスに対するブロードキャストをサポートしていません。そのため、数百万台のデバイスに対してブロードキャストを実行すると、PNS に対して数百万回の呼び出しを実行することになります。このように要求が増大する可能性があることは、重大な問題です。一般的に、アプリケーション開発者は合計遅延時間をできるだけ少なくしようとするためです (たとえば、最終的にメッセージを受信するデバイスが通知の送信後 30 分経ってもメッセージを受信しなければ、ほとんどの場合、プッシュ通知を使用する意味がなくなります)。
- **ルーティング。**PNS は、デバイスにメッセージを送信する方法を提供します。ただし、ほとんどのアプリケーション通知はユーザーや対象グループ (特定の顧客アカウントに割り当てられているすべての従業員など) を対象とします。そのため、正しいデバイスに通知をルーティングするには、アプリケーションのバックエンドで対象グループとデバイス トークンを関連づけるレジストリを保守する必要があります。このオーバーヘッドにより、アプリケーションが市場に出るまでの時間が増え、メンテナンス コストが上昇します。

##Notification Hubs を使用する理由は?

Notification Hubs はすぐに利用できるプッシュ通知インフラストラクチャであり、次の機能がサポートされます。

- **複数のプラットフォーム。**Notification Hubs にはサポート対象のプラットフォームすべてに通知を送信するための共通のインターフェイスが備わっています。通知はアプリケーションのバックエンドからプラットフォーム固有の形式、またはプラットフォームに依存しない形式で送信されます。Notification Hubs では、Windows ストア、iOS、Android、および Windows Phone のアプリケーションに対するプッシュ通知をサポートしています。
- **パブリッシュ/サブスクライブ ルーティング。**Notification Hubs にハンドルを送信するときに、各デバイスで 1 つ以上の「タグ」を指定できます。タグの詳細については、次のセクションを参照してください。タグは事前に定義したり、破棄する必要はありません。タグを使用すると、ユーザーまたは対象グループに簡単に通知を送信できます。タグにはアプリケーション固有の識別子 (ユーザー ID やグループ ID など) を含めることができるため、これらを使用することでアプリケーションのバックエンドでデバイス ハンドルを格納して管理する必要がなくなります。
- **拡張性。**Notification Hubs は再設計やシャーディングなしで、数百万のデバイスに対応します。

通知ハブはマルチプラットフォームに完全対応する、スケールアウトされたプッシュ通知インフラストラクチャを使用しており、アプリケーションのバックエンドで実行するプッシュ通知用コードを大幅に減らすことができます。通知ハブは、プッシュ通知インフラストラクチャのすべての機能を備えています。デバイス側の処理は PNS ハンドルを登録するだけであり、バックエンドによってプラットフォームに依存しないメッセージがユーザーまたは対象グループに送信されます。

![][1]

##次のステップ

Notification Hubs の詳細については、次のトピックを参照してください。

+ **[Notification Hubs の機能と特徴]**

+ **[Notification Hubs のチュートリアルとガイド]** 

+ **Notification Hubs の使用チュートリアル** ([iOS]、[Android]、[Windows Universal]、[Windows Phone]、[Kindle]、[Xamarin.iOS]、[Xamarin.Android])

プッシュ通知の関連する .NET マネージ API リファレンスについては、次を参照してください。

+ [Microsoft.WindowsAzure.Messaging.NotificationHub]
+ [Microsoft.ServiceBus.Notifications] 


  [0]: ./media/notification-hubs-overview/SBPushNotifications1.gif
  [1]: ./media/notification-hubs-overview/SBPushNotifications2.gif
  [Notification Hubs の機能と特徴]: http://azure.microsoft.com/services/notification-hubs
  [Notification Hubs のチュートリアルとガイド]: http://azure.microsoft.com/documentation/services/notification-hubs
  [iOS]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started
  [Android]: http://azure.microsoft.com/documentation/articles/notification-hubs-android-get-started
  [Windows Universal]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started
  [Windows Phone]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-phone-get-started
  [Kindle]: http://azure.microsoft.com/documentation/articles/notification-hubs-kindle-get-started
  [Xamarin.iOS]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-ios-get-started
  [Xamarin.Android]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-android-get-started
  [Microsoft.WindowsAzure.Messaging.NotificationHub]: http://msdn.microsoft.com/library/microsoft.windowsazure.messaging.notificationhub.aspx
  [Microsoft.ServiceBus.Notifications]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.aspx
  

<!--HONumber=49-->