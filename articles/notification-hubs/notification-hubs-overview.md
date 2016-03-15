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
	ms.tgt_pltfrm="multiple"
	ms.devlang="multiple"
	ms.topic="hero-article"
	ms.date="02/11/2016"
	ms.author="wesmc"/>


#Azure Notification Hubs

##概要

Azure Notification Hubs は、任意のバックエンド (クラウドまたはオンプレミス内) から任意のモバイル プラットフォームにモバイル プッシュ通知を送信できるようにする、使いやすい、マルチプラットフォームのスケールアウトされたプッシュ インフラストラクチャです。

Notification Hubs を使用すると、異なるプラットフォーム通知システム (PNS: Platform Notification System) の詳細を抽象化して、クロスプラットフォームの個人用プッシュ通知を簡単に送信できます。単一の API 呼び出しで、すべてのデバイスの個々のユーザー、または数百万のユーザーを含むセグメント全体を対象にすることができます。

Notification Hubs は、エンタープライズ向けシナリオとコンシューマー向けシナリオの両方で使用できます。次に例を示します。

- 短い待機時間でニュース速報通知を数百万人に送信する (Notification Hubs は、すべての Windows および Windows Phone デバイスにプリインストールされた Bing アプリケーションの機能を強化します)。
- 場所に基づくクーポンをユーザー セグメントに送信する。
- イベント通知をスポーツ/金融/ゲーム アプリケーションのユーザーまたはグループに送信する。
- 新着メッセージ/メールやセールス リードなどのイベントをエンタープライズのユーザーに通知する。
- 多要素認証に必要なワンタイム パスワードを送信する。



##プッシュ通知とは?

スマートフォンやタブレットでは、イベントの発生時にそれをユーザーに "知らせる" 機能があります。これらの通知はさまざまな形式を取ることができます。

