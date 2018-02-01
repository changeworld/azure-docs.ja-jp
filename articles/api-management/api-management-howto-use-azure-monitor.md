---
title: "Azure API Management で発行された API を監視する | Microsoft Docs"
description: "このチュートリアルの手順に従って、Azure API Management で API を監視する方法を学びます。"
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: db1ed08c4d4c9e9abd525ec13f5511da82ee1fe4
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2018
---
# <a name="monitor-published-apis"></a>発行された API を監視する

Azure Monitor は、すべての Azure リソースを監視するための単一ソースを提供する Azure サービスです。 Azure Monitor を使用すると、API Management などの Azure リソースのメトリックとログを視覚化、クエリ、ルーティング、アーカイブし、そのメトリックとログに対してアクションを実行できます。 

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * アクティビティ ログを表示する
> * 診断ログを表示する
> * API のメトリックを表示する 
> * API が許可されていない呼び出しを受けたときのアラート ルールをセットアップする

次のビデオは、Azure Monitor を使用して API Management を監視する方法を示しています。 

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Monitor-API-Management-with-Azure-Monitor/player]
>
>

## <a name="prerequisites"></a>前提条件

+ [Azure API Management インスタンスの作成](get-started-create-service-instance.md)に関するクイック スタートを完了します。
+ また、「[Import and publish your first API (最初の API をインポートして発行する)](import-and-publish.md)」のチュートリアルも完了します。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="diagnostic-logs"></a>アクティビティ ログを表示する

アクティビティ ログは、API Management サービスで実行された操作に関する情報を提供します。 アクティビティ ログを使用すると、API Management サービスで発生した書き込み操作 (PUT、POST、DELETE) について、"いつ誰が何を" 行ったのかを確認できます。 

> [!NOTE]
> アクティビティ ログには、読み取り (GET) 操作、従来のパブリッシャー ポータルで実行された操作、または元の管理 API の使用に関する情報は含まれません。

アクティビティ ログには API Management サービスでアクセスするか、Azure Monitor ですべての Azure リソースのログにアクセスできます。 

アクティビティ ログを表示するには、次の手順に従います。

1. APIM サービス インスタンスを選びます。
2. **[アクティビティ ログ]** をクリックします。

## <a name="view-diagnostic-logs"></a>診断ログを表示する

診断ログは、監査とトラブルシューティングを行うために重要な、操作とエラーについての豊富な情報を提供します。 診断ログは、アクティビティ ログとは異なります。 アクティビティ ログは、API リソースで実行された操作に関する情報を提供します。 診断ログでは、リソース自体が実行した操作を調査できます。

診断ログにアクセスするには、次の手順に従います。

1. APIM サービス インスタンスを選びます。
2. **[診断ログ]** をクリックします。

## <a name="view-metrics-of-your-apis"></a>API のメトリックを表示する

API Management はメトリックを 1 分間隔で出力するので、API の状態と正常性をほぼリアルタイムで把握できます。 使用できるメトリックの一部の概要を次に示します。

* 容量 (プレビュー): APIM サービスのアップグレード/ダウングレードに関する判断に役立ちます。 このメトリックは 1 分ごとに出力され、報告時のゲートウェイの容量を反映しています。 メトリックの範囲は 0 ～ 100 で、CPU やメモリの使用率などのゲートウェイ リソースに基づいて計算されます。
* ゲートウェイ要求の合計: 期間中の API 要求の数。 
* 成功したゲートウェイ要求: 304、307 および 301 よりも小さな値 (200 など) を含む成功した HTTP 応答コードを受信した API 要求の数。 
* 失敗したゲートウェイ要求: 400 および 500 よりも大きな値を含む失敗した HTTP 応答コードを受信した API 要求の数。
* 未承認ゲートウェイ要求: 401、403、および 429 を含む HTTP 応答コードを受信した API 要求の数。 
* その他のゲートウェイ要求: 上記のカテゴリのいずれにも属していない HTTP 応答コード (たとえば 418) を受信した API 要求の数。

メトリックにアクセスするには、次の手順に従います。

1. ページの下部にあるメニューの **[メトリック]** を選択します。
2. ドロップダウン リストで、関心のあるメトリックを選択します (複数のメトリックを追加することができます)。 
    
    たとえば、使用できるメトリックの一覧で、**[Total Gateway Requests]\(ゲートウェイ要求の合計\)** と **[Failed Gateway Requests]\(失敗したゲートウェイ要求\)** を選択します。
3. グラフには、API 呼び出しの合計数が表示されます。 失敗した API 呼び出しの数も表示されます。 

## <a name="set-up-an-alert-rule-for-unauthorized-request"></a>許可されていない要求に対するアラート ルールをセットアップする

メトリックとアクティビティ ログに基づいてアラートを受け取るように設定できます。 Azure Monitor では、アラートがトリガーされたときに次の処理を実行するように構成することができます。

* 電子メール通知を送信する
* Webhook を呼び出す
* Azure Logic App を呼び出す

アラートを構成するには、以下の手順に従います。

1. ページの下部にあるメニュー バーの **[アラート ルール]** を選択します。
2. **[メトリック アラートの追加]** を選択します。
3. このアラートの**名前**を入力します。
4. 監視するメトリックとして、**[Unauthorized Gateway Requests]\(未承認ゲートウェイ要求\)** を選択します。
5. **[所有者、共同作成者、閲覧者に電子メールを送信]** を選択します。
6. **[OK]** をクリックします。
7. API キーなしで、Conference API を呼び出してみます。 この API Management サービスの所有者として、電子メール アラートを受信します。 

    > [!TIP]
    > アラート ルールは、トリガーされたときに webhook または Azure Logic App を呼び出すこともできます。

    ![アラートのセットアップ](./media/api-management-azure-monitor/set-up-alert.png)

## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * アクティビティ ログを表示する
> * 診断ログを表示する
> * API のメトリックを表示する 
> * API が許可されていない呼び出しを受けたときのアラート ルールをセットアップする

次のチュートリアルに進みます。

> [!div class="nextstepaction"]
> [呼び出しをトレースする](api-management-howto-api-inspector.md)
