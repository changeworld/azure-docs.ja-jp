---
title: インクルード ファイル
description: インクルード ファイル
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 03/04/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 2459cf6c5055dcde83dccf37addc160aeeaa64ad
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/21/2021
ms.locfileid: "112413561"
---
例を使えば、イベント ドメインを最も簡単に説明できます。 Contoso Construction Machinery という会社を経営しているとします。この会社はトラクター、掘削機器、その他の重機を製造しています。 経営の一部として、機器のメンテナンス、システムの正常性、契約の更新などについて顧客にリアルタイムの情報をプッシュで提供します。 この情報はすべて、アプリ、顧客のエンドポイント、顧客が設定したその他のインフラストラクチャを含むさまざまなエンドポイントに到達します。

イベント ドメインを使用することで、Contoso Construction Machinery を単一のイベント処理エンティティとしてモデル化することができます。 各顧客は、ドメイン内のトピックとして表されます。 認証と承認は Azure Active Directory を使用して処理されます。 各顧客はトピックをサブスクライブし、イベントを自分に配信させることができます。 イベント ドメインを介した管理アクセスにより、確実に自分のトピックにのみアクセスできるようになります。

また、すべての顧客イベントを発行できる、単一のエンドポイントが提供されます。 Event Grid では、各トピックにより、そのテナントをスコープとするイベントのみが認識されるようにします。

:::image type="content" source="./media/event-grid-domain-example-use-case/contoso-construction-example.png" alt-text="Contoso Construction の例" lightbox="./media/event-grid-domain-example-use-case/contoso-construction-example.png":::