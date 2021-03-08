---
title: サブスクライバーと CSP の間で Azure サブスクリプションを譲渡する
description: サブスクライバーと CSP の間で Azure サブスクリプションを譲渡する方法について説明します。
author: bandersmsft
ms.reviewer: dhgandhi
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 02/11/2021
ms.author: banders
ms.openlocfilehash: 63fbf76b2211e530707f3598d176b646c317cc53
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2021
ms.locfileid: "100363051"
---
# <a name="transfer-azure-subscriptions-between-subscribers-and-csps"></a>サブスクライバーと CSP の間で Azure サブスクリプションを譲渡する

この記事では、クラウド ソリューション プロバイダー (CSP) パートナーと CSP の顧客との間で、Azure サブスクリプションを譲渡するために使用される手順の概要について説明します。 ここに記載されている情報は、パートナーとの連携に役立つ Azure サブスクライバーを対象としています。 Microsoft パートナーが転送プロセスに使用する情報については、「[顧客の Azure サブスクリプションを別のパートナーに転送する方法について学習する](/partner-center/switch-azure-subscriptions-to-a-different-partner)」に記載されています。

移転リクエストを開始する前に、残しておきたいコスト情報や課金情報があれば、それらをダウンロードまたはエクスポートしておく必要があります。 課金情報や使用情報がサブスクリプションと一緒に移転されることはありません。 コスト管理データのエクスポートの詳細については、[データのエクスポートと管理](../costs/tutorial-export-acm-data.md)に関するページを参照してください。 請求書データと利用状況データのダウンロードについて詳しくは、「[Azure の請求書と毎日の使用状況データをダウンロードまたは表示する](download-azure-invoice-daily-usage-date.md)」を参照してください。

既存の予約がある場合、サブスクリプションの移転後は適用されなくなります。 必ず[予約の取り消しと払い戻し](../reservations/exchange-and-refund-azure-reservations.md)を行ったうえで、サブスクリプションを移転してください。

## <a name="transfer-ea-subscriptions-to-a-csp-partner"></a>EA サブスクリプションを CSP パートナーに譲渡する

