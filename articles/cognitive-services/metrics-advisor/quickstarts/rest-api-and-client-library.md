---
title: Metrics Advisor クライアント ライブラリ REST API
titleSuffix: Azure Cognitive Services
description: このクイックスタートでは、お使いのアプリケーションを Azure Cognitive Services の Metrics Advisor API に接続する方法を説明します。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: quickstart
ms.date: 10/14/2020
ms.author: mbullwin
zone_pivot_groups: programming-languages-metrics-monitor
ms.openlocfilehash: 5b3df5f4b41b2beeec68b667863f6ca7715df47b
ms.sourcegitcommit: 7a7b6c7ac0aa9dac678c3dfd4b5bcbc45dc030ca
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2020
ms.locfileid: "93186939"
---
# <a name="quickstart-use-the-client-libraries-or-rest-apis-to-customize-your-solution"></a>クイックスタート: クライアント ライブラリまたは REST API を使用してソリューションをカスタマイズする

Metrics Advisor REST API またはクライアント ライブラリの使用を開始します。 以下の手順に従って、パッケージをインストールし、基本タスクのコード例を試してみましょう。

Metrics Advisor を使用すると、次のことができます。

* データ ソースからデータ フィードを追加する
* インジェストの状態を確認する
* 検出とアラートを構成する 
* 異常検出の結果を照会する
* 異常を診断する


::: zone pivot="programming-language-csharp"

[!INCLUDE [REST API quickstart](../includes/quickstarts/csharp.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [REST API quickstart](../includes/quickstarts/java.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [REST API quickstart](../includes/quickstarts/javascript.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [REST API quickstart](../includes/quickstarts/python.md)]

::: zone-end

::: zone pivot="programming-language-rest-api"

[!INCLUDE [REST API quickstart](../includes/quickstarts/rest-api.md)]

::: zone-end

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