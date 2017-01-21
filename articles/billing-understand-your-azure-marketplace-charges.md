---
title: "Azure 外部サービスの課金について | Microsoft Docs"
description: "Azure 外部サービス (旧称 Marketplace) の課金について説明します。"
services: 
documentationcenter: 
author: adpick
manager: ruchic
editor: 
tags: billing
ms.assetid: 5e0e2a3c-d111-4054-8508-0c111c1b749b
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2016
ms.author: adpick
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 45e0d553179b63de2d0664314271472f1151d73f


---
# <a name="understand-your-azure-external-service-charges"></a>Azure 外部サービスの課金について
この記事では、Azure 外部サービスの課金について説明します。 外部サービスは、以前は Marketplace と呼ばれていました。 外部サービスは、独立系サービス ベンダーによって提供されますが、Azure エコシステムに完全に統合されます。 以下の項目について説明します。

* 外部サービスを識別する
* 他の Azure リソースとの課金方法の違いを理解する
* 外部サービスの使用から発生するコストを追跡して表示する
* 外部サービスの注文を管理して支払いを行う

## <a name="what-are-azure-external-services"></a>Azure 外部サービスとは
外部サービスは、以前は Azure Marketplace と呼ばれていました。 通常、外部サービスは、サードパーティが公開している Azure で使用できるサービスです。 たとえば、ClearDB と SendGrid は、Azure で購入できる外部サービスですが、これらはマイクロソフトが公開したものではありません。

### <a name="identify-external-services"></a>外部サービスを識別する
新しい外部サービスまたはリソースをプロビジョニングすると、警告が表示されます。

![Marketplace 購入警告](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

> [!NOTE]
> 外部サービスは Microsoft 以外の会社によって公開されますが、Microsoft 製品が外部サービスとして分類される場合があります。
> 
> 

### <a name="external-services-are-billed-separately"></a>外部サービスは個別に請求されます
外部サービスは、Azure サブスクリプションの中で個別の注文として扱われます。 各サービスの請求期間は、サービスの購入時に設定されます。 サービスの請求期間とサービスを購入するために使用したサブスクリプションの請求期間を混同しないでください。 また、請求書は個別に発行され、クレジット カードの決済も個別に行われます。

### <a name="each-external-service-has-a-different-billing-model"></a>外部サービスの課金モデルはサービスごとに異なります
一部のサービスは従量課金制で請求され、一部のサービスは月単位で課金されます。 Azure 外部サービスにはクレジット カードが必要であり、請求書による支払いで外部サービスを購入することはできません。

### <a name="you-cant-use-monthly-free-credits-for-external-services"></a>外部サービスでは月々の無料クレジットは使用できません
[無料クレジット](https://azure.microsoft.com/pricing/spending-limits/)付きの Azure サブスクリプションを使用している場合でも、外部サービスの請求に対して無料クレジットを適用することはできません。 外部サービスを購入する際は、クレジット カードをご使用ください。

## <a name="view-external-service-spending-and-history"></a>外部サービスの支払い履歴を表示する
サブスクライブ中の外部サービスの一覧を [Azure ポータル](https://portal.azure.com/)に表示できます。 

1. [Azure Portal](https://portal.azure.com/) にアカウント管理者としてサインインします。
2. ハブ メニューで、**[サブスクリプション]** を選択します。
   
    ![ハブ メニューで [サブスクリプション] を選択します](./media/billing-understand-your-azure-marketplace-charges/sub-button.png) 
3. **[サブスクリプション]** ブレードで、表示するサブスクリプションを選択し、**[External services (外部サービス)]** を選択します。
   
    ![[課金] ブレードでサブスクリプションを選択](./media/billing-understand-your-azure-marketplace-charges/select-sub-external-services.png)
4. 外部サービスについて、公開元の名前、購入したサービス レベル、リソースに付けた名前、および現在の注文状態が注文ごとに表示されます。 過去の課金情報を表示する外部サービスを選択します。
   
    ![外部サービスを選択](./media/billing-understand-your-azure-marketplace-charges/external-service-blade2.png)
5. ここから、過去の請求金額について、税金を含む内訳を表示できます。
   
    ![外部サービスの請求履歴の表示](./media/billing-understand-your-azure-marketplace-charges/billing-overview-blade.png)

## <a name="manage-payment-methods-for-external-service-orders"></a>外部サービスに対する支払い方法を管理する
外部サービスに対する支払い方法の更新は[アカウント センター](https://account.windowsazure.com/)で行ってください。

> [!NOTE]
> 職場または学校のアカウントを使用してサブスクリプションを購入しているときに支払い方法を変更する場合は、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。
> 
> 

1. [アカウント センター](https://account.windowsazure.com/)にサインインし、[**[marketplace]** タブに移動](https://account.windowsazure.com/Store)します。
   
    ![アカウント センターで [marketplace] を選択](./media/billing-understand-your-azure-marketplace-charges/select-marketplace.png)
2. 管理する外部サービスを選択します。
   
    ![管理する外部サービスを選択](./media/billing-understand-your-azure-marketplace-charges/select-ext-service.png)
3. ページの右側にある **[支払い方法の変更]** をクリックします。 このリンクをクリックすると、支払い方法を管理する別のポータルに移動します。
   
    ![Order summary](./media/billing-understand-your-azure-marketplace-charges/change-payment.PNG)
4. **[情報の編集]** をクリックし、指示に従って支払い情報を更新します。
   
    ![[情報の編集] を選択](./media/billing-understand-your-azure-marketplace-charges/edit-info.png)

## <a name="cancel-an-external-service-order"></a>外部サービスの注文をキャンセルする
外部サービスの注文をキャンセルする場合は、[Azure ポータル](https://portal.azure.com)でリソースを削除する必要があります。

![リソースを削除](./media/billing-understand-your-azure-marketplace-charges/deleteMarketplaceOrder.PNG)

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください。
まだ他に質問がある場合は、問題を迅速に解決できるよう [サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ください。




<!--HONumber=Nov16_HO3-->


