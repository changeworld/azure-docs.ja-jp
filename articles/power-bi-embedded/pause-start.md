---
title: "Azure Portal での Power BI Embedded 容量の一時停止と開始 | Microsoft Docs"
description: "この記事では、Microsoft Azure で Power BI Embedded 容量の一時停止と開始を行う手順について説明します。"
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/28/2017
ms.author: asaxton
ms.openlocfilehash: ab5d184fde2cbcb517b325624c8405954b4d0972
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="pause-and-start-your-power-bi-embedded-capacity-in-the-azure-portal"></a>Azure Portal での Power BI Embedded 容量の一時停止と開始

この記事では、Microsoft Azure で Power BI Embedded 容量の一時停止と開始を行う手順について説明します。 この記事は、Power BI Embedded 容量を作成済みという前提で説明します。 まだ作成していない場合は、まず「[Create Power BI Embedded capacity in the Azure portal](create-capacity.md)」(Azure Portal で Power BI Embedded 容量を作成する) を参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。

## <a name="pause-your-capacity"></a>容量を一時停止する

容量を一時停止すると、その容量について課金されなくなります。 容量を一時停止は、一定期間、容量を使用する必要がない場合に適しています。 容量を一時停止するには、次の手順を実行します。

> [!NOTE]
> 容量を一時停止すると、Power BI 内からコンテンツを使用できなくなる可能性があります。 中断を防ぐには、一時停止する前に、容量からのワークスペースの割り当てを解除しておきます。

1. [Azure Portal](https://portal.azure.com/) にサインインします。

2. **[その他のサービス]** > **[Power BI Embedded]** を選択して容量を確認します。

    ![Azure Portal の [その他のサービス]](media/pause-start/azure-portal-more-services.png)

3. 一時停止する容量を選択します。

    ![Azure Portal の Power BI Embedded 容量リスト](media/pause-start/azure-portal-capacity-list.png)

4. 容量の詳細内にある **[一時停止]** を選択します。

    ![容量を一時停止する](media/pause-start/azure-portal-pause-capacity.png)

5. **[はい]** を選択し、容量を一時停止することを確認します。

    ![一時停止の確認](media/pause-start/azure-portal-confirm-pause.png)

## <a name="start-your-capacity"></a>容量を開始する

容量を開始して、使用を再開します。 容量を開始すると、課金も再開されます。

1. [Azure Portal](https://portal.azure.com/) にサインインします。

2. **[その他のサービス]** > **[Power BI Embedded]** を選択して容量を確認します。

    ![Azure Portal の [その他のサービス]](media/pause-start/azure-portal-more-services.png)

3. 開始する容量を選択します。

    ![Azure Portal の Power BI Embedded 容量リスト](media/pause-start/azure-portal-capacity-list.png)

4. 容量の詳細内にある **[開始]** を選択します。

    ![容量を開始する](media/pause-start/azure-portal-start-capacity.png)

5. **[はい]** を選択し、容量を開始することを確認します。

    ![開始の確認](media/pause-start/azure-portal-confirm-start.png)

任意のコンテンツがこの容量に割り当てられている場合は、開始後に使用可能になります。

## <a name="next-steps"></a>次のステップ

容量のスケール アップ/スケールダウンについては、「[Power BI Embedded 容量の拡大縮小](scale-capacity.md)」を参照してください。

アプリケーション内に Power BI コンテンツの埋め込みを始めるには、「[How to embed your Power BI dashboards, reports and tiles](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/)」(Power BI のダッシュボード、レポート、タイルを埋め込む方法) を参照してください。

ご質問は、 [Power BI コミュニティで質問してみてください](http://community.powerbi.com/)。