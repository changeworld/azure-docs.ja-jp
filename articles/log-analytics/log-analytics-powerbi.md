---
title: Azure Log Analytics データを Power BI にインポートする | Microsoft Docs
description: Power BI は、さまざまなデータのセットの分析のために豊富な視覚エフェクトとレポートを提供するマイクロソフトのクラウド ベースのビジネス分析サービスです。  この記事では、Log Analytics のデータを構成し、Power BI にインポートする方法と、それを自動的に更新するように構成する方法について説明します。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 83edc411-6886-4de1-aadd-33982147b9c3
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: fb05ddabab3702299df0e81e8dda5af5cb676c1a
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37127511"
---
# <a name="import-azure-log-analytics-data-into-power-bi"></a>Power BI への Azure Log Analytics データのインポート


[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) は、さまざまなデータのセットの分析のために豊富な視覚エフェクトとレポートを提供するマイクロソフトのクラウド ベースのビジネス分析サービスです。  Log Analytics ログ検索の結果を Power BI のデータセットにインポートできます。そうすることでさまざまなソースのデータを組み合わせたり、Web とモバイル デバイス上でレポートを共有したりするような機能を利用できます。

## <a name="overview"></a>概要
Log Analytics ワークスペースのデータを Power BI にインポートするには、Log Analytics のログ検索クエリに基づくデータセットを Power BI で作成します。  クエリはデータセットが更新されるたびに実行されます。  それからデータセットのデータを使用した Power BI レポートを作成できます。  Power BI でデータセットを作成するには、Log Analytics から [Power Query (M) 言語](https://msdn.microsoft.com/library/mt807488.aspx)にクエリをエクスポートします。  次にこのクエリを使用して Power BI Desktop でクエリを作成し、Power BI にデータセットとして発行します。  このプロセスの詳細を以下で説明します。

![Power BI への Log Analytics](media/log-analytics-powerbi/overview.png)

## <a name="export-query"></a>クエリをエクスポートする
まず、Power BI データセットに設定するデータを Log Analytics が返すような[ログ検索](log-analytics-log-search-new.md)を作成します。  次にそのクエリを Power BI Desktop で使用できる [Power Query (M) 言語](https://msdn.microsoft.com/library/mt807488.aspx)にエクスポートします。

1. データセットからデータを抽出するために、Log Analytics でログ検索を作成します。
2. ログ検索ポータルを使用する場合は、**[Power BI]** をクリックします。  Analytics ポータルを使用する場合は、**[エクスポート]** > **[Power BI Query (M)]** の順に選択します。  このオプションはどちらも **PowerBIQuery.txt** という名前のテキスト ファイルをエクスポートします。 

    ![ログ検索のエクスポート](media/log-analytics-powerbi/export-logsearch.png) ![ログ検索のエクスポート](media/log-analytics-powerbi/export-analytics.png)

3. テキスト ファイルを開き、その内容をコピーします。

## <a name="import-query-into-power-bi-desktop"></a>Power BI Desktop にクエリをインポートする
Power BI Desktop は、Power BI に発行可能なデータセットおよび レポートを作成できるデスクトップ アプリケーションです。  これを使用して、Log Analytics からエクスポートした Power Query 言語を使ってクエリを作成することもできます。 

1. まだお持ちでない場合は [Power BI Desktop](https://powerbi.microsoft.com/desktop/) をインストールし、アプリケーションを開きます。
2. **[データの取得]** > **[空のクエリ]** の順に選択して新しいクエリを開きます。  次に **[詳細エディター]** を選択し、エクスポートしたファイルの内容をクエリに貼り付けます。 **[Done]** をクリックします。

    ![Power BI Desktop クエリ](media/log-analytics-powerbi/desktop-new-query.png)

5. クエリが実行され、結果が表示されます。  Azure に接続するための資格情報を要求される場合があります。  
6. クエリにわかりやすい名前を入力します。  規定値は "**Query1**" です。 **[閉じて適用]** をクリックして、レポートにデータセットを追加します。

    ![Power BI Desktop の名前](media/log-analytics-powerbi/desktop-results.png)



## <a name="publish-to-power-bi"></a>Power BI に発行する
Power BI に発行すると、データセットとレポートが作成されます。  Power BI Desktop でレポートを作成すると、お使いのデータを使用したレポートが発行されます。  そうしない場合は、空のレポートが作成されます。  Power BI でレポートを変更したり、データセットに基づく新しいレポートを作成したりできます。

8. お使いのデータに基づくレポートを作成します。  不慣れな場合には [Power BI Desktop のドキュメント](https://docs.microsoft.com/power-bi/desktop-report-view)を使用してください。  Power BI に送信する準備ができたら、**[発行]** をクリックします。  メッセージが表示されたら、Power BI アカウント内で発行先を選択します。  特定の発行先がない限りは、**[マイ ワークスペース]** を使用します。

    ![Power BI Desktop の発行](media/log-analytics-powerbi/desktop-publish.png)

3. 発行が完了したら **[Power BI で開く]** をクリックし、新しいデータセットで Power BI を開きます。


### <a name="configure-scheduled-refresh"></a>スケジュールされた更新を構成する
Power BI で作成されたデータセットには、前に Power BI Desktop で見たのと同じデータがあります。  再度クエリを実行して Log Analytics の最新のデータを設定するために、データセットを定期的に更新する必要があります。  

1. レポートをアップロードしたワークスペースをクリックし、**[データセット]** メニューを選択します。 新しいデータセットの横にあるコンテキスト メニューを選択し、**[設定]** を選択します。 **[データ ソースの資格情報]** に、資格情報が無効であることを示すメッセージがあるはずです。  これは、そのデータを更新するときにデータセットが使用する資格情報を、まだ指定していないためです。  **[資格情報の編集]** をクリックし、Log Analytics にアクセスできる資格情報を指定します。

    ![Power BI のスケジュール](media/log-analytics-powerbi/powerbi-schedule.png)

5. **[スケジュールされている更新]** で、**[データを最新の状態に保つ]** オプションをオンにします。  必要に応じて **[更新の頻度]** および更新を実行する 1 つまたは複数の特定の時間を変更できます。

    ![Power BI の更新](media/log-analytics-powerbi/powerbi-schedule-refresh.png)



## <a name="next-steps"></a>次の手順
* [ログ検索](log-analytics-log-searches.md) について学習し、Power BI にエクスポートできるクエリを作成します。
* [Power BI](http://powerbi.microsoft.com) について詳しく学習し、Log Analytics のエクスポートに基づく視覚エフェクトを作成します。
