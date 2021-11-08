---
title: Microsoft Defender for Cloud の統合ワークロードの保護を有効にする
description: 強化されたセキュリティ機能を有効にして Microsoft Defender for Cloud の保護をハイブリッドおよびマルチクラウドのリソースに拡張する方法について説明します
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 06/07/2021
ms.openlocfilehash: 689fe8336594e26c283a75b5f4dfb0026c73ef32
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131441878"
---
# <a name="quickstart-enable-enhanced-security-features"></a>クイックスタート: 強化されたセキュリティ機能を有効にする

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

強化されたセキュリティ機能の利点については、「[Microsoft Defender for Cloud の強化されたセキュリティ機能](enhanced-security-features-overview.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

Defender for Cloud のクイックスタートとチュートリアルでは、強化されたセキュリティ機能を有効にする必要があります。 

Defender for Cloud の強化されたセキュリティ機能を使用して Azure サブスクリプション全体を保護できます。保護は、サブスクリプション内のすべてのリソースに継承されます。

30 日間の無料試用版が提供されています。 選択した通貨でのお住まいのリージョンに応じた価格の詳細については、[価格ページ](https://azure.microsoft.com/pricing/details/security-center/)を参照してください。

## <a name="enable-enhanced-security-features-from-the-azure-portal"></a>強化されたセキュリティ機能を Azure portal から有効にする

脅威防止機能など、すべての Defender for Cloud 機能を有効にするには、該当するワークロードを含むサブスクリプションで強化されたセキュリティ機能を有効にする必要があります。 ワークスペース レベルで有効にしても、Just-In-Time VM アクセス、適応型アプリケーション制御、Azure リソースのネットワーク検出は有効になりません。 さらに、ワークスペース レベルで使用できる Microsoft Defender プランは、Microsoft Defender for servers と Microsoft Defender for SQL servers on machines のみです。

- **Microsoft Defender for Storage アカウント** は、サブスクリプション レベルまたはリソース レベルで有効にできます
- **Microsoft Defender for SQL** は、サブスクリプション レベルまたはリソース レベルで有効にできます
- **オープンソースのリレーショナル データベース用 Microsoft Defender** は、リソース レベルでのみ有効にできます

### <a name="to-enable-enhanced-security-features-on-your-subscriptions-and-workspaces"></a>強化されたセキュリティ機能をサブスクリプションとワークスペースで有効にするには:

- 強化されたセキュリティ機能を 1 つのサブスクリプションで有効にするには:

    1. Defender for Cloud のメイン メニューで **[環境設定]** を選択します。
    1. 保護するサブスクリプションまたはワークスペースを選択します。
    1. アップグレードするために **[すべての Microsoft Defender for Cloud プランの有効化]** を選択します。
    1. **[保存]** を選択します。

    > [!TIP]
    > 各 Microsoft Defender プランは個別に価格設定されているので、個別に有効/無効に設定できます。 たとえば、App Service プランが関連付けられていないサブスクリプションで Azure Defender for App Service をオフにすることができます。 

    :::image type="content" source="./media/enhanced-security-features-overview/pricing-tier-page.png" alt-text="ポータル内の Defender for Cloud の価格ページ":::

- 強化されたセキュリティを複数のサブスクリプションまたはワークスペースで有効にするには:

    1. Defender for Cloud のメニューから **[概要]** を選択します。

        **[アップグレード]** タブに、オンボードの対象となるサブスクリプションとワークスペースが一覧表示されます。

        :::image type="content" source="./media/enable-enhanced-security/get-started-upgrade-tab.png" alt-text="[はじめに] ページの [アップグレード] タブ。"::: 

    1. **[Microsoft Defender for Cloud で保護するサブスクリプションとワークスペースの選択]** の一覧で、アップグレードするサブスクリプションとワークスペースを選択し、 **[アップグレード]** を選択してすべての Microsoft Defender for Cloud 機能を有効にします。

       - 試用版の対象にならないサブスクリプションやワークスペースを選択した場合、次の手順でそれらがアップグレードされ、課金が開始されます。
       - 無料試用版の対象となるワークスペースを選択した場合、次の手順で試用版が開始されます。

        :::image type="content" source="./media/enable-enhanced-security/upgrade-selected-workspaces-and-subscriptions.png" alt-text="[作業の開始] ページから、選択したすべてのワークスペースとサブスクリプションをアップグレードする。":::


## <a name="disable-enhanced-security-features"></a>強化されたセキュリティ機能を無効にする

サブスクリプションに対して強化されたセキュリティ機能を無効にする必要がある場合、手順は同じですが、 **[強化されたセキュリティ無効]** を選択します。
 
1. [Defender for Cloud] メニューで、 **[環境設定]** を開きます。
1. 関連するサブスクリプションを選択します。
1. **[Defender プラン]** を選択し、 **[強化されたセキュリティ無効]** を選択します。

    :::image type="content" source="./media/enable-enhanced-security/disable-plans.png" alt-text="Defender for Cloud の強化されたセキュリティ機能を有効または有効にします。":::

1. **[保存]** を選択します。

> [!NOTE]
> 強化されたセキュリティ機能を無効にすると、1 つのプランを無効にしたか、すべてのプランを一度に無効にしたかどうかに関係なく、データ収集が短時間継続される場合があります。 

## <a name="next-steps"></a>次の手順

強化されたセキュリティ機能を有効にした後は、[自動プロビジョニング エージェントと拡張機能](enable-data-collection.md)に関するページに説明されているように、必要なエージェントと拡張機能を有効にして自動データ収集を行います。