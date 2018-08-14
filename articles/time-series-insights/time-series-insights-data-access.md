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
ms.openlocfilehash: 97c9480d6f2b75d83252bfb6410d7b5f946757ef
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39630655"
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

## <a name="grant-data-access"></a>データ アクセスの許可
次の手順に従って、ユーザー プリンシパルにデータ アクセスを許可します。

1. [Azure Portal](https://portal.azure.com) にサインインします。

2. Time Series Insights 環境を検索します。 **[検索]** ボックスに「**Time Series**」と入力します。 検索結果の **[Time Series Environment]\(Time Series 環境\)** を選択します。 

3. リストから Time Series Insights 環境を選択します。
   
4. **[データ アクセス ポリシー]** を選択し、**[+ 追加]** をクリックします。
  ![Time Series Insights ソースの管理 - 環境](media/data-access/getstarted-grant-data-access1.png)

5. **[ユーザーの選択]** を選択します。  ユーザー名または電子メール アドレスを検索して追加するユーザーを探します。 **[選択]** をクリックして選択内容を確定します。 

   ![Time Series Insights ソースの管理 - 追加](media/data-access/getstarted-grant-data-access2.png)

6. **[ロールの選択]** を選択します。 ユーザーに割り当てる適切なアクセス ロールを選択します。
   - ユーザーに対して、参照データの変更に加え、保存クエリとパースペクティブを環境の他のユーザーと共有することを許可する場合は **[共同作成者]** を選択します。 
   - それ以外の場合は **[閲覧者]** を選択し、環境内のデータへのクエリ実行と環境内の個人用 (共有されない) クエリの保存をユーザーに許可します。

   **[OK]** を選び、ロールの選択を確定します。

   ![Time Series Insights ソースの管理 - ユーザーの選択](media/data-access/getstarted-grant-data-access3.png)

8. **[ユーザー ロールの選択]** ページで **[OK]** を選択します。

   ![Time Series Insights ソースの管理 - ロールの選択](media/data-access/getstarted-grant-data-access4.png)

9. **[データ アクセス ポリシー]** ページには、各ユーザーのユーザーとロールが一覧表示されます。

   ![Time Series Insights ソースの管理 - 結果](media/data-access/getstarted-grant-data-access5.png)

## <a name="next-steps"></a>次の手順
* [Azure Time Series Insights 環境にイベント ハブ イベント ソースを追加する方法](time-series-insights-how-to-add-an-event-source-eventhub.md)について説明します。
* イベント ソースに[イベントを送信](time-series-insights-send-events.md)します。
* [Time Series Insights エクスプローラー](https://insights.timeseries.azure.com)で環境を表示します。
