---
title: Azure サブスクリプションと予約の譲渡ハブ
description: この記事は、Azure サブスクリプションを譲渡するために必要なことを理解するのに役立ち、詳細情報の他の記事へのリンクも掲載されています。
author: bandersmsft
ms.reviewer: andalmia
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 10/12/2021
ms.author: banders
ms.custom: ''
ms.openlocfilehash: dbe893dc471f055dd5b062e7ce5dcf492dd0f421
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007010"
---
# <a name="azure-subscription-and-reservation-transfer-hub"></a>Azure サブスクリプションと予約の譲渡ハブ

この記事では、Azure サブスクリプションと Azure の予約でサポートされている譲渡の種類 (_製品_ と呼ばれます) について説明します。 さらに、異なる課金契約間で Azure 製品を譲渡するために必要なことを理解するのに役立ち、特定の譲渡の詳細情報についての他の記事へのリンクも掲載しています。 Azure 製品は、さまざまな Azure 契約の種類に基づいて作成され、譲渡元の契約の種類から別の契約の種類への譲渡は、譲渡元と譲渡先の契約の種類によって異なります。 Azure 製品の譲渡は、譲渡元と譲渡先の契約の種類に応じて、自動または手動のプロセスになります。 手動プロセスの場合は、契約の種類によって、必要な手動の作業量が決まります。

