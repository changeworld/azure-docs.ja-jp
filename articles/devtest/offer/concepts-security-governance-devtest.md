---
title: セキュリティ、ガバナンス、Azure Dev/Test サブスクリプション
description: 組織の Dev/Test サブスクリプション内のセキュリティとガバナンスを管理します。
author: jamestramel
ms.author: jametra
ms.prod: visual-studio-windows
ms.topic: how-to
ms.date: 10/20/2021
ms.custom: devtestoffer
ms.openlocfilehash: 6f3d9bae37e49c23dacc9a44ed4e0006df95ec29
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132305885"
---
# <a name="security-within-azure-devtest-subscription"></a>Azure Dev/Test サブスクリプション内のセキュリティ

リソースを安全に保つことは、クラウド プロバイダー、Azure、およびユーザーの間の共同作業です。 Azure Dev/Test サブスクリプションと [Microsoft Defender for Cloud](../../security-center/security-center-introduction.md) には、ネットワークのセキュリティを強化し、サービスをセキュリティで保護し、セキュリティの態勢を確保するために必要なツールが用意されています。  

Azure Dev/Test サブスクリプション内の重要なツールは、リソースへの安全なアクセスを作成するのに役立ちます。  

- Azure 管理グループ  
- Azure Lighthouse  
- クレジットの監視  
- Azure Active Directory  

## <a name="azure-management-groups"></a>Azure 管理グループ  

Azure Dev/Test サブスクリプションを有効にして設定すると、Azure によって、1 つの Azure Active Directory ドメインに ID とリソースへのアクセスを管理するための既定のリソース階層がデプロイされます。 リソース階層を使用すると、組織はリソースとユーザーに強力なセキュリティ境界を設定できます。  

![Azure 管理グループ のスクリーンショット](media/concepts-security-governance-devtest/access-management-groups.png "Azure の既定のリソース階層。")  

リソース、リソース グループ、サブスクリプション、管理グループ、およびテナントは、全体でリソース階層を成しています。 Azure カスタム ロールまたは Azure ポリシー割り当てでこれらの設定を更新および変更すると、リソース階層内のすべてのリソースに影響する可能性があります。 すべてのリソースに悪影響を及ぼす可能性のある変更からリソース階層を保護することが重要です。  

[Azure 管理グループ](../../governance/management-groups/overview.md)は、1 つのテナントでのアクセスの管理とリソースの保護の重要な側面です。 Azure 管理グループでは、クォータ、Azure ポリシー、およびセキュリティをさまざまな種類のサブスクリプションに設定できます。 これらのグループは、組織の Dev/Test サブスクリプションのセキュリティを開発するための重要なコンポーネントです。  

![Azure 組織とガバナンス グループのスクリーンショット](media/concepts-security-governance-devtest/orgs-and-governance.png "Azure の管理グループが全体的なガバナンスに適合する方法。")

上記のように、管理グループを使用すると、既定の階層が変更され、管理グループのレベルが追加されます。 [リソース階層を保護するための適切なプロセス](../../governance/management-groups/how-to/protect-resource-hierarchy.md)に従っていない場合、このような行動により、不測の事態やセキュリティ ホールが発生する可能性があります  

## <a name="why-are-azure-management-groups-useful"></a>Azure 管理グループが役に立つ理由  

組織の Dev/Test サブスクリプション向けのセキュリティ ポリシーを開発するとき、組織単位または基幹業務ごとに複数の Dev/Test サブスクリプションを使用することができます。 そのような管理グループの図を次に示します。  

![組織内の複数のサブスクリプションのサブスクリプション管理グループの図。](media/concepts-security-governance-devtest/access-management-groups.png "組織内の複数のサブスクリプションの管理グループの図。")  

また、すべての異なるユニットに対して 1 つの Dev/Test サブスクリプションを使用することもできます。  

Azure 管理グループと Dev/Test サブスクリプションは、組織構造内のセキュリティ バリアとして機能します。  

このセキュリティ バリアには、次の 2 つのコンポーネントがあります。  

- ID とアクセス: 特定のリソースへのアクセスをセグメント化する必要がある場合があります  
- データ: 個人情報にアクセスするリソース用に異なるサブスクリプション  

## <a name="using-azure-active-directory-tenants"></a>Azure Active Directory テナントの使用  

[テナント](../../active-directory/develop/quickstart-create-new-tenant.md)は、組織またはアプリ開発者が Azure、Microsoft Intune、Microsoft 365 へのサインアップのような Microsoft との関係を作成すると組織またはアプリ開発者が受け取る Azure AD の専用インスタンスです。  

各 Azure AD テナントは、他の Azure AD テナントから分離されています。 各 Azure AD テナントには、職場および学校 ID、コンシューマー ID (Azure AD B2C テナントの場合)、アプリの登録の独自の表現があります。 テナント内部のアプリの登録では、自分のテナント内のみ、またはすべてのテナント内のアカウントからの認証を許可できます。  

1 つのテナント内の管理グループを超えて組織の ID インフラをさらに分離する必要がある場合は、独自のリソース階層を持つ別のテナントを作成することもできます。  

リソースとユーザーを分離する簡単な方法は、新しい Azure AD テナントを作成することです。  

### <a name="create-a-new-azure-ad-tenant"></a>新しい Azure AD テナントの作成  

Azure AD テナントがない場合、または開発用に新しく作成したい場合は、[クイックスタート ガイド](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)を参照するか、 [ディレクトリ作成エクスペリエンス](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory)に従ってください。  新しいテナントを作成するには、次の情報を提供する必要があります。  

- **組織名**  
- **初期ドメイン** - /*.onmicrosoft.com の一部になります。 ドメインは後でカスタマイズできます。  
- **国または地域**  

 [Azure AD テナントの作成と設定の詳細を学習する](../../active-directory/develop/quickstart-create-new-tenant.md)  

### <a name="using-azure-lighthouse-to-manage-multiple-tenants"></a>複数のテナントを管理するための Azure Lighthouse の使用  

Azure Lighthouse により、クロステナントおよびマルチテナントの管理が実現し、リソースやテナント間での自動化やスケーラビリティの向上と統制機能の強化が可能になります。 サービス プロバイダーは、Azure プラットフォームに組み込みの包括的で堅牢な管理ツールを使用して、マネージド サービスを提供できます。 お客様は、テナントにアクセスするユーザー、アクセスするリソース、実行できるアクションを制御します。  

Azure Lighthouse の一般的なシナリオは、お客様の Azure Active Directory テナント内のリソースを管理することです。 ただし、Azure Lighthouse の機能を使用して、複数の Azure AD テナントを使用するエンタープライズ内のクロステナント管理を簡略化することもできます。  

ほとんどの組織では、1 つの Azure AD テナントの方が管理が簡単です。 1 つのテナント内にすべてのリソースを配置することで、そのテナント内の指定されたユーザー、ユーザー グループ、またはサービス プリンシパルによる管理タスクを一元化できます。  

マルチテナント アーキテクチャが必要な場合は、Azure Lighthouse が管理操作の一元化と合理化に役立ちます。 Azure の委任されたリソース管理を使用すると、1 つの管理テナントのユーザーが、一元化されたスケーラブルな方法でクロステナント管理機能を実行できます。  

[その他のセキュリティ リソース](../../security-center/security-center-introduction.md)
