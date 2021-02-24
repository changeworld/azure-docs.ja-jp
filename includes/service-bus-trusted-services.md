---
title: インクルード ファイル
description: インクルード ファイル
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/05/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: bb6f2281cc115904db6babcf31f1af387b254d48
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94426570"
---
## <a name="trusted-microsoft-services"></a>信頼できる Microsoft サービス
**[信頼された Microsoft サービスがこのファイアウォールをバイパスすることを許可する]** 設定を有効にした場合、次のサービスに Service Bus リソースへのアクセス権が与えられます。

| 信頼できるサービス | サポートされる使用シナリオ | 
| --------------- | ------------------------- | 
| Azure Event Grid | Azure Event Grid が Service Bus 名前空間のキューまたはトピックにイベントを送信できるようにします。 次の手順も行う必要があります。 <ul><li>トピックまたはドメインに対してシステム割り当て ID を有効にする</li><li>Service Bus 名前空間で Azure Service Bus データ送信者ロールに ID を追加する</li><li>次に、システムによって割り当てられた ID を使用するために、Service Bus のキューまたはトピックをエンドポイントとして使用するイベント サブスクリプションを構成する</li></ul> <p>詳細については、「[マネージド ID を使用したイベント配信](../articles/event-grid/managed-service-identity.md)」を参照してください</p>|
