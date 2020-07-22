---
title: Azure AD Domain Services でのディレクトリの不一致エラーを修正する | Microsoft Docs
description: Azure AD Domain Services でのディレクトリの不一致エラーの意味とその解決方法について説明します
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 40eb75b7-827e-4d30-af6c-ca3c2af915c7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: 40dd985b7cf09ddc2a902630cec3f0c74a1edbe1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84734607"
---
# <a name="resolve-mismatched-directory-errors-for-existing-azure-active-directory-domain-services-managed-domains"></a>既存の Azure Active Directory Domain Services のマネージド ドメインでのディレクトリの不一致エラーを解決する

Azure Active Directory Domain Services (Azure AD DS) マネージド ドメインに、一致しないテナントのエラーが表示される場合は、解決するまでそのマネージド ドメインを管理できません。 このエラーは、基になっている Azure 仮想ネットワークが別の Azure AD ディレクトリに移動された場合に発生します。

この記事では、エラーが発生する理由とその解決方法について説明します。

## <a name="what-causes-this-error"></a>このエラーの原因

ディレクトリの不一致エラーは、Azure AD DS マネージド ドメインと仮想ネットワークが 2 つの異なる Azure AD テナントに属している場合に発生します。 たとえば、Contoso の Azure AD テナントで実行される *aaddscontoso.com* というマネージド ドメインがあるとします。 しかし、マネージド ドメインの Azure 仮想ネットワークは、Fabrikam の Azure AD テナントの一部です。

Azure はロールベースのアクセス制御 (RBAC) を使用して、リソースへのアクセスを制限します。 Azure AD テナントで Azure AD DS を有効にすると、資格情報ハッシュがそのマネージド ドメインに同期されます。 この操作を行うには、ユーザーが Azure AD ディレクトリのテナント管理者である必要があり、資格情報へのアクセスが制御される必要があります。 Azure 仮想ネットワークにリソースをデプロイし、トラフィックを制御するには、Azure AD DS をデプロイする仮想ネットワークに対する管理特権が必要です。

RBAC が安定して動作し、Azure AD DS が使用するすべてのリソースへのアクセスを保護するには、マネージド ドメインと仮想ネットワークが同じ Azure AD テナントに属している必要があります。

Resource Manager 環境では次のルールが適用されます。

- 1 つの Azure AD ディレクトリで複数の Azure サブスクリプションを所有することができる。
- 1 つの Azure サブスクリプションで複数のリソース (仮想ネットワークなど) を所有することができる。
- 1 つの Azure AD ディレクトリに対して有効化する Azure AD Domain Services のマネージド ドメインは 1 つだけである。
- Azure AD Domain Services のマネージド ドメインは、同一の Azure AD テナント内にあるいずれかの Azure サブスクリプションに属する仮想ネットワークで有効化することができる。

### <a name="valid-configuration"></a>有効な構成

次のデプロイ シナリオの例では、Contoso マネージド ドメインが Contoso Azure AD テナント内で有効になっています。 このマネージド ドメインは、Contoso Azure AD テナントが所有する Azure サブスクリプションに属する仮想ネットワークにデプロイされています。 マネージド ドメインと仮想ネットワークの両方が同じ Azure AD テナントに属しています。 この構成例は有効であり、完全にサポートされています。

![同じ Azure AD テナントのマネージド ドメインと仮想ネットワークの部分を持つ有効な Azure AD DS テナント構成](./media/getting-started/valid-tenant-config.png)

### <a name="mismatched-tenant-configuration"></a>テナントが一致しない構成

次のデプロイ シナリオの例では、Contoso マネージド ドメインが Contoso Azure AD テナント内で有効になっています。 しかし、このマネージド ドメインがデプロイされている仮想ネットワークは、Fabrikam Azure AD テナントが所有する Azure サブスクリプションに属しています。 マネージド ドメインと仮想ネットワークは 2 つの異なる Azure AD テナントに属しています。 この構成例は、一致しないテナントであり、サポートされません。 仮想ネットワークは、マネージド ドメインと同じ Azure AD テナントに移動する必要があります。

![テナントが一致しない構成](./media/getting-started/mismatched-tenant-config.png)

## <a name="resolve-mismatched-tenant-error"></a>一致しないテナントのエラーを解決する

ディレクトリの不一致エラーの解決には、次の 2 つのオプションがあります。

* 既存の Azure AD ディレクトリから[マネージド ドメインを削除](delete-aadds.md)します。 使用する仮想ネットワークと同じ Azure AD ディレクトリに、[代替マネージド ドメインを作成](tutorial-create-instance.md)します。 準備ができたら、削除したドメインに以前参加していたマシンすべてを、再作成したマネージド ドメインに参加させます。
* マネージド ドメインと同じ Azure AD ディレクトリに、仮想ネットワークが含まれる [Azure サブスクリプションを移動](../cost-management-billing/manage/billing-subscription-transfer.md)します。

## <a name="next-steps"></a>次のステップ

Azure AD DS に関する問題のトラブルシューティングの詳細については、[トラブルシューティング ガイド](troubleshoot.md)に関するページを参照してください。
