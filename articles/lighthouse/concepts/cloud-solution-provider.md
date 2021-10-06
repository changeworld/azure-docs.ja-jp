---
title: Cloud Solution Provider プログラムの考慮事項
description: CSP パートナーにとって、Azure の委任されたリソース管理は、粒度の細かいアクセス許可を実現することによってセキュリティと制御を強化する効果があります。
ms.date: 09/13/2021
ms.topic: conceptual
ms.openlocfilehash: 6a53ec7cfc2e9f82664e57eecf5bf3f587444134
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128629106"
---
# <a name="azure-lighthouse-and-the-cloud-solution-provider-program"></a>Azure Lighthouse と Cloud Solution Provider プログラム

[CSP (クラウド ソリューション プロバイダー)](/partner-center/csp-overview) パートナーである場合、CSP プログラムを通じて顧客用に作成された Azure サブスクリプションには、[代理で管理 (AOBO)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) 機能を使用してあらかじめアクセスできるようになっています。 このアクセスにより、顧客のサブスクリプションを直接サポートし、構成し、管理することができます。

[Azure Lighthouse](../overview.md) では、Azure の委任されたリソース管理と AOBO を併用できます。 より粒度の細かいアクセス許可をユーザーに使用できるようになるため、セキュリティが向上すると共に、不要なアクセスを減らすことにつながります。 また、ユーザーは、テナントに対する 1 つのログインを使用して、複数の顧客サブスクリプションにまたがって作業できるため、効率とスケーラビリティも向上します。

> [!TIP]
> 顧客のリソースを効果的に保護するため、Microsoft の[推奨セキュリティ プラクティス](recommended-security-practices.md)と[パートナーのセキュリティ要件](/partner-center/partner-security-requirements)を確認し、それらに従うようにしてください。

## <a name="administer-on-behalf-of-aobo"></a>代理で管理 (AOBO)

AOBO を使用すると、テナント内の[管理エージェント](/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) ロールを持つすべてのユーザーが、CSP プログラムを通じて貴社が作成した Azure サブスクリプションへの AOBO アクセス権を持つことになります。 顧客のサブスクリプションにアクセスする必要のあるすべてのユーザーは、このグループのメンバーである必要があります。 AOBO では、異なる顧客と共同作業を行う別々のグループを柔軟に作成したり、グループやユーザーに対して個別のロールを有効にしたりすることはできません。

![AOBO を使用したテナント管理を示した図。](../media/csp-1.jpg)

## <a name="azure-lighthouse"></a>Azure Lighthouse

Azure Lighthouse を使用すると、次の図に示すように、さまざまなグループをさまざまな顧客またはロールに割り当てることができます。 ユーザーは [Azure の委任されたリソース管理](architecture.md)によって適切なレベルのアクセス権を持つことになるため、管理エージェント ロールを持つ (つまり、完全な AOBO アクセス権を持つ) ユーザーの数を減らすことができます。 これにより、顧客のリソースへの不要なアクセスが制限され、セキュリティを向上させることができます。 また、必要以上のアクセス権をユーザーに付与することなく、各ユーザーの職務に最も適した [Azure 組み込みロール](tenants-users-roles.md#role-support-for-azure-lighthouse)を使用して、大規模な多数の顧客を柔軟に管理できます。

![AOBO と Azure Lighthouse を使用したテナント管理を示す図。](../media/csp-2.jpg)

CSP プログラムを使用して作成したサブスクリプションをオンボードするには、[Azure Lighthouse へのサブスクリプションのオンボード](../how-to/onboard-customer.md)に関する記事で説明されている手順に従ってください。 テナント内に管理エージェント ロールを持つすべてのユーザーが、このオンボードを実行できます。

> [!TIP]
> プライベート プランを含む[マネージド サービス オファー](managed-services-offers.md)は、クラウド ソリューション プロバイダー (CSP) プログラムのリセラーを通じて確立されたサブスクリプションではサポートされません。 [Azure Resource Manager テンプレートを使用する](../how-to/onboard-customer.md)ことで、これらのサブスクリプションを Azure Lighthouse にオンボードできます。

> [!NOTE]
> [Azure portal の **[マイ カスタマー]** ページ](../how-to/view-manage-customers.md)に、 **[クラウド ソリューション プロバイダー (プレビュー)]** セクションが含まれるようになり、[Microsoft 顧客契約 (MCA) に署名](/partner-center/confirm-customer-agreement)し、[Azure プランに含まれている](/partner-center/azure-plan-get-started) CSP のお客様の課金情報とリソースが表示されます。 詳しくは、「[Microsoft Partner Agreement の課金アカウントの概要](../../cost-management-billing/understand/mpa-overview.md)」をご覧ください。
>
> CSP のお客様は、Azure Lighthouse にもオンボードされているかどうかにかかわらず、このセクションに表示されることがあります。 オンボードされている場合は、「[顧客と委任されたリソースを表示し、管理する](../how-to/view-manage-customers.md)」に説明されているように、 **[顧客]** セクションにも表示されます。 同様に、CSP 顧客は、Azure Lighthouse にオンボードするために、 **[マイ カスタマー]** の **[クラウド ソリューション プロバイダー (プレビュー)]** セクションに表示される必要はありません。

## <a name="next-steps"></a>次のステップ

- [テナント間の管理エクスペリエンス](cross-tenant-management-experience.md)について学習します。
- [サブスクリプションを Azure Lighthouse にオンボードする](../how-to/onboard-customer.md)方法について学習します。
- [Cloud Solution Provider プログラム](/partner-center/csp-overview)について学習します。
