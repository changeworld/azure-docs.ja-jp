---
title: インクルード ファイル
description: インクルード ファイル
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 09/15/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: ab3994f44ed5a77ba228d9338d40599012597a95
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128910699"
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


