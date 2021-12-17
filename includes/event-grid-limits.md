---
title: インクルード ファイル
description: インクルード ファイル
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 09/07/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 0afa9814a7fb6b726d07fe5f1a5b2c863df72e2e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124778866"
---
次の制限は、Azure Event Grid の **トピック** (システム、カスタム、パートナーのトピック) に適用されます。 

> [!NOTE]
> この制限は、リージョンごとに存在します。 

| リソース | 制限 |
| --- | --- |
| Azure サブスクリプションあたりのカスタム トピック数 | 100 |
| トピックあたりのイベント サブスクリプション数 | 500 |
| カスタムまたはパートナーのトピックの発行率 (イングレス) | 5,000 イベント/秒または 5 MB/秒 (いずれか先に達した方) |
| イベント サイズ | 1 MB  |
| トピックあたりのプライベート エンドポイント接続数  | 64 | 
| トピックあたりの IP ファイアウォール規則数 | 16 | 

次の制限は、Azure Event Grid **ドメイン** に適用されます。 

| リソース | 制限 |
| --- | --- |
| イベント ドメインあたりのトピック数 | 100,000 |
| ドメイン内のトピックあたりのイベント サブスクリプション数 | 500 |
| ドメイン スコープ イベント サブスクリプション数 | 50 |
| イベント ドメインの発行率 (イングレス) | 5,000 イベント/秒または 5 MB/秒 (いずれか先に達した方) |
| Azure サブスクリプションあたりのイベント ドメイン数 | 100 |
| ドメインあたりのプライベート エンドポイント接続数 | 64 | 
| ドメインあたりの IP ファイアウォール規則数 | 16 | 


