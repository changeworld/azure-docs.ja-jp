---
title: インクルード ファイル
description: インクルード ファイル
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 03/08/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: a735131def481420d7dc5b86ab40d010b8e75ba8
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/21/2021
ms.locfileid: "112415708"
---
## <a name="trusted-microsoft-services"></a>信頼できる Microsoft サービス
**[信頼された Microsoft サービスがこのファイアウォールをバイパスすることを許可する]** 設定を有効にした場合、次のサービスに Service Bus リソースへのアクセス権が与えられます。

| 信頼できるサービス | サポートされる使用シナリオ | 
| --------------- | ------------------------- | 
| Azure Event Grid | Azure Event Grid が Service Bus 名前空間のキューまたはトピックにイベントを送信できるようにします。 次の手順も行う必要があります。 <ul><li>トピックまたはドメインに対してシステム割り当て ID を有効にする</li><li>Service Bus 名前空間で Azure Service Bus データ送信者ロールに ID を追加する</li><li>次に、システムによって割り当てられた ID を使用するために、Service Bus のキューまたはトピックをエンドポイントとして使用するイベント サブスクリプションを構成する</li></ul> <p>詳細については、「[マネージド ID を使用したイベント配信](../../event-grid/managed-service-identity.md)」を参照してください</p>|
| Azure API Management | <p>API Management サービスを使用すると、Service Bus 名前空間の Service Bus キューまたはトピックにメッセージを送信できます。</p><ul><li>[send-request ポリシー](../../api-management/api-management-sample-send-request.md)を使用して API が呼び出されたときに、Service Bus キューまたはトピックにメッセージを送信することで、カスタム ワークフローをトリガーできます。</li><li>API で、Service Bus キューまたはトピックをバックエンドとして扱うこともできます。 サンプル ポリシーについては、[マネージド ID を使用した Service Bus キューまたはトピックへのアクセスの認証](https://github.com/Azure/api-management-policy-snippets/blob/master/examples/Authenticate%20using%20Managed%20Identity%20to%20access%20Service%20Bus.xml)に関するページを参照してください。 次の手順も行う必要があります。<ol><li>API Management インスタンスでシステム割り当て ID を有効にします。 手順については、「[Azure API Management でマネージド ID を使用する](../../api-management/api-management-howto-use-managed-service-identity.md)」を参照してください。</li><li>Service Bus 名前空間で **Azure Service Bus データ送信者** ロールに ID を追加します。</li></ol></li></ul> | 