---
title: Azure Active Directory B2B コラボレーションとは | Microsoft Docs
description: Azure Active Directory B2B コラボレーションは、会社のアプリケーションにビジネス パートナーが選択的にアクセスできるようにすることで会社間のリレーションシップをサポートします。
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 04/26/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 2bc405e6356113e0423f833868c86890c0c3d5d2
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/17/2018
ms.locfileid: "34259593"
---
# <a name="what-is-azure-ad-b2b-collaboration"></a>Azure AD B2B コラボレーションとは

Azure Active Directory (Azure AD) の B2B (Business-To-Business) コラボレーション機能を利用する組織は、Azure AD を使って他の任意の組織のユーザーと安全に共同作業を行うことができます。相手となる組織の規模の大小は関係がありません。 相手の組織が Azure AD を使用しているかどうかは関係なく、IT 部門を持っている必要さえありません。

Azure AD を使用する組織は、独自の社内データに対するコントロールを維持した状態で、そのパートナーに対してドキュメントやリソース、アプリケーションへのアクセスを提供することができます。 開発者は、Azure AD B2B API を使えば、2 つの組織を安全につなぐアプリケーションを開発することができます。 また、これにより、エンド ユーザーが移動しやすくなります。

次のビデオでは、役に立つ概要を提供します。
>[!VIDEO https://www.youtube.com/embed/AhwrweCBdsc]

## <a name="key-benefits-of-azure-ad-b2b-collaboration"></a>Azure AD B2B コラボレーションの主要な利点

### <a name="work-with-any-user-from-any-partner"></a>あらゆるパートナーのユーザーと連携可能

- パートナーはそれぞれ独自の資格情報を使用する
- Azure AD を使用するパートナーには負担が一切ない
- 外部のディレクトリや複雑なセットアップが不要

### <a name="simple-and-secure-collaboration"></a>単純かつ安全なコラボレーション

- 完成度の高い Azure AD の強力な承認ポリシーを適用しながら会社のあらゆるアプリやデータへのアクセスを提供
- ユーザーにとって容易
- エンタープライズ レベルのセキュリティをアプリとデータに確保

### <a name="no-management-overhead"></a>管理のオーバーヘッドを排除

- 外部アカウントやパスワードの管理が不要
- 同期や手動によるアカウント ライフサイクル管理が不要
- 外部の管理オーバーヘッドが発生しない

## <a name="easily-add-b2b-collaboration-users"></a>B2B コラボレーション ユーザーを簡単に追加できる

管理者は、[Azure portal](https://portal.azure.com) を使って簡単に B2B コラボレーション (ゲスト) ユーザーを組織に追加できます。

![ゲスト ユーザーの追加](media/what-is-b2b/adding-b2b-users-admin.png)

### <a name="enable-your-collaborators-to-bring-their-own-identity"></a>コラボレーターによる ID の持ち込みの実現

B2B のコラボレーターは、自分が選んだ任意の ID でサインインすることができます。 Microsoft アカウントも Azure AD アカウントも持っていなければ、オファーに応じた時点でシームレスに作成されます。

### <a name="delegate-to-application-and-group-owners"></a>アプリケーションとグループの所有者への委任

アプリケーションまたはグループの所有者は、Microsoft のアプリケーションであるかどうかに関係なく、自分が管理する任意のアプリケーションに対して B2B ユーザーを直接追加することができます。 管理者は、非管理者ユーザーに対し、B2B ユーザーを追加する権限を委任することができます。 非管理者は、[Azure AD のアプリケーション アクセス パネル](https://myapps.microsoft.com)を使って、B2B コラボレーション ユーザーをアプリケーションまたはグループに追加できます。

![アクセス パネル](media/what-is-b2b/access-panel.png)

![メンバーの追加](media/what-is-b2b/add-member.png)

### <a name="authorization-policies-protect-your-corporate-content"></a>承認ポリシーによる会社のコンテンツの保護

多要素認証などの条件付きアクセス ポリシーを次のように適用できます。
- テナント レベルで
- アプリケーション レベルで
- 特定のユーザーが会社のアプリケーションとデータを保護するために

### <a name="use-apis-and-sample-code-to-easily-build-applications-to-onboard"></a>API とサンプル コードを使用した、ユーザー受け入れのためのアプリケーションの容易な構築

貴社のニーズに応じて、外部のパートナーの受け入れ方法をカスタマイズすることができます。

[B2B コラボレーションの招待 API](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation) を使用すると、セルフサービス サインアップ ポータルの作成など、オンボーディング エクスペリエンスをカスタマイズすることができます。 セルフサービス ポータル用のサンプル コードは [Github](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web) で提供しています。

![サインアップ ポータル](media/what-is-b2b/sign-up-portal.png)

Azure AD B2B コラボレーションを利用すれば、Azure AD の強みをフルに活かしながら、エンド ユーザーにとって簡単かつ直感的な方法でパートナー関係を保護することができます。

## <a name="next-steps"></a>次の手順

- [Azure Active Directory 管理者が B2B コラボレーション ユーザーを追加する方法](add-users-administrator.md)
- [インフォメーション ワーカーが B2B コラボレーション ユーザーを追加する方法](add-users-information-worker.md)
- [B2B コラボレーションの招待の利用](redemption-experience.md)
- [Azure AD B2B コラボレーションのライセンス](licensing-guidance.md)
- また、フィードバックやご意見、ご提案があれば、[Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b) からいつでも製品チームにお寄せください。