---
title: パートナー ID をリンクして、委任されたリソースでパートナー獲得クレジットを有効にする
description: パートナー ID を関連付けて、Azure Lighthouse を通じて管理している顧客のリソースでパートナー獲得クレジット (PEC) を受け取る方法について説明します。
ms.date: 09/04/2020
ms.topic: how-to
ms.openlocfilehash: 0a9e7f51e90b38bad24eada5a665ca60bf43452f
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2020
ms.locfileid: "89493287"
---
# <a name="link-your-partner-id-to-enable-partner-earned-credit-on-delegated-resources"></a>パートナー ID をリンクして、委任されたリソースでパートナー獲得クレジットを有効にする

[Microsoft Partner Network](https://partner.microsoft.com/) のメンバーは、自分のパートナー ID と、委任された顧客リソースの管理に使用されている資格情報を、リンクすることができます。 これにより、顧客エンゲージメントを通して影響を追跡し、[管理サービスに対するパートナー獲得クレジット (PEC)](/partner-center/partner-earned-credit) を受け取ることができます。

[Azure Marketplace の管理サービス オファーに顧客をオンボードした](publish-managed-services-offers.md)場合、このリンクは、オファーの発行に使用されたパートナー センター アカウントに関連付けられている MPN ID を使用して、自動的に行われます。 これらの顧客向けに PEC を受け取るために、これ以上の操作は必要ありません。

[Azure Resource Management テンプレートを使用して顧客をオンボードした](onboard-customer.md)場合は、このリンクを作成する作業を行う必要があります。 そのためには、管理しているテナント内にある、オンボードされた各サブスクリプションにアクセスできる少なくとも 1 つのユーザー アカウントと、[MPN ID をリンク](../../cost-management-billing/manage/link-partner-id.md)します。

## <a name="associate-your-partner-id-when-you-onboard-new-customers"></a>新しい顧客をオンボードするときにパートナー ID を関連付ける

Azure Resource Manager テンプレートを使用して顧客をオンボードするときに、次のプロセスを使用してパートナー ID をリンクし、パートナー獲得クレジットを有効にします。 これらの手順を完了するには、自分の [MPN パートナー ID](/partner-center/partner-center-account-setup#locate-your-mpn-id) を把握しておく必要があります。

わかりやすくするために、テナントにサービス プリンシパル アカウントを作成して MPN ID に関連付け、[PEC の対象となる Azure の組み込みロール](https://docs.microsoft.com/partner-center/azure-roles-perms-pec)を使用してオンボードするすべての顧客へのアクセス権を付与することをお勧めします。

1. 管理しているテナント内に[サービス プリンシパル アカウントを作成](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)します。 この例では、このサービス プリンシパルの名前を "PEC オートメーション アカウント" にします。
1. そのサービス プリンシパル アカウントを使用して、管理しているテナント内の[パートナー ID にリンク](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id)します。 これを行う必要があるのは 1 回だけです。
1. [Azure Resource Manager テンプレートを使用して顧客をオンボードする](onboard-customer.md)ときに、[PEC の資格がある Azure 組み込みロール](https://docs.microsoft.com/partner-center/azure-roles-perms-pec)を持つユーザーとして、PEC オートメーション アカウントを含む承認を含めます。

この手順のようにすると、管理しているすべての顧客テナントがパートナー ID に関連付けられるため、それらの顧客に対して PEC を受け取ることができます。 PEC オートメーション アカウントを使用して、顧客テナントでの認証や操作を行う必要はありません。

## <a name="add-your-partner-id-to-previously-onboarded-customers"></a>以前にオンボードされた顧客にパートナー ID を追加する

既に顧客をオンボードしている場合、PEC オートメーション アカウントのサービス プリンシパルを追加するために、別のデプロイを実行する必要はありません。 代わりに、その顧客のテナントで作業するためのアクセス権が既に与えられているユーザー アカウントに、MPN ID を関連付けることができます。 [PEC の資格がある Azure 組み込みロール](https://docs.microsoft.com/partner-center/azure-roles-perms-pec)がアカウントに付与されていることを確認してください。

アカウントが、管理しているテナント内の[パートナー ID にリンク](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id)されると、その顧客に対する PEC を受け取ることができるようになります。

## <a name="confirm-partner-earned-credit"></a>パートナー獲得クレジットを確認する

[Azure portal で PEC の詳細を表示](/partner-center/partner-earned-credit-explanation#azure-cost-management)し、PEC の特典を受けたコストを確認することができます。

上記の手順に従っても、関連付けが表示されない場合は、Azure portal でサポート リクエストを開いてください。

## <a name="next-steps"></a>次の手順

- [Microsoft Partner Network](/partner-center/mpn-overview) への参加について確認する。
- [PEC の計算と支払いの方法](/partner-center/partner-earned-credit-explanation)を確認する。
- Azure Lighthouse に[顧客をオンボードする](onboard-customer.md)。
