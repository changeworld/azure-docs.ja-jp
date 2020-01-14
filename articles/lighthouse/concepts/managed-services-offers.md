---
title: Azure Marketplace のマネージド サービス オファー
description: マネージド サービス オファーにより、サービス プロバイダーは、Azure Marketplace で顧客にリソース管理オファーを販売することができます。
ms.date: 12/16/2019
ms.topic: conceptual
ms.openlocfilehash: 1b4f0d7457a74afe710a48f429cfe47535a9b122
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453583"
---
# <a name="managed-services-offers-in-azure-marketplace"></a>Azure Marketplace のマネージド サービス オファー

この記事では、[Azure Marketplace](https://azuremarketplace.microsoft.com) での新しいオファーの種類である、**マネージド サービス**について説明します。 マネージド サービス オファーでは、Azure の委任されたリソース管理機能を利用したリソース管理サービスを顧客に提供できます。 これらのオファーは、潜在顧客すべてに公開することも、1 名または複数の特定の顧客のみに公開することもできます。 こうしたマネージド サービスに関連する費用についてはお客様から顧客に直接請求していただくので、Microsoft から料金が課されることはありません。

## <a name="understand-managed-services-offers"></a>マネージド サービス オファーについて

マネージド サービス オファーにより、Azure の委任されたリソース管理に顧客をオンボードするプロセスを効率化できます。 顧客は、Azure Marketplace でオファーを購入すると、オンボードするサブスクリプションやリソース グループを指定できるようになります。 先に、**Microsoft.ManagedServices** リソース プロバイダーを手動で登録することにより、サブスクリプションのオンボードを承認する必要があることに注意してください。

その後、組織内のユーザーは、[Cloud パートナー ポータル](https://cloudpartner.azure.com/)でオファーを作成するときに定義したアクセス権に従って、組織のテナント内からそれらのリソースの管理タスクを実行できるようになります。 これは、Azure の委任されたリソース管理を使用して顧客のリソースにアクセスできる Azure AD ユーザー、グループ、サービス プリンシパルが指定されているマニフェストと、それらのアクセス レベルが定義されているロールによって、行われます。 個々のユーザー アカウントやアプリケーション アカウントではなく、Azure AD グループに権限を割り当てるので、アクセス要件が変わった場合にユーザーを個別に追加、削除できます。

## <a name="public-and-private-offers"></a>パブリック オファーとプライベート オファー

各マネージド サービス オファーには、1 つ以上のプランが含まれます。 プランはプライベートとパブリックのいずれかにできます。 

オファーの対象を特定の顧客に限定する場合は、プライベート プランを公開します。 そうすれば、指定したサブスクリプション ID でのみそのプランを購入できるようになります。 詳細については、「[プライベート オファー](../../marketplace/private-offers.md)」を参照してください。

パブリック プランでは、新しい顧客に向けてサービスを宣伝できます。 一般に、顧客のテナントへのアクセスが制限付きでかまわない場合には、こちらの方が適しています。 顧客との関係構築後、顧客がお客様の組織に追加のアクセス権を付与することに決めた場合は、お客様がその顧客のみを対象として新しいプライベート プランを公開するか、[Azure Resource Manager テンプレート](../how-to/onboard-customer.md)を使用して顧客を追加のアクセスにオンボードしてください。

必要に応じて、同じオファーにパブリック プランとプライベート プランの両方を含めることができます。

> [!IMPORTANT]
> プランをパブリックとして公開した後でプライベートに変更することはできません。 どの顧客がプランを受け入れて、リソースを委任できるようにするかを制御するには、プライベート プランを使用します。 パブリック プランの場合、対象範囲を特定の顧客に制限したり、対象となる顧客数を限定したりできません (ただし、必要に応じてプランの販売を完全に中止することはできます)。 顧客がプランを受け入れたときに、委任を拒否または削除するメカニズムは現時点ではありません。ただし、いつでも顧客に連絡して、[アクセスを削除](../how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers)するように依頼することはできます。

## <a name="publish-managed-service-offers"></a>マネージド サービス オファーの公開

マネージド サービス オファーの公開方法については、「[Azure Marketplace にマネージド サービス オファーを公開する](../how-to/publish-managed-services-offers.md)」を参照してください。 Cloud パートナー ポータルを使用した Azure Marketplace への公開に関する一般的な情報については、「[Azure Marketplace と AppSource の公開ガイド](../../marketplace/marketplace-publishers-guide.md)」および「[Azure Marketplace と AppSource Marketplace のオファーを管理する](../../marketplace/cloud-partner-portal/manage-offers/cpp-manage-offers.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- 「[Azure の委任されたリソース管理](azure-delegated-resource-management.md)」および「[テナント間の管理エクスペリエンス](cross-tenant-management-experience.md)」を確認します。
- Azure Marketplace に[マネージド サービス オファーを公開します](../how-to/publish-managed-services-offers.md)。
