---
title: 電子メールで確認されたユーザーのセルフサービス サインアップ - Azure AD | Microsoft Docs
description: Azure Active Directory (Azure AD) 組織でセルフサービス サインアップを使用する
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: overview
ms.workload: identity
ms.date: 09/01/2021
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 19a300f13a18e4185877291099255c84c45bc34c
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "129986969"
---
# <a name="what-is-self-service-sign-up-for-azure-active-directory"></a>Azure Active Directory のセルフサービス サインアップについて

この記事では、セルフサービス サインアップを使用して Azure Active Directory (Azure AD) に組織を設定する方法について説明します。 アンマネージド Azure AD 組織からドメイン名を引き継ぐ場合は、[アンマネージド テナントを管理者として引き継ぐ方法](domains-admin-takeover.md)に関する記事をご覧ください。

## <a name="why-use-self-service-sign-up"></a>セルフサービス サインアップを使用する理由

* 顧客が求めるサービスを迅速に提供できる。
* サービス提供の電子メール ベース プランを構築できる。
* ユーザーが覚えやすい職場の電子メールの別名を使用して ID をすばやく作成することを可能にする、電子メール ベースのサインアップ フローを作成できる。
* セルフサービスで作成された Azure AD テナントを、他のサービスで使用できるマネージド テナントに変えることができる。

## <a name="terms-and-definitions"></a>用語と定義

* **セルフサービス サインアップ**:ユーザーがこの方法でクラウド サービスにサインアップすると、電子メール ドメインに基づいた ID が Azure AD に自動的に作成されます。
* **アンマネージド Azure AD テナント**: これは、その ID が作成されるテナントです。 アンマネージド テナントとは、全体管理者がいないテナントのことです。
* **電子メール検証済みユーザー**:これは Azure AD のユーザー アカウントの一種です。 セルフサービス プランへのサインアップ後に自動作成された ID を持つユーザーは、電子メール検証済みユーザーです。 電子メール検証済みユーザーは、creationmethod=EmailVerified でタグ付けされたテナントの通常メンバーです。

## <a name="how-do-i-control-self-service-settings"></a>セルフサービス設定の管理方法

管理者は、セルフサービスの 2 種類の管理を実行できます。 次の点を管理できます。

* ユーザーが電子メール経由でテナントに参加できるかどうか
* ユーザー自身がアプリケーションやサービスのライセンスを取得できるかどうか

### <a name="how-can-i-control-these-capabilities"></a>これらの機能の管理方法

管理者は、Azure AD コマンドレット Set-MsolCompanySettings の次のパラメーターを使用してこれらの機能を構成できます。

* **AllowEmailVerifiedUsers** は、ユーザーが電子メールの検証によってテナントに参加できるかどうかを制御します。 参加するには、ユーザーは、テナント内の検証済みドメインのいずれかに一致するドメイン内のメール アドレスを持っている必要があります。 この設定は、テナント内のすべてのドメインに対して、会社全体に適用されます。 このパラメーターを $false に設定すると、電子メール検証済みのユーザーはテナントに参加できません。
* **AllowAdHocSubscriptions** によって、ユーザーがセルフサービス サインアップを実行できるかどうかを管理できます。 このパラメーターを $false に設定すると、ユーザーはセルフサービス サインアップを実行できません。
  
AllowEmailVerifiedUsers と AllowAdHocSubscriptions は、マネージド テナントにもアンマネージド テナントにも適用できるテナント全体の設定です。 次のような例を示します。

* contoso.com などの検証済みドメインを持つテナントを管理する
* 別のテナントから B2B コラボレーションを使用して、contoso.com のホーム テナントにまだ存在しないユーザー (userdoesnotexist@contoso.com) を招待する
* ホーム テナントで AllowEmailVerifiedUsers がオンになっている

上記の条件が当てはまる場合、メンバー ユーザーがホーム テナントに作成され、B2B ゲスト ユーザーが招待側のテナントに作成されます。

Flow および Power Apps の試用版サインアップの詳細については、次の記事を参照してください。

* [既存のユーザーが Power BI の使用を開始できないようにするにはどうすればよいですか。](https://support.office.com/article/Power-BI-in-your-Organization-d7941332-8aec-4e5e-87e8-92073ce73dc5#bkmk_preventjoining)
* [組織における Flow の Q&A](/power-automate/organization-q-and-a)

### <a name="how-do-the-controls-work-together"></a>これらの管理機能の連携について
これら 2 つのパラメーターを組み合わせて使用すると、セルフサービス サインアップをさらに細かく管理できるようになります。 たとえば、次のコマンドによりユーザーはセルフサービス サインアップを実行できますが、 Azure AD のアカウントを既に持っている場合に限定されます (つまり、まずメール検証済みのアカウントを作成する必要があるユーザーは、セルフサービス サインアップを実行できません)。

```powershell
    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true
```

次のフローチャートは、これらのパラメーターのさまざまな組み合わせと、結果として得られるテナントとセルフサービス サインアップの状態を示しています。

![セルフサービス サインアップ コントロールのフローチャート](./media/directory-self-service-signup/SelfServiceSignUpControls.png)

この設定の詳細は、PowerShell コマンドレット Get-MsolCompanyInformation で取得できます。 この詳細については、「[Get-MsolCompanyInformation](/powershell/module/msonline/get-msolcompanyinformation)」を参照してください。

```powershell
    Get-MsolCompanyInformation | Select AllowEmailVerifiedUsers, AllowAdHocSubscriptions
```

これらのパラメーターの使用方法についての詳細は、「 [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings)」を参照してください。

## <a name="next-steps"></a>次の手順

* [Azure AD にカスタム ドメイン名を追加する](../fundamentals/add-custom-domain.md)
* [Azure PowerShell のインストールと構成の方法](/powershell/azure/)
* [Azure PowerShell](/powershell/azure/)
* [Azure コマンドレット リファレンス](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings)
* [アンマネージド テナントの職場または学校アカウントを削除する](users-close-account.md)
