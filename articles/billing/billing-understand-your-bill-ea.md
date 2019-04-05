---
title: Azure Enterprise の課金内容を確認する | Microsoft Docs
description: Enterprise Agreement による Azure カスタマーの使用状況および課金内容の読み方と確認方法について説明します
services: ''
documentationcenter: ''
author: adpick
manager: dougeby
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2018
ms.author: banders
ms.openlocfilehash: 9786561192f1f9d52c2cfb3b2b7a5f45903e36b2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57858914"
---
# <a name="understand-your-bill-for-azure-customers-with-an-enterprise-agreement"></a>Enterprise Ageement による Azure カスタマーの課金内容を確認する

Enterprise Agreement による Azure カスタマーは、組織のクレジットを超過するか、クレジットが対応していないサービスを使用した際に請求書を受け取ります。 

組織のクレジットには、年額コミットメントが含まれています。 年額コミットメントとは、Azure サービスの使用に先立って組織が支払った金額です。 Enterprise Agreement に年額コミットメントの資金を追加するには、Microsoft アカウント マネージャーまたはリセラーに連絡してください。  

## <a name="when-credit-exceeded-or-doesnt-apply"></a>クレジットが超過した、または適用されない場合

お客様は、以下の状況で 1 枚以上の請求書を受け取ります。

- **サービスの超過**:組織の利用料金がクレジットの残高を超過した場合。
- **個別請求の料金**:組織が使用したサービスにクレジットが対応していない場合。 以下のサービスについては、クレジットの残高に関わらず請求書が発行されます。
    - Canonical
    - Citrix XenApp Essentials
    - Citrix XenDesktop 
    - 登録ユーザー
    - OpenLogic
    - Remote Access Rights XenApp Essentials 登録ユーザー
    - Ubuntu Advantage
    - Visual Studio Enterprise (月払い)
    - Visual Studio Enterprise (年払い)
    - Visual Studio Professional (月払い)
    - Visual Studio Professional (年払い)
- **Marketplace の料金**:Azure Marketplace での購入および利用は組織のクレジットでは対応しないため、個別に請求されます。 Enterprise Administrator は Enterprise Portal 内で組織による Marketplace の購入を有効または向こうにする権限を持ちます。 

請求期間中にサービス超過分と個別請求の料金の双方が存在する場合は、それぞれの種類の料金を含む 1 枚の請求書を受け取ります。 Marketplace の料金はつねに別途請求されます。

## <a name="review-charges"></a>請求金額の確認

請求書上の料金をレビューまたは承認するには、Enterprise Administrator である必要があります。 詳細については、[Azure の Azure Enterprise Agreement 管理者ロールを理解する](billing-understand-ea-roles.md)を参照してください。 お客様の組織の Enterprise Administraor が分からない場合、[サポートにお問い合わせください](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

### <a name="review-service-overage-invoice"></a>サービス超過分の請求書の確認

**レポート** > **使用状況の要約**内の Enterprise portal から取得できる使用量合計を、お客様が受け取ったサービスの超過分の請求書と比較してください。 サービスの超過分の請求書には、組織のクレジットを超える使用量や、クレジットに対応していないサービスが含まれています。 **使用状況の要約**に記載されている金額は税を含みません。 

1. [Enterprise portal](https://ea.azure.com) にサインインします。
1. **レポート**を選択します。
1. タブの右上の角で、**M** から **C** へビューを変更し、請求書の期間と一致させます。
 
   ![使用状況の要約上での M + C オプションを示すスクリーンショット。](./media/billing-understand-your-bill-ea/ea-portal-usage-sumary-cm-option.png)

1. **合計使用量**は、お客様が受け取ったサービス超過分の請求書に記載されている**合計拡張料金**と一致している必要があります。 次のテーブルに、請求書ならびに Enterprise portal 内の**使用状況の要約**に表示される用語および説明を示します。

   |請求書の用語|使用状況の要約の用語|説明|
   |---|---|---|
   |合計拡張料金|合計使用量|特定の期間中の、クレジット適用前の税抜き使用料金。|
   |コミットメント使用量|コミットメント使用量|その特定の期間中に適用されるクレジット。|
   |販売合計|超過分合計|クレジット額を超える使用量の合計料金。 この額には税は含まれません。|
   |税額|適用不可|特定の期間の販売合計に適用される税金。|
   |合計金額|適用不可|クレジットの適用後ならびに税の追加後の、請求書に記載される請求金額。|
1. 料金に関する詳細情報を表示するには、**使用量のダウンロード** > **高度なレポートをダウンロード**をご確認ください。 このレポートには税金や予約の費用、または marketplace の料金は含まれません。

      ![使用量のダウンロードタブ内、高度なレポートをダウンロードを表示するスクリーンショット。](./media/billing-understand-your-bill-ea/ea-portal-download-usage-advanced.png)

### <a name="review-marketplace-invoice"></a>Marketplace の請求書の確認

**レポート** > **使用状況の要約**内の Enterprise portal から取得できるAzure Marketplace 合計を、お客様が受け取った marketplace 請求書と比較してください。 Marketplace 請求書は、Azure Marketplace での購入および使用に限定されます。 **使用状況の要約**に記載されている金額は税を含みません。 

1. [Enterprise portal](https://ea.azure.com) にサインインします。
1. **レポート**を選択します。
1. タブの右上の角で、**M** から **C** へビューを変更し、請求書の期間と一致させます。

     ![使用状況の要約上での M + C オプションを示すスクリーンショット。](./media/billing-understand-your-bill-ea/ea-portal-usage-sumary-cm-option.png)

1. **Azure Marketplace** 合計は、marketplace 請求書に記載されている**販売合計**と一致している必要があります。
1. 使用に基づく料金に関する詳細については、**使用量のダウンロード**をご確認ください。 **Marketplace の料金**下で、**ダウンロード**を選択します。 このレポートは税または 1 回限りの購入を含みません。

     ![Marketplace の料金下のダウンロード オプションを表示するスクリーンショット。](./media/billing-understand-your-bill-ea/ea-portal-download-usage-marketplace.png)

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。
