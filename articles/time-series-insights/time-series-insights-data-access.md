---
title: Azure Time Series Insights にアクセスして管理するためのセキュリティの構成 | Microsoft Docs
description: この記事では、管理アクセス ポリシーとデータ アクセス ポリシーとしてセキュリティとアクセス許可を構成し、Azure Time Series Insights を保護する方法を説明します。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/15/2017
ms.openlocfilehash: 7cb5dc5b170103f98d56abc920f36dd85f855961
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/19/2018
ms.locfileid: "46364821"
---
# <a name="grant-data-access-to-a-time-series-insights-environment-using-azure-portal"></a>Azure Portal を使用して Time Series Insights 環境にデータ アクセスを許可する

Time Series Insights 環境には、2 種類の独立したアクセス ポリシーがあります。

* 管理アクセス ポリシー
* データ アクセス ポリシー

どちらのポリシーでも、特定の環境に対するさまざまなアクセス許可が Azure Active Directory プリンシパル (ユーザーおよびアプリ) に付与されます。 このプリンシパル (ユーザーおよびアプリ) は、その環境が含まれているサブスクリプションに関連付けられたアクティブなディレクトリ ("Azure テナント" として知られる) に属している必要があります。

管理アクセス ポリシーは、次のような、環境の構成に関連したアクセス許可を付与します。
*   環境、イベント ソース、参照データ セットの作成と削除。
*   データ アクセス ポリシーの管理。

データ アクセス ポリシーは、データ クエリの発行、環境内での参照データの操作、環境に関連付けられた保存クエリとパースペクティブの共有を実行するためのアクセス許可を付与します。

この 2 種類のポリシーにより、環境の管理へのアクセスと環境内のデータへのアクセスを明確に分離できます。 たとえば、環境の所有者または作成者がデータ アクセスから除外されるように環境を設定することが可能です。 また、環境からのデータの読み取りを許可されているユーザーとサービスに環境の構成へのアクセスを許可しないことも可能です。

[!INCLUDE [iot-tsi-data-access](../../includes/iot-tsi-data-access.md)]

## <a name="next-steps"></a>次の手順

* [Azure Time Series Insights 環境にイベント ハブ イベント ソースを追加する方法](time-series-insights-how-to-add-an-event-source-eventhub.md)について説明します。
* イベント ソースに[イベントを送信](time-series-insights-send-events.md)します。
* [Time Series Insights エクスプローラー](https://insights.timeseries.azure.com)で環境を表示します。