Windows ストアと Windows Phone のアプリケーションでは、通知は_トースト_の形になることができます。つまり、モードレス ウィンドウが、新しい通知の到着を知らせる音と共に表示されます。他に、_タイル_、_未加工_、_バッジ_の形などの通知がサポートされます。Windows デバイスでサポートされる通知の種類については、「[タイル、バッジ、通知](http://msdn.microsoft.com/library/windows/apps/hh779725.aspx)」を参照してください。

Apple iOS デバイスでは、プッシュはダイアログ ボックスを使用して通知され、ユーザーに通知を表示するか閉じるかを要求します。**[View]** をタップすると、そのメッセージを受信するアプリケーションが開きます。iOS 通知の詳細については、[iOS 通知に関するページ](http://go.microsoft.com/fwlink/?LinkId=615245)を参照してください。

プッシュ通知は、モバイル デバイスの電力消費を抑えながら、最新情報を表示するために有効な機能です。通知は、デバイスの対応するアプリがアクティブになっていないときでも、バックエンド システムによってモバイル デバイスに送信できます。プッシュ通知はコンシューマー アプリケーションの必須コンポーネントです。プッシュ通知を利用して、ユーザーをアプリケーションに惹き付け、使用率を向上させるためです。エンタープライズ アプリケーションでも、通知は役立ちます。従業員に最新情報を通知することで、ビジネス イベントに対する即応性を向上することができます。

次に、モバイル デバイス関連のシナリオの具体例を示します。

1.  Windows 8 または Windows Phone のタイルに最新の財務情報を表示する。
2.  ワークフロー ベースのエンタープライズ アプリケーションで、ユーザーに作業項目が割り当てられたときに、トーストでユーザーに警告を表示する。
3.  CRM アプリケーション (Microsoft Dynamics CRM など) で、現在の潜在顧客の数を示すバッジを表示する。

##プッシュ通知の動作

プッシュ通知は、_プラットフォーム通知システム_ (PNS: Platform Notification System) と呼ばれるプラットフォーム独自のインフラストラクチャを利用して配信されます。PNS の機能は最小限であり、ブロードキャストや個人向け設定などはサポートされず、共通インターフェイスも用意されていません。たとえば、Windows ストア アプリに通知を送信するには、WNS (Windows Notification Service) にアクセスする必要があります。また、iOS デバイスに通知を送信するには、まず APNS (Apple Push Notification サービス) にアクセスした後で、メッセージを送信する必要があります。Azure Notification hubs は、共通のインターフェイスとその他の機能によって、プラットフォーム間でのプッシュ通知をサポートします。

ただし、大まかに言えば、すべてのプラットフォームの通知システムが同じパターンに従って動作します。

1.  クライアント アプリが PNS にアクセスし、_ハンドル_を取得します。ハンドルの種類はシステムにより異なります。WNS の場合、ハンドルは URI または "通知チャネル" です。 APNS の場合、ハンドルはトークンです。
2.  クライアント アプリは、このハンドルをアプリの_バックエンド_に格納し、後で使用できるようにします。WNS の場合、一般的にバックエンドはクラウド サービスになります。Apple の場合は、このシステムを_プロバイダー_と呼びます。
3.  プッシュ通知を送信する際には、アプリケーションのバックエンドがハンドルを使用して PNS にアクセスし、特定のクライアント アプリケーション インスタンスを対象として指定します。
4.  PNS はハンドルで指定されたデバイスに通知を転送します。

![][0]

##プッシュ通知の課題

これらのシステムは高度な機能を備えていますが、ブロードキャストやセグメント化されたユーザーへのプッシュ通知の送信などの一般的なプッシュ通知シナリオを実装するだけでも、アプリケーション開発者はさらに多くの課題を克服する必要があります。

プッシュ通知は、モバイル アプリケーション向けのクラウド サービスで最も需要の高い機能の 1 つです。それは、プッシュ通知を機能させるために必要なインフラストラクチャが非常に複雑であることに加え、プッシュ通知の機能がアプリケーションの主要なビジネス ロジックと無関係であることが多いためです。オンデマンド プッシュ インフラストラクチャを構築する際に課題となるのは、次のような点です。

- **プラットフォーム依存。** さまざまなプラットフォームのデバイスに通知を送信するには、複数のインターフェイスに対応するコードをバックエンドに用意する必要があります。詳細な仕様が異なるだけでなく、通知の表示方法 (タイル、トースト、バッジなど) もプラットフォームに依存します。このため、バックエンド コードは複雑化し、保守が困難になります。

- **スケール。** このインフラストラクチャの拡張に関しては、次の 2 つの問題点があります。
	+ PNS のガイドラインに従って、アプリが起動するたびにデバイス トークンを更新する必要があります。そのため、デバイス トークンを最新の状態に維持するためだけに膨大なトラフィック (およびそれに伴うデータベース アクセス) が発生します。デバイスの数が増加すると (数百万台に達する可能性があります)、このインフラストラクチャの作成と保守にかかるコストが無視できないものになります。

	+ ほとんどの PNS は、複数のデバイスに対するブロードキャストをサポートしていません。そのため、数百万台のデバイスに対してブロードキャストを実行すると、PNS に対して数百万回の呼び出しを実行することになります。このように要求が増大する可能性があることは、重大な問題です。一般的に、アプリケーション開発者は合計遅延時間をできるだけ少なくしようとするためです (たとえば、最終的にメッセージを受信するデバイスが通知の送信後 30 分経ってもメッセージを受信しなければ、ほとんどの場合、プッシュ通知を使用する意味がなくなります)。
- **ルーティング。** PNS は、デバイスにメッセージを送信する機能を備えています。しかし、ほとんどのアプリケーションでは通知の対象がユーザーまたは特定のグループです (特定の顧客アカウントに割り当てられた全従業員など)。そのため、通知を適切なデバイスにルーティングするには、特定のグループにデバイス トークンを関連付けるためのレジストリをアプリケーションのバックエンドで維持する必要があります。この対策を行うことで、市場投入までの開発期間が長引き、アプリケーションの保守コストも増大します。

##Notification Hubs を使用する理由は?

Notification Hubs は、複雑さを排除します。プッシュ通知の数々の課題を管理する必要はありません。代わりに、通知ハブを使用できます。Notification Hubs はマルチプラットフォームに完全対応する、スケールアウトされたプッシュ通知インフラストラクチャを使用しており、アプリケーションのバックエンドで実行するプッシュ通知用コードを大幅に減らすことができます。Notification Hubs は、プッシュ インフラストラクチャのすべての機能を備えています。次の図に示すように、デバイス側の処理は PNS ハンドルを登録するだけであり、バックエンドによってプラットフォームに依存しないメッセージがユーザーまたは対象グループに送信されます。

![][1]


通知ハブはすぐに利用できるプッシュ通知インフラストラクチャであり、次のメリットがあります。

- **複数のプラットフォーム。**
	+  すべての主要なモバイル プラットフォームをサポートします。通知ハブからプッシュ通知を送信できるのは、Windows ストア アプリケーション、iOS アプリケーション、Android アプリケーション、および Windows Phone アプリケーションです。

	+  通知ハブは、サポート対象のすべてのプラットフォームに通知を送信する共通インターフェイスを備えています。プラットフォーム固有のプロトコルは必要ありません。アプリケーションのバックエンドから、通知をプラットフォームの独自形式で送信することも、プラットフォームに依存しない形式で送信することもできます。アプリケーションは、Notification Hubs とのみ通信します。

	+  デバイス ハンドル管理。Notification Hubs はハンドル レジストリと PNS からのフィードバックを管理します。

- **任意のバックエンドと連携**: クラウドまたはオンプレミスの .NET、PHP、Java、Node などと連携します。

- **スケール。** 通知ハブは再設計やシャーディングを行わなくても、数百万のデバイスに対応できます。


- **豊富な配信パターン**:

	- *ブロードキャスト*: 数百万台のデバイスへのブロードキャストをほぼ同時に単一の API 呼び出しで実行できます。

	- *ユニキャスト/マルチキャスト*: 個別のユーザーとデバイスを表すタグにプッシュします。または、独立したフォーム ファクター (タブレットとスマートフォン) などの広範囲なグループにプッシュします。

	- *セグメント化*: タグ式によって定義された複雑なセグメント (たとえば、ヤンキースをフォローしているニューヨークのデバイス) にプッシュします。

	各デバイスは、ハンドルを通知ハブに送信するときに、1 つ以上の_タグ_を指定することができます。詳細については、「[タグ]」を参照してください。タグは事前に定義したり、破棄したりする必要はありません。タグを使用して、通知を簡単にユーザーや対象グループに送信できます。タグにはアプリケーション独自の識別子 (ユーザー ID、グループ ID など) を含めることができるため、タグを使用することで、アプリケーションのバックエンドでデバイス ハンドルの格納と管理の処理を行う必要がなくなります。

- **パーソナル化**。各デバイスは、バックエンド コードに影響を与えずにデバイスごとのローカライゼーションと個人用設定を実現するために、1 つ以上のテンプレートを持つことができます。

- **セキュリティ**: 共有アクセス シークレット (SAS) またはフェデレーション認証。

- **リッチ テレメトリ**: ポータルでプログラム的に使用できます。


##App Service Mobile Apps との統合

Azure のサービス間でシームレスかつ同一のエクスペリエンスを実現するために、[App Service Mobile Apps] には Notification Hubs を使用したプッシュ通知のサポートが組み込まれています。[App Service Mobile Apps] は、拡張性が高くグローバルに使用可能な、企業の開発者やシステム インテグレーター向けのモバイル アプリ開発プラットフォームで、モバイル開発者に豊富な機能のセットを提供します。

Mobile Apps の開発者は、次のワークフローで Notification Hubs を使用します。

1. デバイスの PNS ハンドルを取得する
2. 便利な Mobile Apps クライアント SDK 登録 API を介して Notification Hubs でデバイスと[テンプレート]を登録する
    + Mobile Apps ではセキュリティの目的で登録時にすべてのタグを除去することに注意してください。タグをデバイスに関連付けるには、Notification Hubs をバックエンドから直接操作します。
3. Notification Hubs を使用してアプリのバックエンドから通知を送信する

この統合は、開発者にいくつかの便利な機能を提供します。
- **Mobile Apps クライアント SDK。** これらの複数プラットフォームの SDK は登録用のシンプルな API を提供し、モバイル アプリに自動的にリンクされている通知ハブと対話します。開発者は Notification Hubs の資格情報を掘り下げて追加のサービスを操作する必要はありません。
    + SDK は自動的に指定のデバイスを Mobile Apps の認証済みユーザー ID にタグ付けしてユーザー シナリオへのプッシュを有効にします。
    + SDK は自動的に Mobile Apps のインストール ID を Notification Hubs に登録する GUID として使用し、開発者が複数サービスの GUID を維持する手間を省きます。
- **インストール モデル。** Mobile Apps は Notification Hubs の最新のプッシュ モデルと簡単に連携し、プッシュ通知サービスと連携する JSON インストール内のデバイスに関連付けられたすべてのプッシュ プロパティを表します。
- **柔軟性。** 開発者は統合中でも常に Notification Hubs を直接操作することを選択できます。
- **[Azure ポータル]の統合されたエクスペリエンス。** 機能としてのプッシュが Mobile Apps に視覚的に表示され、開発者は Mobile Apps を通じて関連付けられた通知ハブを簡単に操作できます。



##次のステップ

Notification Hubs の詳細については、次のトピックを参照してください。

+ **[Notification Hubs の機能と特徴]**

+ **[Notification Hubs のチュートリアルとガイド]**

+ **Notification Hubs の使用チュートリアル** ([iOS], [Android]、[Windows ユニバーサル]、[Windows Phone]、[Kindle]、[Xamarin.iOS]、[Xamarin.Android])

プッシュ通知の関連する .NET マネージ API リファレンスについては、次を参照してください。

+ [Microsoft.WindowsAzure.Messaging.NotificationHub]
+ [Microsoft.ServiceBus.Notifications]


  [0]: ./media/notification-hubs-overview/registration-diagram.png
  [1]: ./media/notification-hubs-overview/notification-hub-diagram.png
  [Notification Hubs の機能と特徴]: http://azure.microsoft.com/services/notification-hubs
  [Notification Hubs のチュートリアルとガイド]: http://azure.microsoft.com/documentation/services/notification-hubs
  [iOS]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started
  [Android]: http://azure.microsoft.com/documentation/articles/notification-hubs-android-get-started
  [Windows ユニバーサル]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started
  [Windows Phone]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-phone-get-started
  [Kindle]: http://azure.microsoft.com/documentation/articles/notification-hubs-kindle-get-started
  [Xamarin.iOS]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-ios-get-started
  [Xamarin.Android]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-android-get-started
  [Microsoft.WindowsAzure.Messaging.NotificationHub]: http://msdn.microsoft.com/library/microsoft.windowsazure.messaging.notificationhub.aspx
  [Microsoft.ServiceBus.Notifications]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.aspx
  [App Service Mobile Apps]: https://azure.microsoft.com/ja-JP/documentation/articles/app-service-mobile-value-prop/
  [テンプレート]: notification-hubs-templates.md
  [Azure ポータル]: https://portal.azure.com
  [タグ]: (http://msdn.microsoft.com/library/azure/dn530749.aspx)

<!---HONumber=AcomDC_0309_2016-->