[Azure Expert マネージド サービス プロバイダー (MSP)](https://partner.microsoft.com/membership/azure-expert-msp) として認定されている CSP 直接請求パートナーは、ダイレクト Enterprise Agreement (EA) を使用している顧客に対して Azure サブスクリプションの譲渡を要求できます。 サブスクリプションの譲渡は、Microsoft 顧客契約 (MCA) に同意し、CSP プログラム付きの Azure プランを購入した顧客に対してのみ許可されます。

要求が承認されると、CSP は顧客に対して結合された請求書を提供できます。 CSP のサブスクリプション譲渡の詳細については、「[MPA アカウントに Azure サブスクリプションの課金所有権を取得する](mpa-request-ownership.md)」を参照してください。

>[!IMPORTANT]
> EA サブスクリプションを CSP パートナーに譲渡した後は、以前に EA サブスクリプションに適用されたすべてのクォータの増加が既定値にリセットされます。 サブスクリプションの譲渡後に追加のクォータが必要な場合は、CSP プロバイダーに[クォータの増加](../../azure-portal/supportability/regional-quota-requests.md)の要求を送信してもらってください。 

## <a name="other-subscription-transfers-to-a-csp-partner"></a>CSP パートナーへの他のサブスクリプション譲渡

他の Azure サブスクリプションを CSP パートナーに譲渡するには、サブスクライバーがソース サブスクリプションから CSP サブスクリプションにリソースを移動する必要があります。 サブスクリプション間でリソースを移動するには、次のガイダンスに従います。

1. 顧客との[リセラー関係](/partner-center/request-a-relationship-with-a-customer)を確立します。 [CSP リージョン承認の概要](/partner-center/regional-authorization-overview)を参照して、顧客とパートナーの両方のテナントが同じ承認リージョン内にあることを確認します。
1. CSP パートナーと協力して、ターゲットの Azure CSP サブスクリプションを作成します。
1. ソース サブスクリプションとターゲットの CSP サブスクリプションが同じ Azure Active Directory (Azure AD) テナントにあることを確認します。  
    Azure CSP サブスクリプションの Azure AD テナントを変更することはできません。 その代わりに、ソース サブスクリプションを CSP Azure AD テナントに追加するか、関連付ける必要があります。 詳細については、「[Azure サブスクリプションを Azure Active Directory テナントに関連付けるまたは追加する](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)」を参照してください。
    > [!IMPORTANT]
    > - サブスクリプションを別の Azure AD ディレクトリに関連付けると、[Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md) を使用してロールが割り当てられているユーザーはアクセスできなくなります。 サービス管理者や共同管理者などの従来のサブスクリプション管理者もアクセスできなくなります。
    > - そのサブスクリプションが別のディレクトリに関連付けられている場合は、ポリシー割り当てもサブスクリプションから削除されます。
1. 譲渡を実行するために使用するユーザー アカウントには、両方のサブスクリプションで [Azure RBAC](add-change-subscription-administrator.md) 所有者アクセス権が必要です。
1. 開始する前に、すべての Azure リソースをソース サブスクリプションから宛先サブスクリプションに移動できることを[検証](/rest/api/resources/resources/validatemoveresources)します。  
    サブスクリプション間で移動することができない Azure リソースもあります。 移動できる Azure リソースの完全な一覧については、「[リソースの移動操作のサポート](../../azure-resource-manager/management/move-support-resources.md)」を参照してください。
    > [!IMPORTANT]
    >  - Azure CSP は Azure Resource Manager のリソースにのみ対応しています。 ソース サブスクリプションのいずれかの Azure リソースが Azure クラシック デプロイ モデルを使って作成されている場合は、移行前にそれらを [Azure Resource Manager](/azure/cloud-solution-provider/migration/ea-payg-to-azure-csp/ea-open-direct-asm-to-arm) に移す必要があります。 Web ページを表示するには、パートナーである必要があります。

1. すべてのソース サブスクリプション サービスが Azure Resource Manager モデルを使用していることを確認します。 次に、[Azure Resource Move](../../azure-resource-manager/management/move-resource-group-and-subscription.md) を使用して、ソース サブスクリプションから宛先サブスクリプションにリソースを譲渡します。
    > [!IMPORTANT]
    >  - サブスクリプション間で Azure リソースを移動すると、サブスクリプションのリソースに基づいてサービスのダウンタイムが発生する可能性があります。

## <a name="transfer-csp-subscription-to-other-offer"></a>CSP サブスクリプションを他のオファーに譲渡する

他のサブスクリプションを CSP パートナーから他の Azure オファーに譲渡するには、サブスクライバーがソース CSP サブスクリプションとターゲット サブスクリプションの間でリソースを移動する必要があります。

1. ターゲットの Azure サブスクリプションを作成します。
1. 移行元サブスクリプションと移行先サブスクリプションが同じ Azure Active Directory (Azure AD) テナントにあることを確認します。 Azure AD テナントの変更について詳しくは、「[Azure サブスクリプションを Azure Active Directory テナントに関連付けるまたは追加する](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)」を参照してください。
    CSP サブスクリプションではディレクトリの変更オプションがサポートされないことに注意してください。 たとえば、CSP から従量課金制のサブスクリプションに譲渡するとします。 従量課金制のサブスクリプションのディレクトリを変更して、そのディレクトリに一致させる必要があります。

    > [!IMPORTANT]
    >  - サブスクリプションを別のディレクトリに関連付けると、[Azure RBAC](../../role-based-access-control/role-assignments-portal.md) を使用してロールが割り当てられているユーザーはアクセスできなくなります。 サービス管理者や共同管理者などの従来のサブスクリプション管理者もアクセスできなくなります。
    >  - そのサブスクリプションが別のディレクトリに関連付けられている場合は、ポリシー割り当てもサブスクリプションから削除されます。

1. 譲渡を実行するために使用するユーザー アカウントには、両方のサブスクリプションで [Azure RBAC](add-change-subscription-administrator.md) 所有者アクセス権が必要です。
1. 開始する前に、すべての Azure リソースをソース サブスクリプションから宛先サブスクリプションに移動できることを[検証](/rest/api/resources/resources/validatemoveresources)します。
    > [!IMPORTANT]
    >  - サブスクリプション間で移動することができない Azure リソースもあります。 移動できる Azure リソースの完全な一覧については、「[リソースの移動操作のサポート](../../azure-resource-manager/management/move-support-resources.md)」を参照してください。

1. [Azure Resource Move](../../azure-resource-manager/management/move-resource-group-and-subscription.md) を使用して、ソース サブスクリプションから宛先サブスクリプションにリソースを譲渡します。
    > [!IMPORTANT]
    >  - サブスクリプション間で Azure リソースを移動すると、サブスクリプションのリソースに基づいてサービスのダウンタイムが発生する可能性があります。

## <a name="next-steps"></a>次のステップ
- [MPA アカウントに Azure サブスクリプションの課金所有権を取得する](mpa-request-ownership.md)。
- [Azure Billing を使用したアカウントとサブスクリプションの管理](../index.yml)方法を確認する。
