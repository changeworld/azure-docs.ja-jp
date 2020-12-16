---
title: Azure サブスクリプションの課金所有権の譲渡の概要
description: この記事では、Azure サブスクリプションの課金所有権を別のアカウントに譲渡する前に理解しておく必要がある事項について説明します。
keywords: Azure サブスクリプションを譲渡する, Azure サブスクリプションの譲渡, Azure サブスクリプションを別のアカウントに移動する, Azure サブスクリプション所有者の変更, Azure サブスクリプションを別のアカウントに譲渡する, Azure 課金所有権を譲渡する
author: bandersmsft
ms.reviewer: amberb
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: 163bd7dbdac4de7b7d9c1657ac3debf45382a27a
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2020
ms.locfileid: "97027273"
---
# <a name="about-transferring-billing-ownership-for-an-azure-subscription"></a>Azure サブスクリプションの課金所有権の譲渡の概要

この記事は、Azure サブスクリプションの課金所有権を別のアカウントに譲渡する前に知っておくべきことを理解するために役立ちます。 

自分が退職するときや、Azure サブスクリプションの請求先を別のアカウントに変える場合には、サブスクリプションの課金所有権を譲渡する必要があります。 別のアカウントに課金所有権を譲渡すると、管理者には請求タスク用の新しいアカウントのアクセス許可が付与されます。 これにより、支払い方法の変更、料金の表示、サブスクリプションの取り消しを行うことができます。

課金所有権を維持したまま、サブスクリプションの種類を変更する場合は、[別のオファーへの Azure サブスクリプションの切り替え](../manage/switch-azure-offer.md)に関するページを参照してください。 サブスクリプション内のリソースにアクセスできるユーザーを制御する場合は、「[Azure 組み込みロール](../../role-based-access-control/built-in-roles.md)」を参照してください。

マイクロソフト エンタープライズ契約 (EA) のお客様であれば、お客様のエンタープライズ管理者が、アカウント間でサブスクリプションの課金所有権を移すことができます。

アカウントの課金管理者のみがサブスクリプションの所有権を譲渡できます。

## <a name="determine-account-billing-administrator"></a>アカウントの課金管理者を判断する

<a name="whoisaa"></a>

