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
ms.openlocfilehash: e084256d9c2043d4382ca180ef3178175b301367
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2020
ms.locfileid: "91745501"
---
次の制限は、Azure Event Grid の**トピック** (システム、カスタム、パートナーのトピック) に適用されます。 

| リソース | 制限 |
| --- | --- |
| Azure サブスクリプションあたりのカスタム トピック数 | 100 |
| トピックあたりのイベント サブスクリプション数 | 500 |
| カスタムまたはパートナーのトピックの発行率 (イングレス) | 5,000 イベント/秒または 1 MB/秒 (いずれか先に達した場合)<br/>システム トピックには適用されません。 |
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


