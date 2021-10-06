---
title: Azure Lighthouse のアーキテクチャ
description: Azure Lighthouse のテナントと、そのリレーションシップを有効にする顧客のテナントで作成されたリソースとの関係について説明します。
ms.date: 09/13/2021
ms.topic: conceptual
ms.openlocfilehash: 92890199b11da69fac58ec4ff2bccc1ab347d076
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128629125"
---
# <a name="azure-lighthouse-architecture"></a>Azure Lighthouse のアーキテクチャ

Azure Lighthouse は、サービス プロバイダーによる顧客のエンゲージメントとオンボードのエクスペリエンスを簡略化しながら、機敏かつ正確に委任されたリソースを大規模に管理する上で役立ちます。 承認されたユーザー、グループ、およびサービス プリンシパルは、顧客の Azure Active Directory (Azure AD) テナントにアカウントを持っていない場合や、顧客のテナントの共同所有者でない場合でも、顧客のサブスクリプションのコンテキストで直接作業できます。 このアクセスをサポートするために使用されるメカニズムは、Azure の委任されたリソース管理と呼ばれます。

:::image type="content" source="../media/delegation.jpg" alt-text="Azure の委任されたリソース管理を示す図。":::

> [!TIP]
> [独自の Azure AD テナントが複数存在する企業内](enterprise.md)で Azure Lighthouse を使用することで、テナント間の管理を簡素化することもできます。

このトピックでは、Azure Lighthouse のテナントと、そのリレーションシップを有効にする顧客のテナントで作成されたリソースとの関係について説明します。

## <a name="delegation-resources-created-in-the-customer-tenant"></a>顧客テナントで作成された委任リソース

顧客のサブスクリプションまたはリソース グループが Azure Lighthouse にオンボードされると、**登録定義** と **登録の割り当て** という 2 つのリソースが作成されます。 ユーザーは [API と管理ツール](cross-tenant-management-experience.md#apis-and-management-tool-support)を使用することで、これらのリソースにアクセスしたり、[Azure portal で](../how-to/view-manage-customers.md)操作したりすることができます。

### <a name="registration-definition"></a>登録定義

登録定義には、Azure Lighthouse オファーの詳細 (管理テナントの特定のユーザー、グループ、サービス プリンシパルに組み込みロールを割り当てる管理テナント ID と承認) が含まれています。

登録定義は、委任されたサブスクリプションごとに、または委任されたリソース グループを含むサブスクリプションごとに、サブスクリプション レベルで作成されます。 API を使用して登録定義を作成する場合は、サブスクリプション レベルで作業する必要があります。 たとえば、Azure PowerShell を使用する場合は、新しい登録定義 ([New-AzManagedServicesDefinition](/powershell/module/az.managedservices/new-azmanagedservicesdefinition)) を作成する前に New-AzureRmResourceGroupDeployment を使用するのではなく、New-AzureRmDeployment を使用する必要があります。

### <a name="registration-assignment"></a>登録の割り当て

登録割り当てによって、登録定義が特定のスコープ (オンボードされたサブスクリプションやリソース グループ) に割り当てられます。

登録割り当ては、委任されたスコープごとに作成されます。そのため、オンボードされた内容に応じて、サブスクリプション グループ レベルまたはリソース グループ レベルのいずれかになります。

各登録割り当ては、サブスクリプション レベルで有効な登録定義を参照する必要があります。これにより、そのサービス プロバイダーに対する認証を委任されたスコープに結びつけ、それによりアクセス権が付与されます。

## <a name="logical-projection"></a>論理プロジェクション

Azure Lighthouse を使用すると、あるテナントから別のテナントへの、リソースの論理プロジェクションが作成されます。 これにより、承認されたサービス プロバイダー ユーザーは、委任された顧客サブスクリプションとリソース グループを操作できる認証を使用して、自分のテナントにサインインできます。 その後、サービス プロバイダーのテナント内にいるユーザーは、個々の顧客テナントにサインインしなくても、顧客に代わって管理操作を実行できるようになります。

サービス プロバイダー テナント内のユーザー、グループ、またはサービス プリンシパルが顧客のテナント内のリソースにアクセスするたびに、Azure Resource Manager に要求が送信されます。 Resource Manager では、顧客のテナント内のユーザーが行った要求の場合と同様に、これらの要求が認証されます。 Azure Lighthouse の場合、登録定義と登録の割り当てという 2 つのリソースが顧客のテナントに存在するかどうかを確認することでこれを行います。 その場合、Resource Manager では、これらのリソースによって定義された情報に従ってアクセスが承認されます。

:::image type="content" source="../media/logical-projection.jpg" alt-text="Azure Lighthouse の論理プロジェクションを示す図。":::

サービス プロバイダーのテナント内のユーザーからのアクティビティは、顧客のテナントに格納されているアクティビティ ログで追跡されます。 これにより、顧客は[どのような変更が誰によって行われたのか](../how-to/view-service-provider-activity.md)を確認できます。

## <a name="how-azure-lighthouse-works"></a>Azure Lighthouse のしくみ

管理テナントに対する Azure Lighthouse の大まかなしくみは以下のとおりです。

1. 顧客の Azure リソースを管理するために必要なグループ、サービス プリンシパル、またはユーザーの[ロール](tenants-users-roles.md#role-support-for-azure-lighthouse)を特定します。
2. このアクセス権を指定し、[Azure Marketplace にマネージド サービス オファーを公開する](../how-to/publish-managed-services-offers.md)か、[Azure Resource Manager テンプレートを展開](../how-to/onboard-customer.md)して、顧客を Azure Lighthouse にオンボードします。 このオンボード プロセスによって、前述した 2 つのリソース (登録定義と登録割り当て) が顧客のテナントに作成されます。
3. 顧客のオンボードが完了したら、許可されているユーザーは、定義したアクセス権に基づいて、指定した顧客のスコープ (サブスクリプションまたはリソース グループ) で、管理テナントにサインインしてタスクを実行します。 顧客は実行されたすべてのアクションを確認でき、いつでもアクセスを削除できます。

ほとんどの場合、1 社のサービス プロバイダーが顧客の代わりに特定のリソースの管理を行いますが、顧客が同じサブスクリプションやリソース グループに対して複数の委任を作成し、複数のサービス プロバイダーがアクセスできるようにすることも可能です。 このシナリオにより、[サービス プロバイダーのテナントから複数の顧客にリソースをプロジェクションする ISV のシナリオ](isv-scenarios.md#saas-based-multi-tenant-offerings)も可能になります。

## <a name="next-steps"></a>次のステップ

- 登録定義と登録割り当てを操作するための [Azure CLI](/cli/azure/managedservices) および [Azure Powershell](/powershell/module/az.managedservices) コマンドを確認します。
- Azure Lighthouse 用の[拡張サービスとシナリオ](cross-tenant-management-experience.md#enhanced-services-and-scenarios)について確認します。
- Azure Lighthouse における[テナント、ユーザー、およびロール](tenants-users-roles.md)のしくみについて確認します。
