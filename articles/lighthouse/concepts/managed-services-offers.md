---
title: Azure Marketplace のマネージド サービス オファー
description: Azure Marketplace 内でマネージド サービス オファーを使用して、Azure Lighthouse 管理サービスを顧客に提供します。
ms.date: 09/08/2021
ms.topic: conceptual
ms.openlocfilehash: 5d96a23f1dbdba74eefbf4f483a441c25e2dd47b
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124732933"
---
# <a name="managed-service-offers-in-azure-marketplace"></a>Azure Marketplace のマネージド サービス オファー

この記事では、[Azure Marketplace](https://azuremarketplace.microsoft.com) の **マネージド サービス** オファーの種類について説明します。 マネージド サービス オファーを使用すると、[Azure Lighthouse](../overview.md) を介して顧客にリソース管理サービスを提供できます。 これらのオファーをすべての潜在顧客から、または 1 社以上の特定の顧客からのみ使用可能にすることができます。 こうしたマネージド サービスに関連する費用についてはお客様から顧客に直接請求していただくので、Microsoft から料金が課されることはありません。

## <a name="understand-managed-service-offers"></a>マネージド サービス オファーについて理解する

マネージド サービス オファーにより、Azure Lighthouse に顧客をオンボードするプロセスが効率化されます。 顧客は、Azure Marketplace でオファーを購入すると、オンボードするサブスクリプションやリソース グループを指定できるようになります。

オファーごとに、組織内のユーザーが顧客テナント内のリソースに対して作業する必要があるアクセス権を定義します。 これは、顧客のリソースにアクセスできる Azure Active Directory (Azure AD) ユーザー、グループ、サービス プリンシパルが指定されているマニフェストと、それらのアクセス レベルが定義されている[ロール](tenants-users-roles.md)を通じて行われます。

> [!NOTE]
> マネージド サービス オファーは、Azure Government およびその他の国内クラウドでは利用できない場合があります。

## <a name="public-and-private-offers"></a>パブリック オファーとプライベート オファー

各マネージド サービス オファーには、1 つ以上のプランが含まれます。 プランはプライベートとパブリックのいずれかにできます。

オファーの対象を特定の顧客に限定する場合は、プライベート プランを公開します。 そうすれば、指定したサブスクリプション ID でのみそのプランを購入できるようになります。 詳細については、「[プライベート オファー](../../marketplace/private-offers.md)」を参照してください。

> [!NOTE]
> プライベート オファーは、クラウド ソリューション プロバイダー (CSP) プログラムのリセラーを通じて確立されたサブスクリプションではサポートされていません。

パブリック プランでは、新しい顧客に向けてサービスを宣伝できます。 一般に、顧客のテナントへのアクセスが制限付きでかまわない場合には、こちらの方が適しています。 顧客との関係構築後、顧客がお客様の組織に追加のアクセス権を付与することに決めた場合は、お客様がその顧客のみを対象として新しいプライベート プランを公開するか、[Azure Resource Manager テンプレート](../how-to/onboard-customer.md)を使用して顧客を追加のアクセスにオンボードしてください。

必要に応じて、同じオファーにパブリック プランとプライベート プランの両方を含めることができます。

> [!IMPORTANT]
> プランをパブリックとして公開した後でプライベートに変更することはできません。 どの顧客がプランを受け入れて、リソースを委任できるようにするかを制御するには、プライベート プランを使用します。 パブリック プランの場合、対象範囲を特定の顧客に制限したり、対象となる顧客数を限定したりできません (ただし、必要に応じてプランの販売を完全に中止することはできます)。 オファーを発行し、カスタマーがそれを受け入れた後に [委任へのアクセス権を削除](../how-to/remove-delegation.md)できるのは、 その **ロールの定義** セットに「[管理されたサービスの登録割り当て削除ロール](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)」の **承認** を含めた場合に限ります。 また、カスタマーに連絡して、[アクセスを削除](../how-to/view-manage-service-providers.md#remove-service-provider-offers)するよう依頼することもできます。

## <a name="publish-managed-service-offers"></a>マネージド サービス オファーの公開

マネージド サービス オファーの公開方法については、「[Azure Marketplace にマネージド サービス オファーを発行する](../how-to/publish-managed-services-offers.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- Azure Lighthouse の[アーキテクチャ](architecture.md)と[テナント間の管理エクスペリエンス](cross-tenant-management-experience.md)について学習します。
- Azure Marketplace に[マネージド サービス オファーを公開](../how-to/publish-managed-services-offers.md)します。
