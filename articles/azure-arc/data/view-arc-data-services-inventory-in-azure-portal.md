---
title: Azure portal でインスタンスのインベントリを表示する
description: Azure portal でインスタンスのインベントリを表示する
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 2d68fbc1b2ae7ef92279b1e99bb77299da82b87e
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90931287"
---
# <a name="view-inventory-of-your-instances-in-the-azure-portal"></a>Azure portal でインスタンスのインベントリを表示する

[メトリック、ログ](upload-metrics-and-logs-to-azure-monitor.md)、または[使用状況](view-billing-data-in-azure.md)をアップロードした後は、Azure portal からインスタンスを Azure リソースとして表示できます。 これを行うには、特別な URL [https://portal.azure.com](https://portal.azure.com) に移動し、次のスクリーンショットのようにします

1. [すべてのサービス] に移動します
1. データベース インスタンスの種類を検索します
1. それをお気に入りに追加します
1. 左側のナビゲーションでインスタンスの種類をクリックします
1. Azure SQL または Azure PostgreSQL Hyperscale の他のリソースと同じビューにインスタンスが表示されます (詳細なビューを取得するにはフィルターを使用します)