課金管理者とは、アカウントに対する課金を管理する権限が付与されているユーザーのことです。 [Azure portal](https://portal.azure.com) で課金情報にアクセスする権限のほか、サブスクリプションの作成、請求書の表示と支払い、支払い方法の更新など、さまざまな課金関連の作業を行う権限が付与されています。

課金管理者であるアカウントを特定するには、[Azure portal で [コストの管理と請求]](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview) に移動します。 次に、左側のペインの **[すべての課金スコープ]** を選択します。 [サブスクリプション] ページに、自分が課金管理者となっているサブスクリプションがすべて一覧表示されます。

サブスクリプションのアカウント管理者が誰かわからない場合は、[Azure portal の [サブスクリプション] ページ](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)に移動します。 次に、確認するサブスクリプションを選択し、 **[設定]** を調べます。 **[プロパティ]** を選択すると、サブスクリプションのアカウント管理者が **[アカウント管理者]** ボックスに表示されます。


## <a name="supported-subscription-types"></a>サポートされているサブスクリプションの種類

Azure portal でのサブスクリプションの譲渡は、下記の種類のサブスクリプションについて行うことができます。 現在、[無料試用版](https://azure.microsoft.com/offers/ms-azr-0044p/)と [Azure イン オープン プラン (AIO)](https://azure.microsoft.com/offers/ms-azr-0111p/) のサブスクリプションについては、譲渡はサポートされていません。 対処法ついては、「[新しいリソース グループまたはサブスクリプションへのリソースの移動](../../azure-resource-manager/management/move-resource-group-and-subscription.md)」を参照してください。 サポート プランなどの他のサブスクリプションを譲渡する場合は、[Azure サポートにお問い合わせください](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

- [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)<sup>1</sup>
- [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)  
- [Visual Studio Enterprise (MPN) サブスクライバー](https://azure.microsoft.com/offers/ms-azr-0029p/)
- [MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p/)  
- [従量課金制](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [開発テスト用の従量課金制プラン](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise:BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [Microsoft Azure プラン](https://azure.microsoft.com/offers/ms-azr-0017g/)<sup>2</sup>

<sup>1</sup> EA ポータルを使用します。

<sup>2</sup> Azure Web サイトでサインアップしたときに作成されたアカウントのみでサポート。

## <a name="resources-transferred-with-subscriptions"></a>サブスクリプションを使用して譲渡されたリソース

VM、ディスク、Web サイトなどのすべてのリソースが、新しいアカウントに譲渡されます。 しかし、別の Azure AD テナント内のアカウントにサブスクリプションを譲渡する場合、そのサブスクリプションへの[管理者ロール](../manage/add-change-subscription-administrator.md)と [Azure ロールの割り当て](../../role-based-access-control/role-assignments-portal.md)は譲渡されません。 また、[アプリの登録](../../active-directory/develop/quickstart-register-app.md)とテナント固有のその他のサービスも、サブスクリプションと共に譲渡されることはありません。

## <a name="transfer-account-ownership-to-another-countryregion"></a>アカウントの所有権を別の国または地域に譲渡する

残念ながら、Azure portal を使用して国や地域間でサブスクリプションを譲渡することはできません。 ただし、Azure サポート リクエストを開いた場合、譲渡できる可能性があります。 サポート リクエストを作成するには、[サポートにお問い合わせください](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

## <a name="transfer-a-subscription-from-one-account-to-another"></a>サブスクリプションをあるアカウントから別のアカウントに譲渡する

2 つのアカウントの管理者の場合、アカウント間でサブスクリプションを譲渡できます。 概念上、そうしたアカウントは 2 人の異なるユーザーのアカウントと見なされるので、アカウント間でサブスクリプションを譲渡できます。
サブスクリプションの譲渡に必要な手順を確認するには、「[Azure サブスクリプションの課金所有権を譲渡する」](../manage/billing-subscription-transfer.md)を参照してください。

## <a name="transferring-a-subscription-shouldnt-create-downtime"></a>サブスクリプションを譲渡するときにダウンタイムが発生しないようにする

同じ Azure AD テナント内のアカウントにサブスクリプションを譲渡する場合、そのサブスクリプションで実行されているリソースに影響は生じません。 ただし、PowerShell に保存されたコンテキスト情報は更新されないため、それをクリアするか、設定を変更しなければならない場合があります。 サブスクリプションを別のテナントのアカウントに譲渡し、サブスクリプションをそのテナントに移行することにした場合、そのサブスクリプション内のリソースにアクセスするための [Azure ロールが割り当て](../../role-based-access-control/role-assignments-portal.md)られたすべてのユーザー、グループ、およびサービス プリンシパルのアクセス権は失われます。 サービスのダウンタイムが発生する可能性があります。

## <a name="new-account-usage-and-billing-history"></a>新しいアカウントの使用状況と請求履歴

新しいアカウントのユーザーが利用できる情報は、サブスクリプションの先月の料金のみです。 使用履歴と請求履歴の他の部分が、サブスクリプションと共に譲渡されることはありません。

## <a name="manually-migrate-data-and-services"></a>データとサービスを手動で移行する

サブスクリプションを譲渡すると、そのリソースはそのまま残ります。 サブスクリプションの所有権を譲渡できない場合、リソースを手動で移行することもできます。 詳細については、「 [新しいリソース グループまたはサブスクリプションへのリソースの移動](../../azure-resource-manager/management/move-resource-group-and-subscription.md)」を参照してください。

## <a name="remaining-subscription-credits"></a>残りのサブスクリプション クレジット 

Visual Studio または Microsoft Partner Network のサブスクリプションをお持ちの場合は、毎月のクレジットを取得します。 クレジットは、新しいアカウントのサブスクリプションと共に繰り越されません。 譲渡要求を受け取ったユーザーが、譲渡要求を受け入れるには、Visual Studio ライセンスが必要になります。 サブスクリプションでは、ユーザーのアカウントで利用可能な Visual Studio クレジットが使用されます。 詳細については、[Visual Studio と Partner Network のサブスクリプションの譲渡](../manage/billing-subscription-transfer.md#transfer-visual-studio-and-partner-network-subscriptions)に関するセクションを参照してください。

## <a name="users-keep-access-to-transferred-resources"></a>譲渡されたリソースへのアクセスをユーザーが保持する

所有権が譲渡されても、サブスクリプション内のリソースにアクセス権を持つユーザーにはそのアクセス権を残ることに注意してください。 しかし、[管理者ロール](../manage/add-change-subscription-administrator.md)と [Azure ロールの割り当て](../../role-based-access-control/role-assignments-portal.md)が削除される可能性があります。 ご自分のアカウントがサブスクリプションのテナントとは異なる Azure AD テナント内にあり、譲渡要求を送信したユーザーがサブスクリプションをご自分のアカウントのテナントに移行した場合、アクセス権が失われます。 

Azure portal で、サブスクリプション内のリソースにアクセスできる Azure ロールが割り当てられているユーザーを表示できます。 [Azure portal の [サブスクリプション] ページ](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)にアクセスします。 次に、確認するサブスクリプションを選択し、左側のペインで **[アクセス制御 (IAM)]** を選択します。 次に、ページの上部で **[ロールの割り当て]** を選択します。 [ロールの割り当て] ページに、サブスクリプションに対するアクセス権が付与されているすべてのユーザーが一覧表示されます。

[Azure ロールの割り当て](../../role-based-access-control/role-assignments-portal.md)が譲渡中に削除された場合でも、元の所有者アカウントのユーザーは、次のような他のセキュリティ メカニズムを通じて、引き続きサブスクリプションにアクセスできます。

* サブスクリプションのリソースに対する管理者権限をユーザーに付与する管理証明書。 詳細については、「[Azure の管理証明書の作成とアップロード](../../cloud-services/cloud-services-certs-create.md)」をご覧ください。
* Storage などのサービス用のアクセス キー。 詳細については、「[Azure ストレージ アカウントについて](../../storage/common/storage-account-create.md)」を参照してください。
* Azure Virtual Machines などのサービス用のリモート アクセス資格情報。

譲渡先でリソースへのアクセスを制限する必要がある場合、サービスに関連付けられているすべてのシークレットの更新を検討する必要があります。 ほとんどのリソースを更新することができます。 [Azure portal](https://portal.azure.com) にサインインし、[ハブ] メニューで **[すべてのリソース]** を選択します。 次に、リソースを選択します。 次に、リソースのページで **[設定]** を選択します。 ここで、既存のシークレットを表示して更新できます。

## <a name="you-pay-for-usage-when-you-receive-ownership"></a>所有権を譲渡された場合は、使用量に応じて課金されます

お使いのアカウントでは、譲渡時点より後に報告された使用量についてお支払いいただくことになります。 譲渡する前に発生したにもかかわらず、譲渡後に報告される使用量もあります。 この使用量は、お客様のアカウントの請求書に記載されます。

## <a name="use-a-different-payment-method"></a>別の支払い方法を使用する

譲渡要求を受け入れるときに、アカウントに関連付けられている既存の支払い方法を選択することも、新しい支払い方法を追加することもできます。

## <a name="transfer-enterprise-agreement-subscription-ownership"></a>Enterprise Agreement サブスクリプションの所有権を譲渡する

元のアカウント所有者が組織の一員ではなくなった後でも、エンタープライズ管理者は、任意のアカウントのアカウント所有権を更新できます。 Azure Enterprise Agreement アカウントの譲渡の詳細については、「[Azure エンタープライズ転送](../manage/ea-transfers.md)」を参照してください。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次のステップ

- [Azure サブスクリプションの課金所有権を譲渡する](../manage/billing-subscription-transfer.md)
