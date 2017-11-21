---
title: "Azure Active Directory のセルフサービス (バイラル) サインアップ | Microsoft Docs"
description: "Azure Active Directory (Azure AD) テナントでセルフサービス サインアップを使用します。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: b9f01876-29d1-4ab8-8b74-04d43d532f4b
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/03/2017
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.openlocfilehash: 762627a1d743922d680d90d1065ae80fbd3f4420
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2017
---
# <a name="what-is-self-service-signup-for-azure-active-directory"></a>Azure Active Directory のセルフサービス サインアップについて
この記事では、セルフサービス サインアップの概要と、Azure Active Directory (Azure AD) でサポートする方法について説明します。 管理されていない Azure AD テナントからドメイン名を引き継ぐ場合は、[管理されていないディレクトリを管理者として引き継ぐ方法](domains-admin-takeover.md)に関する記事をご覧ください。

## <a name="why-use-self-service-signup"></a>セルフサービス サインアップを使用する理由
* 顧客が求めるサービスを迅速に提供できる。
* サービス提供の電子メール ベース プランを構築できる。
* ユーザーが覚えやすい職場の電子メールの別名を使用して ID をすばやく作成することを可能にする、電子メール ベースのサインアップ フローを作成できる。
* セルフサービスで作成された Azure AD ディレクトリを、他のサービスで使用できる管理ディレクトリに変えることができる。

## <a name="terms-and-definitions"></a>用語と定義
* **セルフサービス サインアップ**: ユーザーがこの方法でクラウド サービスにサインアップすると、電子メール ドメインに基づいた ID が Azure AD に自動的に作成されます。
* **管理されていない Azure AD ディレクトリ**: これは、前述の ID が作成されるディレクトリです。 管理されていないディレクトリは、グローバル管理者がいないディレクトリです。
* **電子メール検証済みユーザー**: これは Azure AD のユーザー アカウントの 1 種です。 セルフサービス プランへのサインアップ後に自動作成された ID を持つユーザーは、電子メール検証済みユーザーです。 電子メール検証済みユーザーは、creationmethod=EmailVerified でタグ付けされたディレクトリの通常メンバーです。

## <a name="how-do-i-control-self-service-settings"></a>セルフサービス設定の管理方法
管理者は、セルフサービスの 2 種類の管理を実行できます。 次の点を管理できます。

* ユーザーが電子メール経由でディレクトリに参加できるかどうか。
* ユーザー自身がアプリケーションやサービスのライセンスを取得できるかどうか。

### <a name="how-can-i-control-these-capabilities"></a>これらの機能の管理方法
管理者は、Azure AD コマンドレット Set-MsolCompanySettings の次のパラメーターを使用してこれらの機能を構成できます。

* **AllowEmailVerifiedUsers** によって、ユーザーが管理されていないディレクトリを作成または参加できるかを管理できます。 このパラメーターを $false に設定すると、電子メール検証済みのユーザーはディレクトリに参加できません。
* **AllowAdHocSubscriptions** によって、ユーザーがセルフサービス サインアップを実行できるかどうかを管理できます。 このパラメータを $false に設定すると、ユーザーはセルフサービス サインアップを実行できません。

### <a name="how-do-the-controls-work-together"></a>これらの管理機能の連携について
これらの 2 つのパラメーターを組み合わせて使用すると、セルフサービス サインアップをさらに細かく管理できるようになります。 たとえば、次のコマンドにより、ユーザーはセルフサービス サインアップを実行できますが、Azure AD のアカウントを既に持っている場合に限られます (つまり、電子メール検証済みのアカウントをまず作成する必要があるユーザーは、セルフサービス サインアップを実行できません)。

````
    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true
````
次のフローチャートは、これらのパラメーターのさまざまな組み合わせと、結果として得られるディレクトリとセルフサービス サインアップの状態を示しています。

![][1]

これらのパラメーターの使用方法についての詳細は、「 [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)」を参照してください。

## <a name="next-steps"></a>次のステップ
* [Azure AD にカスタム ドメイン名を追加する](add-custom-domain.md)
* [Azure PowerShell のインストールおよび構成方法](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure コマンドレット リファレンス](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
