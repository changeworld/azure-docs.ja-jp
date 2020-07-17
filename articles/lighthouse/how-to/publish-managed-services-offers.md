---
title: Azure Marketplace にマネージド サービス オファーを発行する
description: Azure の委任されたリソース管理に顧客をオンボードするマネージド サービス オファーを発行する方法について説明します。
ms.date: 05/04/2020
ms.topic: conceptual
ms.openlocfilehash: 04da33d0232371f4175a935bf1e35925376babbc
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/05/2020
ms.locfileid: "82788741"
---
# <a name="publish-a-managed-service-offer-to-azure-marketplace"></a>Azure Marketplace にマネージド サービス オファーを発行する

この記事では、Partner Center の[コマーシャル マーケットプレース](../../marketplace/partner-center-portal/commercial-marketplace-overview.md) プログラムを使用して、パブリックまたはプライベートのマネージド サービス オファーを [Azure Marketplace](https://azuremarketplace.microsoft.com) に発行する方法について説明します。 オファーを購入した顧客は、[Azure の委任されたリソース管理](../concepts/azure-delegated-resource-management.md)のためにサブスクリプションとリソース グループをオンボードできます。

## <a name="publishing-requirements"></a>発行要件

オファーを作成して発行するには、[パートナー センターの有効なアカウント](../../marketplace/partner-center-portal/create-account.md)が必要です。 まだアカウントをお持ちでない場合は、[サインアップ プロセス](https://aka.ms/joinmarketplace)の手順に従って、パートナー センターのアカウントを作成し、コマーシャル マーケットプレース プログラムに登録します。

[マネージド サービス オファーの認定要件](https://docs.microsoft.com/legal/marketplace/certification-policies#7004-business-requirements)に従って、マネージド サービス オファーを発行するには、[Silver または Gold Cloud Platform コンピテンシー レベル](https://docs.microsoft.com/partner-center/learn-about-competencies)を取得しているか、または [Azure Expert MSP](https://partner.microsoft.com/membership/azure-expert-msp) である必要があります。

顧客エンゲージメント全体に対するお客様の影響を追跡するために、ご使用の Microsoft Partner Network (MPN) ID はお客様が発行したオファーに[自動的に関連付けられます](../../billing/billing-partner-admin-link-started.md)。

> [!NOTE]
> オファーを Azure Marketplace に発行しない場合は、Azure Resource Manager テンプレートを使用して、顧客を手動でオンボードできます。 詳細については、「[Azure の委任されたリソース管理に顧客をオンボードする](onboard-customer.md)」を参照してください。

## <a name="create-your-offer"></a>プランを作成する

提供する必要があるすべての情報と資産を含む、オファーを作成する方法の詳細については、「[管理サービス オファーの作成](../../marketplace/partner-center-portal/create-new-managed-service-offer.md)」を参照してください。

一般的な発行プロセスについては、「[Azure Marketplace と AppSource の公開ガイド](../../marketplace/marketplace-publishers-guide.md)」を参照してください。 さらに、「[商業マーケットプレースの認定ポリシー](https://docs.microsoft.com/legal/marketplace/certification-policies)」(特に「[マネージド サービス](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services)」セクション) も確認してください。

顧客がオファーを追加すると、1 つまたは複数のサブスクリプションまたはリソース グループを委任できるようになり、これらは [Azure の委任されたリソース管理のためにオンボード](#the-customer-onboarding-process)されます。

> [!IMPORTANT]
> マネージド サービス オファー内の各プランには、 **[Manifest Details]\(マニフェストの詳細\)** セクションが含まれています。そこでは、そのプランを購入したお客様に代わり、委任されたリソース グループやサブスクリプションへのアクセス権を持つ Azure Active Directory (Azure AD) エンティティをお客様のテナント内に定義します。 含まれるグループ (またはユーザーまたはサービス プリンシパル) は、そのプランを購入したすべての顧客に対して同じアクセス許可を持つことに注意する必要があります。 顧客ごとに異なる担当グループを割り当てるには、各顧客専用の個別の[プライベート プラン](../../marketplace/private-offers.md)を発行する必要があります。

## <a name="publish-your-offer"></a>実際のオファーを発行する

すべてのセクションの指定が済んだら、Azure Marketplace にオファーを発行します。 **[発行]** ボタンを選択して、お客様のオファーを一般公開するプロセスを始めます。 このプロセスの詳細については、[こちら](../../marketplace/partner-center-portal/create-new-managed-service-offer.md#publish)を参照してください。 

いつでも[更新済みバージョンのオファー](../..//marketplace/partner-center-portal/update-existing-offer.md)を公開できます。 たとえば、以前に公開されたオファーに新しいロールの定義を追加したい場合があります。 そうすると、オファーを既に追加している顧客には、Azure portal の [ **[サービス プロバイダー]** ](view-manage-service-providers.md) ページに、更新が利用可能であることを知らせるアイコンが表示されます。 各顧客は、[変更を確認](view-manage-service-providers.md#update-service-provider-offers)して、新しいバージョンに更新するかどうかを決定できます。 

## <a name="the-customer-onboarding-process"></a>顧客オンボーディング プロセス

顧客がオファーを追加すると、[1 つまたは複数の特定のサブスクリプションまたはリソース グループを委任](view-manage-service-providers.md#delegate-resources)できるようになります。これらは、Azure の委任されたリソース管理のためにオンボードされます。 顧客がオファーを承諾しても、まだリソースを委任していなければ、Azure portal の [ **[サービス プロバイダー]** ](view-manage-service-providers.md) ページの **[プロバイダーのオファー]** セクションの上部に注意書きが表示されます。

> [!IMPORTANT]
> 委任は、ゲスト以外のアカウントが、オンボード対象のサブスクリプションで[所有者の組み込みロール](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner)を持っている (またはオンボード対象のリソース グループを含む) 顧客のテナント内で実行する必要があります。 サブスクリプションを委任できるすべてのユーザーを表示するには、顧客のテナントのユーザーが Azure portal でサブスクリプションを選択し、 **[アクセス制御 (IAM)]** を開くと、[所有者ロールを持つすべてのユーザーを表示](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription)することができます。

顧客がサブスクリプション (またはサブスクリプション内の 1 つまたは複数のリソース グループ) を委任した後、そのサブスクリプションに **Microsoft.ManagedServices** リソース プロバイダーが登録され、テナント内のユーザーは、オファー内の承認に従って、委任されたリソースにアクセスできるようになります。

## <a name="next-steps"></a>次のステップ

- [コマーシャル マーケットプレース](../../marketplace/partner-center-portal/commercial-marketplace-overview.md)について説明します。
- [テナント間の管理エクスペリエンス](../concepts/cross-tenant-management-experience.md)について学習します。
- Azure portal の **[マイ カスタマー]** に移動して、[顧客を表示および管理](view-manage-customers.md)します。
