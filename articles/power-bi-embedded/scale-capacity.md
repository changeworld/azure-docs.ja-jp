---
title: Power BI Embedded 容量の拡大縮小 | Microsoft Docs
description: この記事では、Microsoft Azure で Power BI Embedded 容量の拡大縮小を行う手順について説明します。
services: power-bi-embedded
documentationcenter: ''
author: markingmyname
manager: kfile
editor: ''
tags: ''
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 01/19/2018
ms.author: maghan
ms.openlocfilehash: a089d34b3f49188f3037ad57aab4d7bcc8c660f2
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2018
---
# <a name="scale-your-power-bi-embedded-capacity"></a>Power BI Embedded 容量の拡大縮小

この記事では、Microsoft Azure で Power BI Embedded 容量の拡大縮小を行う手順について説明します。 拡大縮小によって、容量のサイズを増減できます。

この記事は、Power BI Embedded 容量を作成済みという前提で説明します。 まだ作成していない場合は、まず「[Create Power BI Embedded capacity in the Azure portal](create-capacity.md)」(Azure Portal で Power BI Embedded 容量を作成する) を参照してください。

> [!NOTE]
> 拡大縮小操作は、約 1 分かかることがあります。 この時間の間、容量を使用できません。 埋め込みコンテンツは読み込みに失敗することがあります。

## <a name="scale-a-capacity"></a>容量の拡大縮小

1. [Azure Portal](https://portal.azure.com/) にサインインします。

2. **[すべてのサービス]** > **[Power BI Embedded]** を選択して容量を確認します。

    ![Azure Portal の [すべてのサービス]](media/scale-capacity/azure-portal-more-services.png)

3. 拡大縮小する容量を選択します。

    ![Azure Portal の Power BI Embedded 容量リスト](media/scale-capacity/azure-portal-capacity-list.png)

4. 容量の **[スケール]** で **[価格レベル]** を選択します。

    ![[スケール] の [価格レベル] オプション](media/scale-capacity/azure-portal-scale-pricing-tier.png)

    現在の価格レベルは青枠で囲まれます。

    ![青枠で囲まれた現在の価格レベル](media/scale-capacity/azure-portal-current-tier.png)

5. スケール アップまたはスケール ダウンするには、移行先の新しいレベルを選択します。 新しいレベルを選択すると、選択されたレベルが点線の青枠で囲まれます。 **[選択]** を選択してスケールを新しいレベルに変更します。

    ![新しいレベルの選択](media/scale-capacity/azure-portal-select-new-tier.png)

    容量の拡大縮小が完了するまでに 1 ～ 2 分かかることがあります。

6. [概要] タブを表示してレベルを確認します。現在の価格レベルが表示されます。

    ![現在のレベルの確認](media/scale-capacity/azure-portal-confirm-tier.png)

## <a name="next-steps"></a>次の手順

容量を一時停止または開始するには、「[Pause and start your Power BI Embedded capacity in the Azure portal](pause-start.md)」(Azure Portal での Power BI Embedded 容量の一時停止と開始) を参照してください。

アプリケーション内に Power BI コンテンツの埋め込みを始めるには、「[How to embed your Power BI dashboards, reports and tiles](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/)」(Power BI のダッシュボード、レポート、タイルを埋め込む方法) を参照してください。

ご質問は、 [Power BI コミュニティで質問してみてください](http://community.powerbi.com/)。
