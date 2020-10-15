---
title: インクルード ファイル
description: インクルード ファイル
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 10/18/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: b884bc72381c98af77f2f49336f3dd5762c68734
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91859708"
---
次の制限は、Azure Event Grid の**トピック** (システム、カスタム、パートナーのトピック) に適用されます。 

| リソース | 制限 |
| --- | --- |
| Azure サブスクリプションあたりのカスタム トピック数 | 100 |
| トピックあたりのイベント サブスクリプション数 | 500 |
| カスタムまたはパートナーのトピックの発行率 (イングレス) | 5,000 イベント/秒または 1 MB/秒 (いずれか先に達した場合) |
| イベント サイズ | 1 MB  |
| トピックあたりのプライベート エンドポイント接続数  | 64 | 
| トピックあたりの IP ファイアウォール規則数 | 16 | 

次の制限は、Azure Event Grid **ドメイン**に適用されます。 

| リソース | 制限 |
| --- | --- |
| イベント ドメインあたりのトピック数 | 100,000 |
| ドメイン内のトピックあたりのイベント サブスクリプション数 | 500 |
| ドメイン スコープ イベント サブスクリプション数 | 50 |
| イベント ドメインの発行率 (イングレス) | 5,000 イベント/秒または 1 MB/秒 (いずれか先に達した場合) |
| Azure サブスクリプションあたりのイベント ドメイン数 | 100 |
| ドメインあたりのプライベート エンドポイント接続数 | 64 | 
| ドメインあたりの IP ファイアウォール規則数 | 16 | 


