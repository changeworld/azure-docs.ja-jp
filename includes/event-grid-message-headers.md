---
title: インクルード ファイル
description: インクルード ファイル
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 11/19/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: c37e2c28eb6aee9edf4bdd97066ce5f15e7447c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96005634"
---
## <a name="message-headers"></a>メッセージ ヘッダー
これらは、メッセージ ヘッダーで受け取るプロパティです。

| プロパティ名 | 説明 |
| ------------- | ----------- | 
| aeg-subscription-name | イベント サブスクリプションの名前。 |
| aeg-delivery-count | イベントに対して行われた試行の回数。 |
| aeg-event-type | <p>イベントの種類。</p><p>次のいずれかの値を指定できます。</p><ul><li>SubscriptionValidation</li><li>Notification</li><li>SubscriptionDeletion</li></ul> | 
| aeg-metadata-version | <p>イベントのメタデータ バージョン。<p> **Event Grid のイベント スキーマ** の場合、このプロパティはメタデータのバージョンを表し、**クラウド イベント スキーマ** の場合は **仕様のバージョン** を表します。 </p>|
| aeg-data-version | <p>イベントのデータ バージョン。</p><p>**Event Grid のイベント スキーマ** の場合、このプロパティはデータのバージョンを表し、**クラウド イベント スキーマ** の場合は適用されません。</p> |
| aeg-output-event-id | Event Grid イベントの ID。 |


