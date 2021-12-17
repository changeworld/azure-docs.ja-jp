---
title: クォータの増加をサポートに依頼する
description: Azure portal で Azure Data Factory のサポート リクエストを作成し、クォータの増加や問題解決の支援を依頼する方法。
ms.service: data-factory
ms.topic: conceptual
ms.subservice: troubleshooting
ms.date: 03/10/2020
author: jonburchel
ms.author: jburchel
ms.openlocfilehash: a6cffaf7b1f49e747b2d160318673dd1a07b9885
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132350087"
---
# <a name="request-quota-increases-and-get-support-for-azure-data-factory"></a>クォータの増加を依頼し、Azure Data Factory のサポートを得る

この記事では、Azure portal で Azure Data Factory のサポート チケットを送信する方法について説明します。 このプロセスでは、クォータの増加を要求したり、エンジニアリング サポート チームのテクニカル サポート リクエストを送信したりできます。

## <a name="create-a-support-ticket"></a>サポート チケットの作成

Azure portal から Azure Data Factory の新しいサポート要求を作成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) のメニューで、 **[ヘルプとサポート]** を選択します。

   ![[ヘルプとサポート] リンク](./media/quota-increase/help-plus-support.png)


1. **[ヘルプとサポート]** で、 **[新しいサポート リクエスト]** を選択します

    :::image type="content" source="./media/quota-increase/new-support-request.png" alt-text="新しいサポート リクエストを作成する":::

1. お使いの [Azure サポート プラン](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/)を確認します。

   * **課金、クォータ、サブスクリプション管理** のサポートは、すべてのサポート レベルでご利用いただけます。
   * **障害対応** のサポートは、[Developer](https://azure.microsoft.com/support/plans/developer/)、[Standard](https://azure.microsoft.com/support/plans/standard/)、[Professional Direct](https://azure.microsoft.com/support/plans/prodirect/)、または [Premier](https://azure.microsoft.com/support/plans/premier/) サポートを通して提供されます。 障害対応のサポートとは、Azure のご利用に際して発生した、マイクロソフトが原因と想定される問題への対応です。
   * **開発者支援** および **アドバイザリ サービス** は、[Professional Direct](https://azure.microsoft.com/support/plans/prodirect/) および [Premier](https://azure.microsoft.com/support/plans/premier/) サポート レベルでご利用いただけます。

   Premier サポート プランを使用している場合は、[Microsoft Premier オンライン ポータル](https://premier.microsoft.com/)で Azure Data Factory 関連の問題を報告することもできます。 各種のサポート プランの詳細 (範囲、応答時間、価格などを含む) については、[Azure サポート プラン](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/)に関するページを参照してください。Azure サポートに関するよく寄せられる質問については、「[Azure サポートに関する FAQ](https://azure.microsoft.com/support/faq/)」を参照してください。

1. **[問題の種類]** で、適切な問題の種類を選択します。 障害対応の問題の場合は、 **[技術]** を選択します。 クォータの増加の要求の場合は、 **[サービスとサブスクリプションの制限 (クォータ)** ] を選択します。

   :::image type="content" source="./media/quota-increase/select-quota-issue-type.png" alt-text="問題の種類を選択する":::  

   > [!NOTE]
   > この記事の残りの部分では、クォータ増加の要求に焦点を当てます。 ただし、ここで **[技術]** を選択することで、問題解決のサポート リクエストを行うことも出来ます。 **[技術]** を選択した場合は、概要を入力してから、 **[問題の種類を選択してください]** を選択して問題の種類を特定するよう求められます。 問題の解決に役立つ解決策が表示される場合があります。 提示された解決策で問題が解決しない場合は、 **[次へ: 詳細]** を選択してフォームに記入し、サポート チケットを送信します。

1. クォータの増加を依頼する場合、 **[クォータの種類]** で **[Data Factory]** を選択します。 **[次へ]** を選択します。

   :::image type="content" source="./media/quota-increase/select-quota-type.png" alt-text="クォータの種類を選択する":::

1. **[追加情報]** ウィンドウで、増加が必要なクォータ上限に関する追加情報を入力します。  特定の上限については、「[Azure Data Factory の制限](../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits)」を参照してください。  希望する連絡方法を選択し、 **[次へ]** をクリックしてください。

   :::image type="content" source="./media/quota-increase/provide-details.png" alt-text="&quot;[詳細の指定]&quot; セクション":::

## <a name="submit-your-request"></a>要求を送信する

最終ページで要求の詳細を確認したら、 **[作成]** を選択して要求を送信します。

## <a name="monitor-a-support-ticket"></a>サポート チケットの状態の確認

サポート要求を送信すると、Azure サポート チームから連絡があります。 要求の状態と詳細を確認するには、ダッシュボードで **[すべてのサポート要求]** を選択します。

:::image type="content" source="./media/quota-increase/monitor-ticket.png" alt-text="状態の確認":::

## <a name="other-resources"></a>その他のリソース

[Stack Overflow](https://stackoverflow.com/questions/tagged/azure-data-factory) や [Azure Data Factory の Microsoft Q&A 質問ページ](/answers/topics/azure-data-factory.html)で Azure Data Factory のコミュニティとつながることもできます。