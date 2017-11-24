---
title: "Azure Time Series Insights 環境に参照データ セットを追加する方法 | Microsoft Docs"
description: "この記事では、Azure Time Series Insights 環境に参照データ セットを追加する方法について説明します。 参照データは、ソース データに結合することにより値を増幅するのに役立ちます。"
services: time-series-insights
ms.service: time-series-insights
author: venkatgct
ms.author: venkatja
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: article
ms.date: 11/15/2017
ms.openlocfilehash: 7cdcefbd0daec3b7bab59680afa1470624583c74
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2017
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-azure-portal"></a>Azure Portal を使用して Time Series Insights 環境の参照データ セットを作成する

この記事では、Azure Time Series Insights 環境に参照データ セットを追加する方法について説明します。 参照データは、ソース データに結合することにより値を増幅するのに役立ちます。

参照データ セットは、イベント ソースからのイベントによって増幅される項目の集まりです。 イベント ソースから受信したイベントは、Time Series Insights のイングレス エンジンによって、指定した参照データ セット内の項目と結合されます。 こうして増幅されたイベントをクエリで利用することができます。 この結合操作は、参照データ セットに定義されているキーに基づいて行われます。

## <a name="add-a-reference-data-set"></a>参照データ セットを追加する

1. [Azure ポータル](https://portal.azure.com)にサインインします。

2. 既存の Time Series Insights 環境を見つけます。 Azure Portal の左側のメニューにある **[すべてのリソース]** をクリックします。 Time Series Insights 環境を選択します。

3. **[Environment Topology] (環境トポロジ)** 見出しで、**[Reference Data Sets] (参照データ セット)** を選択します。

    ![Time Series Insights の参照データ セットの作成](media/add-reference-data-set/getstarted-create-reference-data-set-1.png)

4. **[+ 追加]** を選択して、新しい参照データ セットを追加します。

5. 一意の**参照データ セットの名前**を指定します。

    ![Time Series Insights の参照データ セットの作成 - 詳細](media/add-reference-data-set/getstarted-create-reference-data-set-2.png)

6. 空のフィールドに**キー名**を指定し、その**型**を選択します。 この名前と型は、参照データに結合するため、イベント ソース内のイベントから正しいプロパティを選択するために使用されます。 

   たとえば、キー名を **DeviceId**、型を**文字列**として指定した場合、検索して結合する各受信イベントで、Time Series Insights のイングレス エンジンは**文字列**型の **DeviceId** という名前のプロパティを探します。 複数のキーを指定して、イベントと結合させることができます。 キー名を突き合わせる際は、大文字と小文字が区別されます。

7. **[作成]**を選択します。

## <a name="next-steps"></a>次のステップ
* プログラムで[参照データを管理](time-series-insights-manage-reference-data-csharp.md)する。
* 詳細な API リファレンスについては、[参照データ API](/rest/api/time-series-insights/time-series-insights-reference-reference-data-api) に関するドキュメントを参照してください。
