---
title: インクルード ファイル
description: インクルード ファイル
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 09/29/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: c6c6a92848b91f32dec612abe7c16ab73b71cccd
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129300397"
---
## <a name="trusted-microsoft-services"></a>信頼できる Microsoft サービス
**[信頼された Microsoft サービスがこのファイアウォールをバイパスすることを許可する]** 設定を有効にした場合、次のサービスに Service Bus リソースへのアクセス権が与えられます。

| 信頼できるサービス | サポートされる使用シナリオ | 
| --------------- | ------------------------- | 
| Azure Event Grid | Azure Event Grid が Service Bus 名前空間のキューまたはトピックにイベントを送信できるようにします。 次の手順も行う必要があります。 <ul><li>トピックまたはドメインに対してシステム割り当て ID を有効にする</li><li>Service Bus 名前空間で Azure Service Bus データ送信者ロールに ID を追加する</li><li>次に、システムによって割り当てられた ID を使用するために、Service Bus のキューまたはトピックをエンドポイントとして使用するイベント サブスクリプションを構成する</li></ul> <p>詳細については、「[マネージド ID を使用したイベント配信](../../event-grid/managed-service-identity.md)」を参照してください</p>|
| Azure API Management | <p>API Management サービスを使用すると、Service Bus 名前空間の Service Bus キューまたはトピックにメッセージを送信できます。</p><ul><li>[send-request ポリシー](../../api-management/api-management-sample-send-request.md)を使用して API が呼び出されたときに、Service Bus キューまたはトピックにメッセージを送信することで、カスタム ワークフローをトリガーできます。</li><li>API で、Service Bus キューまたはトピックをバックエンドとして扱うこともできます。 サンプル ポリシーについては、[マネージド ID を使用した Service Bus キューまたはトピックへのアクセスの認証](https://github.com/Azure/api-management-policy-snippets/blob/master/examples/Authenticate%20using%20Managed%20Identity%20to%20access%20Service%20Bus.xml)に関するページを参照してください。 次の手順も行う必要があります。<ol><li>API Management インスタンスでシステム割り当て ID を有効にします。 手順については、「[Azure API Management でマネージド ID を使用する](../../api-management/api-management-howto-use-managed-service-identity.md)」を参照してください。</li><li>Service Bus 名前空間で **Azure Service Bus データ送信者** ロールに ID を追加します。</li></ol></li></ul> | 
| Azure IoT Central | <p>IoT Central を使用すると、Service Bus 名前空間の Service Bus キューまたはトピックにデータをエクスポートできます。 次の手順も行う必要があります。</p><ul><li>IoT Central アプリケーションのシステム割り当て ID を有効にします</li><li>Service Bus 名前空間で **Azure Service Bus データ送信者** ロールに ID を追加します。 </li><li>次に、[IoT Central アプリケーションの Service Bus エクスポート先](../../iot-central/core/howto-export-data.md)を、ID ベースの認証を使用するように構成します。 </li>
