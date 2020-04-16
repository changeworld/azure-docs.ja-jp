---
title: Azure Marketplace のマネージド サービス オファー
description: マネージド サービス オファーを使用すると、サービス プロバイダーは Azure Marketplace の顧客にリソース管理オファーを販売できます。
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: a0670bd74971132dcf243736bdf882a00154a942
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "80672425"
---
# <a name="managed-service-offers-in-azure-marketplace"></a>Azure Marketplace のマネージド サービス オファー

この記事では、[Azure Marketplace](https://azuremarketplace.microsoft.com) の**マネージド サービス** オファーの種類について説明します。 マネージド サービス オファーを使用すると、[Azure の委任されたリソース管理](azure-delegated-resource-management.md)を使用して、顧客にリソース管理サービスを提供できます。 これらのオファーをすべての潜在顧客から、または 1 社以上の特定の顧客からのみ使用可能にすることができます。 こうしたマネージド サービスに関連する費用についてはお客様から顧客に直接請求していただくので、Microsoft から料金が課されることはありません。

## <a name="understand-managed-service-offers"></a>マネージド サービス オファーを理解する

マネージド サービス オファーにより、Azure の委任されたリソース管理に顧客をオンボードするプロセスが効率化されます。 顧客は、Azure Marketplace でオファーを購入すると、オンボードするサブスクリプションやリソース グループを指定できるようになります。

その後、組織内のユーザーは、[Cloud パートナー ポータル](https://cloudpartner.azure.com/)でオファーを作成するときに定義されたアクセス権に従って、組織のテナント内からこれらのリソースに対して作業できるようになります。 これは、Azure の委任されたリソース管理を使用して顧客のリソースにアクセスできる Azure AD ユーザー、グループ、サービス プリンシパルが指定されているマニフェストと、それらのアクセス レベルが定義されているロールによって、行われます。 個々のユーザー アカウントやアプリケーション アカウントではなく、Azure AD グループに権限を割り当てるので、アクセス要件が変わった場合にユーザーを個別に追加、削除できます。

## <a name="public-and-private-offers"></a>パブリック オファーとプライベート オファー

各マネージド サービス オファーには、1 つ以上のプランが含まれます。 プランはプライベートとパブリックのいずれかにできます。

オファーの対象を特定の顧客に限定する場合は、プライベート プランを公開します。 そうすれば、指定したサブスクリプション ID でのみそのプランを購入できるようになります。 詳細については、「[プライベート オファー](../../marketplace/private-offers.md)」を参照してください。

パブリック プランでは、新しい顧客に向けてサービスを宣伝できます。 一般に、顧客のテナントへのアクセスが制限付きでかまわない場合には、こちらの方が適しています。 顧客との関係構築後、顧客がお客様の組織に追加のアクセス権を付与することに決めた場合は、お客様がその顧客のみを対象として新しいプライベート プランを公開するか、[Azure Resource Manager テンプレート](../how-to/onboard-customer.md)を使用して顧客を追加のアクセスにオンボードしてください。

必要に応じて、同じオファーにパブリック プランとプライベート プランの両方を含めることができます。

> [!IMPORTANT]
> プランをパブリックとして公開した後でプライベートに変更することはできません。 どの顧客がプランを受け入れて、リソースを委任できるようにするかを制御するには、プライベート プランを使用します。 パブリック プランの場合、対象範囲を特定の顧客に制限したり、対象となる顧客数を限定したりできません (ただし、必要に応じてプランの販売を完全に中止することはできます)。 オファーを発行し、カスタマーがそれを受け入れた後に[委任へのアクセス権を削除](../how-to/onboard-customer.md#remove-access-to-a-delegation)できるのは、 その**ロールの定義**セットに「[管理されたサービスの登録割り当て削除ロール](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)」の**承認**を含めた場合に限ります。 また、カスタマーに連絡して、[アクセスを削除](../how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers)するよう依頼することもできます。

## <a name="publish-managed-service-offers"></a>マネージド サービス オファーの公開

マネージド サービス オファーの公開方法については、「[Azure Marketplace にマネージド サービス オファーを公開する](../how-to/publish-managed-services-offers.md)」を参照してください。 Cloud パートナー ポータルを使用した Azure Marketplace への公開に関する一般的な情報については、[Azure Marketplace と AppSource の公開ガイド](../../marketplace/marketplace-publishers-guide.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

- 「[Azure の委任されたリソース管理](azure-delegated-resource-management.md)」および「[テナント間の管理エクスペリエンス](cross-tenant-management-experience.md)」を確認します。
- Azure Marketplace に[マネージド サービス オファーを公開します](../how-to/publish-managed-services-offers.md)。
