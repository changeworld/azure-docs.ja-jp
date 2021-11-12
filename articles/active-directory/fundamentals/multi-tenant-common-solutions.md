---
title: Azure Active Directory テナントでのマルチテナント ユーザー管理の一般的なソリューション
description: ゲスト アカウントを使用して、Azure Active Directory テナント間でユーザー アクセスを構成するために使用する一般的なソリューションについて学習する
services: active-directory
author: BarbaraSelden
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/25/2021
ms.author: baselden
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48412e7ad5c7ff90f1e5b89542f55b57fec4a534
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270071"
---
# <a name="common-solutions-for-multi-tenant-user-management"></a>マルチテナント ユーザー管理の一般的なソリューション

お客様が現在のツールを使用して解決した具体的な課題は 2 つがあります。 そのソリューションの詳細を以下に示します。 Microsoft では、可能な限り 1 つのテナントを推奨し、これらの課題をより簡単に解決するためのツールに取り組んでいます。 単一テナントがシナリオで機能しない場合、これらのソリューションは現在、お客様に対して機能しています。 

## <a name="automatic-user-lifecycle-management-and-resource-allocation-across-tenants"></a>テナント間での自動ユーザー ライフサイクル管理とリソース割り当て

お客様は、以前は密接なビジネス関係を持っていた競合他社を獲得します。 組織は、会社の ID を維持します。

### <a name="current-state"></a>現在の状態

現在、組織は互いに連絡先メール オブジェクトとしてユーザーを同期し、互いにディレクトリに表示しています。 

* 各リソース テナントには、他のテナント内のすべてのユーザーに対してメール連絡先オブジェクトが有効になっています。

* テナント間でアプリケーションにアクセスできません。

### <a name="goals"></a>目標

このお客様には次の目標があります。

* すべてのユーザーは、各組織の GAL に引き続き表示されます。

   * ホーム テナントのユーザー アカウント ライフサイクルの変更は、リソース テナント GAL に自動的に反映されます。 

   * ホーム テナントの属性の変更 (部門、名前、SMTP アドレスなど) は、リソース テナント GAL とホーム GAL に自動的に反映されます。

* ユーザーは、リソース テナント内のアプリケーションとリソースにアクセスできます。

* ユーザーは、リソースへのアクセス要求をセルフサービスで実行できます。

### <a name="solution-architecture"></a>ソリューションのアーキテクチャ 

組織は、ポイント対ポイント アーキテクチャを、同期エンジン (例: MIM) で使用します。

![ポイント対ポイント アーキテクチャの例](media/multi-tenant-common-solutions/point-to-point-sync.png)

各テナント管理者は、ユーザー オブジェクトを作成するために次の操作を行います。

1. ユーザーのデータベースが最新の情報に更新されている必要があります。

1. [MIM をデプロイして構成する](/microsoft-identity-manager/microsoft-identity-manager-deploy)。

   1. 既存の連絡先オブジェクトにアドレスを指定します。

   1. 他のテナントのメンバーの B2B 外部メンバー オブジェクトを作成します。

   1. ユーザー オブジェクト属性を同期します。

1. [エンタイトルメント管理](../governance/entitlement-management-overview.md)アクセス パッケージをデプロイして構成します。

   1. 共有するリソース

   1. 有効期限とアクセス レビュー ポリシー

## <a name="sharing-on-premises-apps-across-tenants"></a>テナント間でのオンプレミス アプリの共有

このお客様は、複数のピア組織と、テナントの 1 つからオンプレミスのアプリケーションを共有する必要があります。

### <a name="current-state"></a>現在の状態

複数のピア組織がメッシュ トポロジで B2B ゲスト ユーザーを同期し、テナント間でクラウド アプリケーションへのリソース割り当てを有効にします。 現在次のとおりです。

* Azure AD でアプリケーションを共有します。

* リソース テナントのユーザー ライフサイクル管理がホーム テナントに基づいて自動化されています。 つまり、追加、変更、削除が反映されます。

* 会社 A のメンバー ユーザーだけが、会社 A のオンプレミス アプリにアクセスします。

![マルチテナント シナリオ](media/multi-tenant-user-management-scenarios/mesh.png)

### <a name="goals"></a>目標

現在の機能と共に、今後次のことを望んでいます。

* 外部ゲスト ユーザーに対して、会社 A のオンプレミス リソースへのアクセスを提供します。 

* SAML 認証を使用するアプリ

* Windows 認証および Kerberos Windows と統合されたアプリ

### <a name="solution-architecture"></a>ソリューションのアーキテクチャ

A 社は現在、自社のメンバーのために、オンプレミス アプリに対して、Azure アプリケーション プロキシ経由で SSO を提供しています。

![適用アクセスの例](media/multi-tenant-common-solutions/app-access-scenario.png)

ゲスト ユーザーがテネット A で同じオンプレミス アプリケーション管理者にアクセスできるようにするには、次の手順を実行します。

1. [SAML アプリへのアクセスを構成します](../external-identities/hybrid-cloud-to-on-premises.md#access-to-saml-apps)。

2. [他のアプリケーションに対するアクセスを構成します](../external-identities/hybrid-cloud-to-on-premises.md#access-to-iwa-and-kcd-apps)。

3. [MIM](../external-identities/hybrid-cloud-to-on-premises.md#create-b2b-guest-user-objects-through-mim) または [PowerShell](https://www.microsoft.com/en-us/download/details.aspx?id=51495) を使用して、オンプレミスのゲスト ユーザーを作成します。

B2B コラボレーションの詳細については、次を参照してください。

[Azure AD の B2B ユーザーにオンプレミス リソースへのアクセスを許可する](../external-identities/hybrid-cloud-to-on-premises.md)

[ハイブリッド組織向けの Azure Active Directory B2B コラボレーション](../external-identities/hybrid-organizations.md)

## <a name="next-steps"></a>次のステップ
[マルチテナント ユーザー管理の概要](multi-tenant-user-management-introduction.md)

[マルチテナント エンド ユーザー管理シナリオ](multi-tenant-user-management-scenarios.md)

[マルチテナントの一般的な考慮事項](multi-tenant-common-considerations.md)
