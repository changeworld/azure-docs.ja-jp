---
title: Azure の委任されたリソース管理
description: マネージド サービス オファーにより、サービス プロバイダーは、Azure Marketplace で顧客にリソース管理オファーを販売することができます。
ms.date: 01/17/2020
ms.topic: conceptual
ms.openlocfilehash: 1fc3d95b1becccc014afeb4621d17770a03e38a4
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/17/2020
ms.locfileid: "76263916"
---
# <a name="azure-delegated-resource-management"></a>Azure の委任されたリソース管理

Azure の委任されたリソース管理は、Azure Lighthouse の主要なコンポーネントの 1 つです。 Azure の委任されたリソース管理を使用すると、サービス プロバイダーは顧客のエンゲージメントとオンボードのエクスペリエンスを簡略化しながら、機敏かつ正確に委任されたリソースを大規模に管理できます。

## <a name="what-is-azure-delegated-resource-management"></a>Azure の委任されたリソース管理とは

Azure の委任されたリソース管理を使用すると、あるテナントから別のテナントにリソースを論理的に投影できます。 これにより、1 つの Azure Active Directory (Azure AD) テナントの承認されたユーザーは、顧客に属するさまざまな Azure AD テナントで管理操作を実行できるようになります。 サービス プロバイダーは、独自の Azure AD テナントにサインインし、委任された顧客のサブスクリプションとリソース グループに対して承認を行うことができます。 これにより、個々の顧客テナントにサインインしなくても、顧客に代わって管理操作を実行できます。

> [!NOTE]
> Azure の委任されたリソース管理は、[独自の Azure AD テナントを複数所有する企業内](enterprise.md)で、テナント間の管理を簡略化するためにも使用できます。

Azure の委任されたリソース管理を使用すると、承認されたユーザーは、顧客のテナントにアカウントを持っていない場合や、顧客のテナントの共同所有者でない場合でも、顧客のサブスクリプションのコンテキストで直接作業できます。 また、Azure portal の[新しい **[マイ カスタマー]** ページで、すべての委任された顧客サブスクリプションを表示および管理する](../how-to/view-manage-customers.md)こともできます。

[テナント間の管理エクスペリエンス](cross-tenant-management-experience.md)により、Azure Policy、Azure Security Center などの Azure 管理サービスで、より効率的に作業を行うことができます。 サービス プロバイダーのすべてのアクティビティは、サービス プロバイダーと顧客両方のテナントに格納されるアクティビティ ログで追跡されます。 これは、顧客とサービス プロバイダーの両方が、変更に関連付けられたユーザーを簡単に識別できることを意味します。

顧客を Azure の委任されたリソース管理にオンボードすると、顧客は Azure portal の新しい **[サービスプロバイダー]** ページにアクセスできるようになります。そこでは、[オファー、サービス プロバイダー、委任されたリソースを確認して管理する](../how-to/view-manage-service-providers.md)ことができます。 顧客がサービス プロバイダーへのアクセスを取り消したい場合は、いつでもここでそれを行うことができます。

[新しいマネージド サービス オファーの種類を Azure Marketplace に発行する](../how-to/publish-managed-services-offers.md)ことで、顧客を Azure の委任されたリソース管理に簡単にオンボードできます。 または、[Azure Resource Manager テンプレートをデプロイして、オンボード プロセスを完了する](../how-to/onboard-customer.md)こともできます。

## <a name="how-azure-delegated-resource-management-works"></a>Azure の委任されたリソース管理の動作のしくみ

大まかに言えば、Azure の委任されたリソース管理のしくみは次のとおりです。

1. サービス プロバイダーは、顧客の Azure リソースを管理するためにグループ、サービス プリンシパル、またはユーザーが必要とするアクセス権 (ロール) を明らかにします。 アクセスの定義には、サービス プロバイダーのテナント ID と、オファーに必要なアクセスが含まれます。これは、[組み込みの **roleDefinition** の値](../../role-based-access-control/built-in-roles.md) (共同作成者、VM 共同作成者、閲覧者など) にマップされるテナントの **principalId** id を使用して定義されます。
2. このアクセスを指定し、次の 2 つの方法のいずれかで Azure の委任されたリソース管理に顧客をオンボードします。
   - 顧客が受け入れる [Azure Marketplace の管理されたサービス オファーを発行する](../how-to/publish-managed-services-offers.md) (プライベートまたはパブリック)
   - 1 つ以上の特定のサブスクリプションまたはリソース グループについて、[顧客のテナントに Azure Resource Manager テンプレートをデプロイする](../how-to/onboard-customer.md)
3. 顧客がオンボードされたら、承認されたユーザーは、定義されたアクセス権に基づいて、特定の顧客のスコープで、サービス プロバイダーのテナントにサインインして管理タスクを実行できます。

> [!NOTE]
> 異なるクラウド間での 2 つのテナント間のサブスクリプションの委任はサポートされていません。

## <a name="support-for-azure-delegated-resource-management"></a>Azure の委任されたリソース管理のサポート

Azure の委任されたリソース管理に関するヘルプが必要な場合は、Azure portal でサポート リクエストを開くことができます。 **[問題の種類]** では、 **[技術]** を選択します。 サブスクリプションを選択し、 **[Delegated Resource Management]\(委任されたリソース管理\)** ( **[管理と監視]** の下) を選択します。

## <a name="next-steps"></a>次のステップ

- [テナント間の管理エクスペリエンス](cross-tenant-management-experience.md)について学習します。
- [Azure Marketplace でのマネージド サービス オファー](managed-services-offers.md)について学習します。