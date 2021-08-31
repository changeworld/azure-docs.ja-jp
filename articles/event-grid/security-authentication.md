---
title: イベンド ハンドラーへのイベント配信の認証 (Azure Event Grid)
description: この記事では、Azure Event Grid のイベント ハンドラーへの配信を認証するさまざまな方法について説明します。
ms.topic: conceptual
ms.date: 06/28/2021
ms.openlocfilehash: 01383809e6aab895ff4ed42763c57004a6ee02a8
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2021
ms.locfileid: "113003235"
---
# <a name="authenticate-event-delivery-to-event-handlers-azure-event-grid"></a>イベンド ハンドラーへのイベント配信の認証 (Azure Event Grid)
この記事では、イベント ハンドラーへのイベント配信の認証について説明します。 

## <a name="overview"></a>概要
Azure Event Grid ではさまざまな認証方法を利用してイベントをイベント ハンドラーに配信します。 `

| 認証方法 | サポートされているハンドラー | 説明  |
|--|--|--|
アクセス キー | <p>Event Hubs</p><p>Service Bus</p><p>ストレージ キュー</p><p>Relay ハイブリッド接続</p><p>Azure Functions</p><p>Storage BLOB (Deadletter)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</p> | アクセス キーは Event Grid サービス プリンシパルの資格情報を利用してフェッチされます。 Azure サブスクリプションで Event Grid リソース プロバイダーを登録すると、アクセス許可が Event Grid に付与されます。 |  
マネージド システム ID <br/>&<br/> ロールベースのアクセス制御 | <p>Event Hubs</p><p>Service Bus</p><p>ストレージ キュー</p><p>Storage BLOB (Deadletter)</p></li></ul> | トピックのマネージド システム ID を有効にし、それを宛先の該当するロールに追加します。 詳細については、「[イベント配信にシステム割り当て ID を使用する](#use-system-assigned-identities-for-event-delivery)」を参照してください。  |
|Azure AD で保護する Webhook によるベアラー トークン認証 | Webhook | 詳細については、「[Webhook エンドポイントへのイベント配信の認証](#authenticate-event-delivery-to-webhook-endpoints)」セクションを参照してください。 |
クエリ パラメーターとしてクライアント シークレット | Webhook | 詳細については、「[クエリ パラメーターとしてクライアント シークレットを使用する](#using-client-secret-as-a-query-parameter)」を参照してください。 |

## <a name="use-system-assigned-identities-for-event-delivery"></a>イベント配信にシステム割り当て ID を使用する
トピックまたはドメインに対してシステム割り当てのマネージド ID を有効にすることができ、その ID を使用して Service Bus のキューやトピック、イベント ハブ、ストレージ アカウントなどのサポートされている宛先にイベントを転送できます。

次に手順を示します。 

1. システム割り当て ID を持つトピックまたはドメインを作成するか、既存のトピックまたはドメインを更新して ID を有効にする。 詳細については、[システム トピックのマネージド ID を有効にする](enable-identity-system-topics.md)方法、または[カスタム トピックまたはドメインのマネージド ID を有効にする](enable-identity-custom-topics-domains.md)方法に関するページを参照してください。
1. 配信先 (Service Bus キューなど) の適切なロール (Service Bus データ送信者など) に ID を追加する。 詳細については、[ID に Event Grid の配信先へのアクセスを許可する](add-identity-roles.md)方法に関するページを参照してください。
1. イベント サブスクリプションを作成する際に ID の使用を有効にして、イベントを配信先に配信する。 詳細については、[ID を使用するイベント サブスクリプションを作成する](managed-service-identity.md)方法に関するページを参照してください。 

詳細な手順については、「[マネージド ID を使用したイベント配信](managed-service-identity.md)」を参照してください。


## <a name="authenticate-event-delivery-to-webhook-endpoints"></a>Webhook エンドポイントへのイベント配信の認証
以下のセクションでは、Webhook エンドポイントへのイベント配信を認証する方法について説明します。 使用する方法に関係なく、検証ハンドシェイク メカニズムを使用します。 詳細については、「[Webhook のイベント配信](webhook-event-delivery.md)」を参照してください。 


### <a name="using-azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD) を使用する
Azure AD を使用して、Event Grid からイベントを受信するために使用される Webhook エンドポイントをセキュリティで保護できます。 Azure AD アプリケーションを作成し、アプリケーションで Event Grid を承認するためのロールとサービス プリンシパルを作成する必要があります。さらに、Azure AD アプリケーションを使用するようにイベント サブスクリプションを構成する必要があります。 Event Grid で Azure Active Directory を構成する方法については、[こちら](secure-webhook-delivery.md)をご覧ください。

### <a name="using-client-secret-as-a-query-parameter"></a>クエリ パラメーターとしてクライアント シークレットを使用する
イベント サブスクリプションの作成の一環として指定したWebhook の送信先 URL にクエリ パラメーターを追加することで、Webhook エンドポイントをセキュリティで保護することもできます。 クエリ パラメーターのいずれかを、[アクセス トークン](https://en.wikipedia.org/wiki/Access_token)などのクライアント シークレットまたは共有シークレットとして設定します。 Event Grid サービスでは、Webhook へのすべてのイベント配信要求にすべてのクエリ パラメーターを含めます。 Webhook サービスはシークレットを取得して検証できます。 クライアント シークレットが更新された場合は、イベント サブスクリプションも更新する必要があります。 このシークレットのローテーション中の配信エラーを回避するために、イベント サブスクリプションを新しいシークレットで更新する前に、一定期間、Webhook で古いシークレットと新しいシークレットの両方を受け入れるようにします。 

クエリ パラメーターにはクライアント シークレットを含めることができるため、特別な注意を払って処理されます。 これらは暗号化されて格納され、サービス オペレーターがアクセスすることはできません。 これらは、サービス ログ/トレースの一部として記録されません。 イベント サブスクリプションのプロパティを取得するときに、送信先クエリ パラメーターは既定では返されません。 たとえば、[--include-full-endpoint-url](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_show) パラメーターは、Azure [CLI](/cli/azure) 内で使用されます。

Webhook へのイベント配信の詳細については、「[Webhook のイベント配信](webhook-event-delivery.md)」を参照してください。

> [!IMPORTANT]
> Azure Event Grid は、**HTTPS** Webhook エンドポイントのみをサポートします。 

## <a name="endpoint-validation-with-cloudevents-v10"></a>CloudEvents v1.0 を使用したエンドポイントの検証
Event Grid を既に使い慣れている場合、不正使用を防ぐためのエンドポイント検証ハンドシェイクをご存じかもしれません。 CloudEvents v1.0 では、**HTTP OPTIONS** メソッドを使用することで、独自の [不正使用防止のセマンティクス](webhook-event-delivery.md)が実装されます。 詳細については、[イベント配信用の HTTP 1.1 Web Hooks (バージョン 1.0)](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection) を参照してください。 出力に CloudEvents スキーマを使用すると、Event Grid では、Event Grid の検証イベント メカニズムではなく CloudEvents v1.0 の不正使用防止が使用されます。 詳細については、「[Event Grid に CloudEvents v1.0 スキーマを使用する](cloudevents-schema.md)」を参照してください。 


## <a name="next-steps"></a>次のステップ
トピックまたはドメインにイベントを発行するクライアントを認証する方法については、「[発行クライアントを認証する](security-authenticate-publishing-clients.md)」を参照してください。 
