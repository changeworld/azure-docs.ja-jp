<properties 
   pageTitle="Service Bus による Shared Access Signature 認証 | Microsoft Azure"
   description="Service Bus による SAS 認証について詳しく説明します。"
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="" />
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/09/2016"
   ms.author="sethm" />

# Service Bus による Shared Access Signature 認証

[Shared Access Signature (SAS)](service-bus-sas-overview.md) 認証により、アプリケーションは、名前空間、または特定の権限が関連付けられているメッセージ エンティティ (キューまたはトピック) で構成されたアクセス キーを使用して Service Bus に対して認証できます。次に、このキーを使用して、クライアントが後で Service Bus に対する認証に使用できる SAS トークンを生成できます。

Service Bus の SAS 認証サポートは、Azure SDK バージョン 2.0 以降に含まれています。Service Bus 認証の詳細については、「[Service Bus の認証と承認](service-bus-authentication-and-authorization.md)」を参照してください。

## 概念

Service Bus の SAS 認証には、Service Bus リソースに対する関連した権限を使用した暗号化キーの構成が伴います。クライアントは SAS トークンを渡して Service Bus のリソースへのアクセスを要求します。このトークンは、アクセスされるリソース URI と、構成されたキーで署名された有効期限で構成されます。

Shared Access Signature の承認規則は、Service Bus の[リレー](service-bus-fundamentals-hybrid-solutions.md#relays)、[キュー](service-bus-fundamentals-hybrid-solutions.md#queues)、[トピック](service-bus-fundamentals-hybrid-solutions.md#topics)、[Event Hubs](https://azure.microsoft.com/documentation/services/event-hubs/) で構成できます。

SAS 認証では、次の要素が使用されます。

- [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx): Base64 形式の 256 ビット プライマリ暗号化キー、オプションのセカンダリ キー、キー名と関連付けられている権限 (*Listen*、*Send*、*Manage* 権限のコレクション)。

- [SharedAccessSignature](https://msdn.microsoft.com/library/azure/microsoft.servicebus.sharedaccesssignaturetokenprovider.sharedaccesssignature.aspx) トークン: リソース文字列の HMAC-SHA256 を使用して生成されます。この文字列は、暗号化キーによって、アクセスされるリソースの URI と、有効期限で構成されます。以下のセクションで説明する署名とその他の要素は、[SharedAccessSignature](https://msdn.microsoft.com/library/azure/microsoft.servicebus.sharedaccesssignaturetokenprovider.sharedaccesssignature.aspx) トークンを構成するために文字列にフォーマットされています。

## Shared Access Signature 認証の構成

[SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) 規則は、Service Bus の名前空間、キュー、トピックに構成できます。Service Bus サブスクリプションに対する [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) の構成は現在サポートされていませんが、名前空間またはトピックに構成されている規則を使用して、サブスクリプションへのアクセスをセキュリティで保護できます。この手順を示す作業サンプルについては、[Service Bus サブスクリプションでの Shared Access Signature (SAS) 認証の使用](http://code.msdn.microsoft.com/windowsazure/Using-Shared-Access-e605b37c)に関するサンプルを参照してください。

Service Bus の名前空間、キュー、トピックでは、このような規則を最大 12 個構成できます。Service Bus 名前空間に構成されている規則は、その名前空間内のすべてのエンティティに適用されます。

![SAS](./media/service-bus-shared-access-signature-authentication/IC676272.gif)

この図では、*manageRuleNS*、*sendRuleNS*、*listenRuleNS* という承認規則がキュー Q1 とトピック T1 の両方に適用されると同時に、*listenRuleQ* と *sendRuleQ* はキュー Q1 のみに適用され、*sendRuleT* はトピック T1 のみに適用されています。

[SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) の主なパラメーターは次のとおりです。

|パラメーター|説明|
|---|---|
|*KeyName*|承認規則を説明する文字列。|
|*PrimaryKey*|SAS トークンの署名と検証用の Base64 でエンコードされた 256 ビットのプライマリ キー。|
|*SecondaryKey*|SAS トークンの署名と検証用の Base64 でエンコードされた 256 ビットのセカンダリ キー。|
|*AccessRights*|承認規則によって付与されたアクセス権限の一覧。これらの権限には、Listen、Send、Manage 権限の任意のコレクションを指定できます。|

Service Bus 名前空間がプロビジョニングされると、**RootManageSharedAccessKey** に設定された [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) と [KeyName](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.keyname.aspx) が既定で作成されます。また、既定の 2 つの [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) オブジェクトは通知ハブ用に構成されます。1 つは Listen、Send、Manage 権限で構成され、もう 1 つは Listen 権限のみで構成されます。

## 共有アクセス承認規則のキーの再生成と取り消し

[SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) オブジェクトで使用されるキーは、定期的に再生成することをお勧めします。一般的に、アプリケーションでは、SAS トークンの生成に [PrimaryKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.primarykey.aspx) を使用する必要があります。キーを再生成するときには、[SecondaryKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.secondarykey.aspx) を古いプライマリ キーに置き換え、新しいキーを新しいプライマリ キーとして生成する必要があります。これにより、古いプライマリ キーで発行され、まだ期限が切れていないトークンを引き続き承認に使用できます。

キーが侵害され、キーを取り消す必要がある場合は、[SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) の [PrimaryKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.primarykey.aspx) と [SecondaryKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.secondarykey.aspx) の両方を再生成し、それらを新しいキーに置き換えることができます。この手順により、古いキーで署名されたすべてのトークンが無効になります。

## Shared Access Signature トークンの生成

共有アクセス承認規則で指定された署名キーにアクセスできるクライアントは、SAS トークンを生成できます。これは次のような形式になります。

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

SAS トークンの**署名**の計算には、承認規則の [PrimaryKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.primarykey.aspx) プロパティを使用した署名文字列の HMAC-SHA256 ハッシュを使用します。署名文字列は、リソース URI と有効期限で構成され、次のような形式になります。

```
StringToSign = <resourceURI> + "\n" + expiry;
```

この操作には、エンコード済みのリソース URI を使用する必要があることに注意してください。リソース URI とは、アクセスが要求される Service Bus リソースの完全な URI です。たとえば、`http://<namespace>.servicebus.windows.net/<entityPath>` または `sb://<namespace>.servicebus.windows.net/<entityPath>` (つまり `http://contoso.servicebus.windows.net/contosoTopics/T1/Subscriptions/S3`) です。

有効期限は 1970 年 1 月 1 日の 00 時 00 分 00 秒 UTC からのエポック秒で表されます。

署名に使用される共有アクセス承認規則は、この URI、またはその階層の親のいずれかで指定したエンティティに構成する必要があります。たとえば、前の例では、`http://contoso.servicebus.windows.net/contosoTopics/T1` または `http://contoso.servicebus.windows.net` となります。

SAS トークンは、署名文字列で使用される `<resourceURI>` の下のすべてのリソースで有効です。

SAS トークン内の [KeyName](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.keyname.aspx) は、トークンの生成に使用される共有アクセス承認規則の **keyName** を表します。

*URL-encoded-resourceURI* は、署名の計算中に署名文字列で使用する URI と同じものにする必要があります。[パーセントエンコード](https://msdn.microsoft.com/library/4fkewx0t.aspx)にする必要があります。

## Service Bus による Shared Access Signature 認証の使用方法

以下のシナリオには、承認規則の構成、SAS トークンの生成、クライアントの承認などが含まれます。

構成を説明して SAS 承認を使用する、Service Bus アプリケーションの完全に動作するサンプルについては、[Service Bus による Shared Access Signature 認証](http://code.msdn.microsoft.com/Shared-Access-Signature-0a88adf8)に関するページを参照してください。Service Bus サブスクリプションをセキュリティで保護するために名前空間またはトピックに構成された SAS 承認規則の使用を示す関連のサンプルについては、[Service Bus サブスクリプションでの Shared Access Signature (SAS) 認証の使用](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c)に関するページを参照してください。

## 名前空間の共有アクセス承認規則へのアクセス

Service Bus 名前空間のルートに対する操作では、証明書の認証が必要です。Azure サブスクリプションの管理証明書をアップロードする必要があります。管理証明書をアップロードするには、[Azure クラシック ポータル][]の左側のウィンドウにある **[設定]** をクリックします。Azure 管理証明書の詳細については、「[Azure 証明書の概要](../cloud-services/cloud-services-certs-create.md#what-are-management-certificates)」を参照してください。

Service Bus 名前空間の共有アクセス承認規則にアクセスするためのエンドポイントは次のようになります。

```
https://management.core.windows.net/{subscriptionId}/services/ServiceBus/namespaces/{namespace}/AuthorizationRules/
```

Service Bus 名前空間に [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) オブジェクトを作成するには、JSON または XML としてシリアル化された規則情報を使用して、このエンドポイントに対して POST 操作を実行します。次に例を示します。

```
// Base address for accessing authorization rules on a namespace
string baseAddress = @"https://management.core.windows.net/<subscriptionId>/services/ServiceBus/namespaces/<namespace>/AuthorizationRules/";

// Configure authorization rule with base64-encoded 256-bit key and Send rights
var sendRule = new SharedAccessAuthorizationRule("contosoSendAll",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Send });

// Operations on the Service Bus namespace root require certificate authentication.
WebRequestHandler handler = new WebRequestHandler
{
    ClientCertificateOptions = ClientCertificateOption.Manual
};
// Access the management certificate by subject name
handler.ClientCertificates.Add(GetCertificate(<certificateSN>));

HttpClient httpClient = new HttpClient(handler)
{
    BaseAddress = new Uri(baseAddress)
};
httpClient.DefaultRequestHeaders.Accept.Add(
    new MediaTypeWithQualityHeaderValue("application/json"));
httpClient.DefaultRequestHeaders.Add("x-ms-version", "2015-01-01");

// Execute a POST operation on the baseAddress above to create an auth rule
var postResult = httpClient.PostAsJsonAsync("", sendRule).Result;
```

同様に、名前空間に構成された承認規則を読み取るには、エンドポイントに対して GET 操作を使用します。

特定の承認規則を更新または削除するには、次のエンドポイントを使用します。

```
https://management.core.windows.net/{subscriptionId}/services/ServiceBus/namespaces/{namespace}/AuthorizationRules/{KeyName}
```

## エンティティの共有アクセス承認規則へのアクセス

Service Bus のキューまたはトピックに構成された [Microsoft.ServiceBus.Messaging.SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) オブジェクトには、対応する [QueueDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx)、[TopicDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.aspx)、[NotificationHubDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.notifications.notificationhubdescription.aspx) オブジェクト内の [AuthorizationRules](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.authorizationrules.aspx) コレクションを介してアクセスできます。

次のコードでは、キューの承認規則を追加する方法を示します。

```
// Create an instance of NamespaceManager for the operation
NamespaceManager nsm = NamespaceManager.CreateFromConnectionString( 
    <connectionString> );
QueueDescription qd = new QueueDescription( <qPath> );

// Create a rule with send rights with keyName as "contosoQSendKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoSendKey", 
    SharedAccessAuthorizationRule.GenerateRandomKey(), 
    new[] { AccessRights.Send }));

// Create a rule with listen rights with keyName as "contosoQListenKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQListenKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Listen }));

// Create a rule with manage rights with keyName as "contosoQManageKey"
// and add it to the queue description.
// A rule with manage rights must also have send and receive rights.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQManageKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] {AccessRights.Manage, AccessRights.Listen, AccessRights.Send }));

// Create the queue.
nsm.CreateQueue(qd);
```

## Shared Access Signature 承認の使用

Service Bus .NET ライブラリと Azure .NET SDK を使用するアプリケーションでは、[SharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.sharedaccesssignaturetokenprovider.aspx) クラスを介して SAS 承認を使用できます。次のコードでは、Service Bus キューにメッセージを送信するトークン プロバイダーの使用を示しています。

```
Uri runtimeUri = ServiceBusEnvironment.CreateServiceUri("sb", 
    <yourServiceNamespace>, string.Empty);
MessagingFactory mf = MessagingFactory.Create(runtimeUri, 
    TokenProvider.CreateSharedAccessSignatureTokenProvider(keyName, key));
QueueClient sendClient = mf.CreateQueueClient(qPath);

//Sending hello message to queue.
BrokeredMessage helloMessage = new BrokeredMessage("Hello, Service Bus!");
helloMessage.MessageId = "SAS-Sample-Message";
sendClient.Send(helloMessage);
```

アプリケーションは、接続文字列を受け入れるメソッドで SAS 接続文字列を使用することで、認証に SAS を使用することもできます。

Service Bus リレーで SAS 承認を使用するには、Service Bus 名前空間に構成されている SAS キーを使用できます。名前空間 ([RelayDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.relaydescription.aspx) を指定した [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx)) のオブジェクトにリレーを明示的に作成する場合は、そのリレーに SAS 規則を設定するだけです。Service Bus サブスクリプションで SAS 承認を使用するには、Service Bus 名前空間またはトピックに構成されている SAS キーを使用できます。

## Service Bus の操作に必要な権限

次の表に、Service Bus のリソースでのさまざまな操作に必要となるアクセス権を示します。

|操作|必要な要求|要求のスコープ|
|---|---|---|
|**名前空間**|||
|名前空間での承認規則を構成する|管理|任意の名前空間アドレス|
|**サービス レジストリ**|||
|プライベート ポリシーを列挙する|管理|任意の名前空間アドレス|
|リレー|||
|名前空間でリッスンを開始する|リッスン|任意の名前空間アドレス|
|名前空間のリスナーにメッセージを送信する|送信|任意の名前空間アドレス|
|**キュー**|||
|キューを作成する|管理|任意の名前空間アドレス|
|キューを削除する|管理|任意の有効なキュー アドレス|
|キューを列挙する|管理|/$Resources/Queues|
|キューの説明を取得する|管理または送信|任意の有効なキュー アドレス|
|キューの承認規則を構成する|管理|任意の有効なキュー アドレス|
|キューに送信する|送信|任意の有効なキュー アドレス|
|キューからメッセージを受信する|リッスン|任意の有効なキュー アドレス|
|ピーク ロック モードでメッセージを受信した後にそのメッセージを破棄または終了する|リッスン|任意の有効なキュー アドレス|
|後で取得するためにメッセージを保留する|リッスン|任意の有効なキュー アドレス|
|メッセージを配信不能にする|リッスン|任意の有効なキュー アドレス|
|メッセージのキュー セッションに関連付けられた状態を取得する|リッスン|任意の有効なキュー アドレス|
|メッセージのキュー セッションに関連付けられた状態を設定する|リッスン|任意の有効なキュー アドレス|
|**トピック**|||
|トピックを作成する|管理|任意の名前空間アドレス|
|トピックを削除する|管理|任意の有効なトピック アドレス|
|トピックを列挙する|管理|/$Resources/Topics|
|トピックの説明を取得する|管理または送信|任意の有効なトピック アドレス|
|トピックの承認規則を構成する|管理|任意の有効なトピック アドレス|
|トピックに送信する|送信|任意の有効なトピック アドレス|
|**サブスクリプション**|||
|サブスクリプションを作成する|管理|任意の名前空間アドレス|
|サブスクリプションを削除する|管理|../myTopic/Subscriptions/mySubscription|
|サブスクリプションを列挙する|管理|../myTopic/Subscriptions|
|サブスクリプションの説明を取得する|管理またはリッスン|../myTopic/Subscriptions/mySubscription|
|ピーク ロック モードでメッセージを受信した後にそのメッセージを破棄または終了する|リッスン|../myTopic/Subscriptions/mySubscription|
|後で取得するためにメッセージを保留する|リッスン|../myTopic/Subscriptions/mySubscription|
|メッセージを配信不能にする|リッスン|../myTopic/Subscriptions/mySubscription|
|トピック セッションに関連付けられた状態を取得する|リッスン|../myTopic/Subscriptions/mySubscription|
|トピック セッションに関連付けられた状態を設定する|リッスン|../myTopic/Subscriptions/mySubscription|
|**ルール**|||
|規則を作成する|管理|../myTopic/Subscriptions/mySubscription|
|規則を削除する|管理|../myTopic/Subscriptions/mySubscription|
|規則を列挙する|管理またはリッスン|../myTopic/Subscriptions/mySubscription/Rules|
|**Notification Hubs**|||
|通知ハブを作成する|管理|任意の名前空間アドレス|
|アクティブなデバイスの登録を作成または更新する|リッスンまたは管理|../notificationHub/tags/{tag}/registrations|
|PNS 情報を更新する|リッスンまたは管理|../notificationHub/tags/{tag}/registrations/updatepnshandle|
|通知ハブに送信する|送信|../notificationHub/messages|

## 次のステップ

Service Bus における SAS の概要については、「[Shared Access Signature](service-bus-sas-overview.md)」を参照してください。

Service Bus 認証の背景の詳細については、「[Service Bus の認証と承認](service-bus-authentication-and-authorization.md)」を参照してください。

[Azure クラシック ポータル]: http://manage.windowsazure.com

<!---HONumber=AcomDC_0316_2016-->