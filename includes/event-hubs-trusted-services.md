---
title: インクルード ファイル
description: インクルード ファイル
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 08/07/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 7cef6252a99430f0d62d8f976510f0a723badc1f
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2020
ms.locfileid: "91654415"
---
## <a name="trusted-microsoft-services"></a>信頼できる Microsoft サービス
**[Allow trusted Microsoft services to bypass this firewall]\(信頼された Microsoft サービスがこのファイアウォールをバイパスすることを許可する\)** 設定を有効にした場合、次のサービスに Event Hubs リソースへのアクセス権が与えられます。

| 信頼できるサービス | サポートされる使用シナリオ | 
| --------------- | ------------------------- | 
| Azure Event Grid | Event Hubs 名前空間のイベント ハブにイベントを送信することを Azure Event Grid に許可します。 次の手順も行う必要があります。 <ul><li>トピックまたはドメインに対してシステム割り当て ID を有効にする</li><li>Event Hubs 名前空間で Azure Event Hubs データ送信者ロールに ID を追加する</li><li>次に、システムによって割り当てられた ID を使用するために、イベント ハブをエンドポイントとして使用するイベント サブスクリプションを構成する</li></ul> <p>詳細については、「[マネージド ID を使用したイベント配信](../articles/event-grid/managed-service-identity.md)」を参照してください</p>|
| Azure Monitor (診断設定) | Event Hubs 名前空間のイベント ハブに診断情報を送信することを Azure Monitor に許可します。 |