---
title: Azure のデータ エクスプ ローラー クラスターの正常性を確認する
description: この記事では、Azure のデータ エクスプ ローラー クラスターが正常であるかを判断する手順について説明します。
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 0746247d2c912ba66e81b95f45b168e32b522130
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46988429"
---
# <a name="check-the-health-of-an-azure-data-explorer-cluster"></a>Azure のデータ エクスプ ローラー クラスターの正常性を確認する

Azure のデータ エクスプ ローラー クラスターの正常性に影響をする要素は CPU やメモリ、ディスク サブシステムなどいくつかあります｡ この記事では、クラスターの正常性を評価するために行えるいくつかの基本的な手順を示しています。

1. [https://dataexplorer.azure.com](https://dataexplorer.azure.com) にサインインします。

1. 左側のウィンドウでクラスターを選択して、次のコマンドを実行します。

    ```Kusto
    .show diagnostics
    | project IsHealthy
    ```
    出力が 1 なら正常です。0 の場合は問題があります。

1. [Azure ポータル](https://portal.azure.com)にサインインし、IoT Hub に移動します。

1. **監視** で **メトリクス**を選択し、次の図に示すように **Keep Alive** を選択します｡ 出力が 1 に近ければ、クラスターが正常であることを示します｡

    ![クラスターの Keep Alive メトリック](media/check-cluster-health/portal-metrics.png)

1. CPU や Memory Caching などの他のメトリクスを追加して､クラスターのリソース使用率を測定する

1. クラスターの正常性診断で問題が起きた場合は､[Azure portal](https://portal.azure.com) でサポート リクエストを開いてください｡