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
ms.openlocfilehash: 3c7299ff211035f81cc08e9f191641c780ad02c4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "91826649"
---
# <a name="view-inventory-of-your-instances-in-the-azure-portal"></a>Azure portal でインスタンスのインベントリを表示する

[メトリック、ログ](upload-metrics-and-logs-to-azure-monitor.md)、または[使用状況](view-billing-data-in-azure.md)をアップロードした後は、Azure portal からインスタンスを Azure リソースとして表示できます。 Azure portal でリソースを表示するには、専用 URL ([https://portal.azure.com](https://portal.azure.com)) にアクセスして、次の手順を実行します。

1. **[すべてのサービス]** に移動します。
1. データベース インスタンスの種類を検索します。
1. その種類をお気に入りに追加します。
1. 左側のメニューで、インスタンスの種類を選択します。
1. Azure SQL または Azure PostgreSQL Hyperscale の他のリソースと同じビューにインスタンスが表示されます (詳細なビューを表示するにはフィルターを使用します)。