> [!NOTE]
> Azure 製品には多くの種類がありますが、すべての製品をある種類から別の種類に譲渡できるわけではありません。 サポートされている製品譲渡のみをこの記事で説明しています。 この記事で対処されていない状況についての支援が必要な場合は、支援を求める [Azure サポート リクエスト](https://go.microsoft.com/fwlink/?linkid=2083458)を作成できます。

この記事は、Azure 製品の課金所有権を別のアカウントに譲渡する _前_ に知っておくべきことを理解するために役立ちます。 組織を離れるときや、製品の請求先を別のアカウントにする場合に、Azure 製品の課金所有権を譲渡する必要がある可能性があります。 別のアカウントに課金所有権を譲渡すると、管理者には請求タスク用の新しいアカウントのアクセス許可が付与されます。 これにより、支払い方法の変更、料金の表示、製品の取り消しを行うことができます。

課金所有権を維持したまま、製品の種類を変更する場合は、[別のオファーへの Azure サブスクリプションの切り替え](switch-azure-offer.md)に関するページを参照してください。 製品内のリソースにアクセスできるユーザーを制御する場合は、「[Azure 組み込みロール](../../role-based-access-control/built-in-roles.md)」を参照してください。

マイクロソフト エンタープライズ契約 (EA) のお客様であれば、お客様のエンタープライズ管理者が、EA ポータルで、製品の課金所有権をアカウント間で譲渡することができます。 詳細については、「[Azure サブスクリプションまたはアカウントの所有権を変更する](ea-portal-administration.md#change-azure-subscription-or-account-ownership)」をご覧ください。

この記事では、製品の譲渡に焦点を当てています。 ただし、リソースの譲渡についても一部の製品譲渡シナリオで必要になるため説明しています。

異なる Azure AD テナント間の製品の譲渡の詳細については、「[Azure サブスクリプションを別の Azure AD ディレクトリに移転する](../../role-based-access-control/transfer-subscription.md)」を参照してください。

> [!NOTE]
> ほとんどの課金所有権の譲渡では、基になる製品のサービス テナントは変更されません。 ダウンタイムは発生しません。 ただし、課金テナントが変更された場合でも、変更は実行中のサービスやリソースに影響を与えません。

## <a name="product-transfer-planning"></a>製品の譲渡計画

製品の譲渡の計画を開始するときに、次の質問に答えるために必要な情報を考慮してください。

- 製品の譲渡はなぜ必要か
- 製品の譲渡で求められるタイムラインはどうなっているか
- 製品の現在のオファーの種類と、それを何に譲渡するか
  - Microsoft Online Services プログラム (MOSP)。従量課金制 (PAYG) とも呼ばれます
  - CSP での以前の Azure オファー
  - CSP の新しい Azure オファー。Microsoft パートナー契約 (MPA) による Azure プランとも呼ばれます
  - Enterprise Agreement (EA)
  - Microsoft の担当者を通じて Azure サービスを購入する場合の Enterprise 申請での Microsoft 顧客契約 (MCA) と、Azure.com 経由で Azure サービスを購入する場合の個々の MCA
  - MSDN、BizSpark、EOPEN、Azure Pass、無料試用版などのその他
- 譲渡を達成するために必要な製品へのアクセス許可があるか 各譲渡の種類に必要な特定のアクセス許可を、次の製品譲渡サポートの表に一覧表示しています。
  - アカウントの課金管理者のみがサブスクリプションの所有権を譲渡できます。
  - 課金管理者の所有者だけが予約所有権を譲渡できます。
- 予約から利点が得られる既存のサブスクリプションがあるか、および予約をサブスクリプションと共に譲渡する必要があるか

譲渡を続行する前に、各質問の回答を用意する必要があります。

上記の質問に対する回答は、他者と早期にコミュニケーションを取り、予測やタイムラインを設定するのに役立ちます。 製品譲渡作業は大きく異なりますが、譲渡には予想よりも長い時間がかかる可能性があります。

譲渡元と譲渡先のオファーの種類に関する質問に対する回答は、従う必要がある技術的パスを定義し、譲渡の組み合わせで発生する可能性のある制限を特定するのに役立ちます。 制限については、次のセクションで詳しく説明します。

## <a name="product-transfer-support"></a>製品譲渡のサポート

次の表に、さまざまな契約の種類間の製品譲渡のサポートについて説明します。 各種類の譲渡の詳細についてのリンクを提供しています。

現在、[無料試用版](https://azure.microsoft.com/offers/ms-azr-0044p/)と [Azure イン オープン (AIO)](https://azure.microsoft.com/offers/ms-azr-0111p/) 製品については、譲渡がサポートされていません。 対処法ついては、「[新しいリソース グループまたはサブスクリプションへのリソースの移動](../../azure-resource-manager/management/move-resource-group-and-subscription.md)」を参照してください。

サポート プランを譲渡することはできません。 サポート プランがある場合は、古い契約についてのそれをキャンセルしてから、新しい契約について新たに購入してください。

次の表では、Dev/Test 製品は示していません。 Dev/Test 製品の譲渡は、他の製品の種類と同じ方法で処理されます。 たとえば、EA の Dev/Test 製品の譲渡は、EA 製品の譲渡の方法で処理されます。

> [!NOTE]
> ほとんどのサポートされている製品の譲渡で、予約が譲渡されます。 ただし、次の表に示すように、予約が譲渡されない譲渡がいくつかあります。

| 譲渡元 (現在) の製品契約の種類 | 譲渡先 (将来) の製品契約の種類 | メモ |
| --- | --- | --- |
| EA | MOSP (PAYG) | <ul><li>EA 登録から MOSP サブスクリプションへの譲渡には、[課金サポート チケット](https://azure.microsoft.com/support/create-ticket/)が必要です。 <li> 予約は自動的に譲渡されず、それらの譲渡もサポートされていません。 |
| EA | MCA - 個人 | <ul><li>詳細については、「[Azure サブスクリプションの課金所有権を Microsoft 顧客契約で使用するために譲渡する](mca-request-billing-ownership.md)」を参照してください。 <li> 予約は自動的に譲渡されないため、[課金サポート チケット](https://azure.microsoft.com/support/create-ticket/)を開いてそれらを譲渡する必要があります。 |
| EA | EA | <ul><li>EA 登録間の譲渡には、[課金サポート チケット](https://azure.microsoft.com/support/create-ticket/)が必要です。 <li>予約は登録間で自動的に譲渡されないため、[課金サポート チケット](https://azure.microsoft.com/support/create-ticket/)を開いてそれらを譲渡する必要があります。 <li> 同じ登録内での譲渡は、アカウント所有者の変更と同じアクションです。 詳細については、[EA サブスクリプションまたはアカウントの所有権の変更](ea-portal-administration.md#change-azure-subscription-or-account-ownership)に関するページを参照してください。 |
| EA | WPA - エンタープライズ | <ul><li> すべての登録製品の譲渡は、EA からの MCA 移行プロセスの一部として実行されます。 詳細については、「[Microsoft 顧客契約用の課金アカウントでの Enterprise Agreement タスクを実行する](mca-enterprise-operations.md)」を参照してください。 <li> 登録に含まれるすべての製品ではなく、特定の製品を譲渡する場合は、「[Azure サブスクリプションの課金所有権を Microsoft 顧客契約で使用するために譲渡する](mca-request-billing-ownership.md)」を参照してください。 |
| EA | MPA | <ul><li> [Azure Expert マネージド サービス プロバイダー (MSP)](https://partner.microsoft.com/membership/azure-expert-msp) として認定されている CSP 直接請求パートナーのみ、ダイレクト Enterprise Agreement (EA) があるお客様に対して Azure 製品の譲渡を要求できます。 詳細については、「[MPA アカウントに Azure サブスクリプションの課金所有権を取得する](mpa-request-ownership.md)」を参照してください。 製品の譲渡は、Microsoft 顧客契約 (MCA) に同意し、CSP プログラム付きの Azure プランを購入したお客様にのみ許可されます。 <li> 制限と制約があります。 詳細については、「[EA サブスクリプションを CSP パートナーに譲渡する](transfer-subscriptions-subscribers-csp.md#transfer-ea-subscriptions-to-a-csp-partner)」を参照してください。  |
| MCA - 個人 | MOSP (PAYG) | <ul><li> 詳細については、「[Azure サブスクリプションの課金所有権を別のアカウントに譲渡する](billing-subscription-transfer.md)」を参照してください。 <li> 予約は自動的に譲渡されず、それらの譲渡もサポートされていません。 |
| MCA - 個人 | MCA - 個人 | <ul><li>詳細については、「[Azure サブスクリプションの課金所有権を Microsoft 顧客契約で使用するために譲渡する](mca-request-billing-ownership.md)」を参照してください。 <li> セルフサービスの予約譲渡がサポートされています。 |
| MCA - 個人 | EA | <ul><li> 詳細については、「[サブスクリプションを EA に譲渡する](mosp-ea-transfer.md#transfer-the-subscription-to-the-ea)」を参照してください。 <li> セルフサービスの予約譲渡がサポートされています。 |
| MCA - 個人 | WPA - エンタープライズ | <ul><li> 詳細については、「[Azure サブスクリプションの課金所有権を Microsoft 顧客契約で使用するために譲渡する](mca-request-billing-ownership.md)」を参照してください。<li> セルフサービスの予約譲渡がサポートされています。 |
| WPA - エンタープライズ | MOSP | <ul><li> [課金サポート チケット](https://azure.microsoft.com/support/create-ticket/)が必要です。 <li> 予約は自動的に譲渡されず、それらの譲渡もサポートされていません。 |
| WPA - エンタープライズ | MCA - 個人 | <ul><li> 詳細については、「[Azure サブスクリプションの課金所有権を Microsoft 顧客契約で使用するために譲渡する](mca-request-billing-ownership.md)」を参照してください。 <li> 予約は自動的に譲渡されないため、[課金サポート チケット](https://azure.microsoft.com/support/create-ticket/)を開いてそれらを譲渡する必要があります。 |
| WPA - エンタープライズ | WPA - エンタープライズ | <ul><li> 詳細については、「[Azure サブスクリプションの課金所有権を Microsoft 顧客契約で使用するために譲渡する](mca-request-billing-ownership.md)」を参照してください。 <li> 予約は自動的に譲渡されないため、[課金サポート チケット](https://azure.microsoft.com/support/create-ticket/)を開いてそれらを譲渡する必要があります。  |
| CSP での以前の Azure オファー | CSP での以前の Azure オファー | <ul><li> [課金サポート チケット](https://azure.microsoft.com/support/create-ticket/)が必要です。 <li> 予約は自動的に譲渡されず、それらの譲渡もサポートされていません。 |
| CSP での以前の Azure オファー | MPA | 詳細については、「[顧客の Azure サブスクリプションを別の CSP に譲渡する (Azure プランで)](/partner-center/transfer-azure-subscriptions-under-azure-plan)」を参照してください。 |
| MPA | EA | <ul><li> 自動譲渡はサポートされていません。 すべての譲渡で、既存の MPA 製品から新しく作成されたまたは既存の EA 製品にリソースを手動で移動する必要があります。 <li> 「[リソースの譲渡を実行する](#perform-resource-transfers)」セクションの情報を使用してください。 <li> 予約は自動的に譲渡されず、それらの譲渡もサポートされていません。 |
| MPA | MPA | <ul><li> 詳細については、「[顧客の Azure サブスクリプションを別の CSP に譲渡する (Azure プランで)](/partner-center/transfer-azure-subscriptions-under-azure-plan)」を参照してください。 <li> 予約は自動的に譲渡されず、それらの譲渡もサポートされていません。 |
| MOSP (PAYG) | MOSP (PAYG) | <ul><li> サブスクリプションの課金所有者を変更する場合は、「[Azure サブスクリプションの課金所有権を別のアカウントに譲渡する](billing-subscription-transfer.md)」を参照してください。 <li> 予約は自動的に譲渡されないため、[課金サポート チケット](https://azure.microsoft.com/support/create-ticket/)を開いてそれらを譲渡する必要があります。  |
| MOSP (PAYG) | MCA - 個人 | <ul><li> 詳細については、「[Azure サブスクリプションの課金所有権を Microsoft 顧客契約で使用するために譲渡する](mca-request-billing-ownership.md)」を参照してください。 <li> セルフサービスの予約譲渡がサポートされています。 |
| MOSP (PAYG) | EA | 詳細については、「[サブスクリプションを EA に譲渡する](mosp-ea-transfer.md#transfer-the-subscription-to-the-ea)」を参照してください。 |
| MOSP (PAYG) | WPA - エンタープライズ | <ul><li> 詳細については、「[Azure サブスクリプションの課金所有権を Microsoft 顧客契約で使用するために譲渡する](mca-request-billing-ownership.md)」を参照してください。 <li> セルフサービスの予約譲渡がサポートされています。 |

## <a name="perform-resource-transfers"></a>リソースの譲渡を実行する

一部の製品譲渡では、サブスクリプション間で Azure リソースを手動で移動する必要があります。 リソースを移動すると、ダウンタイムが発生する可能性があり、VM、NSG、App Services などの Azure リソースの種類を移動するためにはさまざまな制限があります。

Microsoft では、サブスクリプション間でリソースを自動的に移動するツールを提供していません。 必要に応じて、サブスクリプション間で Azure リソースを手動で移動する必要があります。 詳細については、「[リソースを新しいリソース グループまたはサブスクリプションに移動する](../../azure-resource-manager/management/move-resource-group-and-subscription.md)」を参照してください。 移動するリソースが多い場合は、追加の時間と計画が必要です。

## <a name="other-planning-considerations"></a>計画に関するその他の考慮事項

製品の譲渡を開始する前に、次のセクションを読んで、その他の考慮事項の詳細を確認してください。

### <a name="resources-transfer-with-subscriptions"></a>サブスクリプションによるリソースの譲渡

課金所有権だけを変更する場合、リソースは影響を受けません。 ただし、リソースの移動またはサービス テナントの変更を行う場合は、リソースが影響を受ける可能性があります。

### <a name="transfer-a-product-from-one-account-to-another"></a>製品をあるアカウントから別のアカウントに譲渡する

2 つのアカウントの管理者の場合、アカウント間で製品を譲渡できます。 概念上、そうしたアカウントは 2 人の異なるユーザーのアカウントと見なされるので、アカウント間で製品を譲渡できます。

製品の譲渡に必要な手順を確認するには、「[Azure サブスクリプションの課金所有権を譲渡する」](billing-subscription-transfer.md)を参照してください。

### <a name="transferring-a-product-shouldnt-create-downtime"></a>製品の譲渡でダウンタイムが発生しないようにする

同じ Azure AD テナント内のアカウントに製品を譲渡する場合、そのサブスクリプションで実行されているリソースに影響は生じません。 ただし、PowerShell に保存されたコンテキスト情報は更新されないため、それをクリアするか、設定を変更しなければならない場合があります。 リソースの移動またはサービス テナントの変更を行う場合、リソースが影響を受ける可能性があります。

### <a name="new-account-usage-and-billing-history"></a>新しいアカウントの使用状況と請求履歴

新しいアカウントについてユーザーが入手できる情報は、譲渡時から開始する使用状況と請求履歴のみです。 使用状況と請求履歴は、製品と一緒に譲渡されません。

### <a name="remaining-product-credits"></a>製品クレジット残高

Visual Studio または Microsoft Partner Network 製品をお持ちの場合は、毎月クレジットを受け取ります。 クレジットは、新しいアカウントで製品と共に繰り越されません。 譲渡要求を受け取ったユーザーが、譲渡要求を受け入れるには、Visual Studio ライセンスが必要になります。 製品には、ユーザーのアカウントで利用可能な Visual Studio クレジットが使用されます。 詳細については、[Visual Studio と Partner Network のサブスクリプションの譲渡](billing-subscription-transfer.md#transfer-visual-studio-and-partner-network-subscriptions)に関するセクションを参照してください。

### <a name="users-keep-access-to-transferred-resources"></a>譲渡されたリソースへのアクセスをユーザーが保持する

課金所有権が譲渡されても、製品内のリソースにアクセス権を持つユーザーにはそのアクセス権が残ることに注意してください。 しかし、[管理者ロール](add-change-subscription-administrator.md)と [Azure ロールの割り当て](../../role-based-access-control/role-assignments-portal.md)が削除される可能性があります。 自分のアカウントが製品のテナントとは異なる Azure AD テナント内にあり、譲渡要求を送信したユーザーが製品を自分のアカウントのテナントに移行した場合、アクセス権が失われます。

Azure portal で、製品内のリソースにアクセスできる Azure ロールが割り当てられているユーザーを表示できます。 [Azure portal の [サブスクリプション] ページ](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)にアクセスします。 次に、確認する製品を選択し、左側のウィンドウで **[アクセス制御 (IAM)]** を選択します。 次に、ページの上部で **[ロールの割り当て]** を選択します。 [ロールの割り当て] ページに、製品に対するアクセス権が付与されているすべてのユーザーが一覧表示されます。

[Azure ロールの割り当て](../../role-based-access-control/role-assignments-portal.md)が譲渡中に削除された場合でも、元の所有者アカウントのユーザーは、次のような他のセキュリティ メカニズムを通じて、引き続き製品にアクセスできます。

- サブスクリプションのリソースに対する管理者権限をユーザーに付与する管理証明書。 詳細については、「[Azure の管理証明書の作成とアップロード](../../cloud-services/cloud-services-certs-create.md)」をご覧ください。
- Storage などのサービス用のアクセス キー。 詳細については、「[Azure ストレージ アカウントについて](../../storage/common/storage-account-create.md)」を参照してください。
- Azure Virtual Machines などのサービス用のリモート アクセス資格情報。

譲渡先でリソースへのアクセスを制限する必要がある場合、サービスに関連付けられているすべてのシークレットの更新を検討する必要があります。 ほとんどのリソースを更新することができます。 [Azure portal](https://portal.azure.com/) にサインインし、[ハブ] メニューで **[すべてのリソース]** を選択します。 次に、リソースを選択します。 次に、リソースのページで **[設定]** を選択します。 ここで、既存のシークレットを表示して更新できます。

### <a name="you-pay-for-usage-when-you-receive-ownership"></a>所有権を譲渡された場合は、使用量に応じて課金されます

お使いのアカウントでは、譲渡時点より後に報告された使用量についてお支払いいただくことになります。 譲渡する前に発生したにもかかわらず、譲渡後に報告される使用量もあります。 この使用量は、お客様のアカウントの請求書に記載されます。

### <a name="transfer-enterprise-agreement-product-ownership"></a>Enterprise Agreement 製品の所有権を譲渡する

元のアカウント所有者が組織の一員ではなくなった後でも、エンタープライズ管理者は、任意のアカウントのアカウント所有権を更新できます。 Azure Enterprise Agreement アカウントの譲渡の詳細については、「[Azure エンタープライズ転送](ea-transfers.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- 「[リソースを新しいリソース グループまたはサブスクリプションに移動する](../../azure-resource-manager/management/move-resource-group-and-subscription.md)」という記事を参照してください。
