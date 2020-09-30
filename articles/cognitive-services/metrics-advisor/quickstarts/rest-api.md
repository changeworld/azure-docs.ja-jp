---
title: クイック スタート:Metrics Advisor REST API
titleSuffix: Azure Cognitive Services
description: このクイックスタートでは、お使いのアプリケーションを Azure Cognitive Services の Metrics Advisor API に接続する方法を説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: quickstart
ms.date: 09/23/2020
ms.author: aahi
ms.openlocfilehash: 2376ea01190d619a5d1b6368a17e55c09806a406
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2020
ms.locfileid: "91253745"
---
# <a name="quickstart-use-the-rest-apis-to-customize-your-solution"></a>クイック スタート:REST API を使用してソリューションをカスタマイズする

Metrics Advisor REST API の基本的な使い方について説明します。 以下の手順に従って、パッケージをインストールし、基本タスクのコード例を試してみましょう。

Metrics Advisor を使用すると、次のことができます。

* データ ソースからデータ フィードを追加する
* インジェストの状態を確認する
* 検出とアラートを構成する 
* 異常検出の結果を照会する
* 異常を診断する

[!INCLUDE [REST API quickstart](../includes/quickstarts/rest-api.md)]

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Cognitive Services サブスクリプションをクリーンアップして削除したい場合は、リソースまたはリソース グループを削除することができます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。

* [ポータル](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>次のステップ

- [Web ポータルを使用する](web-portal.md)
- [データ フィードをオンボードする](../how-tos/onboard-your-data.md)
    - [データ フィードを管理する](../how-tos/manage-data-feeds.md)
    - [さまざまなデータ ソースの構成](../data-feeds-from-different-sources.md)
- [メトリックを構成して検出構成を微調整する](../how-tos/configure-metrics.md)
- [フィードバックを使用して異常検出を調整する](../how-tos/anomaly-feedback.md)