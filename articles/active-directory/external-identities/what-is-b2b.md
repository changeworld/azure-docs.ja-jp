---
title: Azure Active Directory での B2B コラボレーションとは
description: Azure Active Directory B2B コラボレーションでは、リソースを外部パートナーと安全に共有してコラボレーションできるように、ゲスト ユーザー アクセスをサポートしています。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 10/21/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12d37973813fe2eee4972d2063d40df9e46b9b08
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130222478"
---
# <a name="what-is-guest-user-access-in-azure-active-directory-b2b"></a>Azure Active Directory B2B のゲスト ユーザー アクセスとは

Azure Active Directory (Azure AD) の企業間 (B2B) コラボレーションは、自分の組織にゲスト ユーザーを招待して共同作業を行うことができる External Identities の機能です。 B2B コラボレーションによって、貴社のデータに対するコントロールを維持した状態で、他の組織からアクセスするゲスト ユーザーとアプリケーションとサービスを安全に共有できます。 外部パートナーの規模に関係なく、Azure AD を所有していない場合や IT 部門が存在していない場合でも、外部パートナーとセキュリティで保護された安全な状態で作業できます。 単純な招待と受諾プロセスによって、パートナーは各自の資格情報を使用して、貴社のリソースにアクセスできます。 開発者は、Azure AD の B2B API を使用して、招待プロセスをカスタマイズしたり、セルフサービス サインアップ ポータルなどのアプリケーションを作成ししたりできます。 ゲスト ユーザーに関連したライセンスと価格情報については、「[Azure Active Directory 外部 ID の価格](https://azure.microsoft.com/pricing/details/active-directory/external-identities/)」を参照してください。  

> [!IMPORTANT]
>
> - **2021 年 7 月 12 日以降**、Azure AD の B2B のお客様が、カスタムまたは基幹業務アプリケーションのセルフサービス サインアップで使用するために新しい Google の統合をセットアップした場合、認証がシステム Web ビューに移動されるまで、Google ID を使用した認証が機能しなくなります。 [詳細については、こちらを参照してください](google-federation.md#deprecation-of-web-view-sign-in-support)。
> - **2021 年の 9 月 30 日より**、Google は [埋め込みの Web ビューのサインイン サポートを廃止](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html)します。 アプリで埋め込み Web ビューを使用してユーザーを認証していて、Google フェデレーションを [Azure AD B2C](../../active-directory-b2c/identity-provider-google.md)、Azure AD B2B [(外部ユーザーの招待用)](google-federation.md)、または[セルフサービス サインアップ](identity-providers.md)で使用している場合、Google Gmail ユーザーが認証されなくなります。 [詳細については、こちらを参照してください](google-federation.md#deprecation-of-web-view-sign-in-support)。
> - **2021 年 11 月 1 日以降**、既存のすべてのテナントに対して電子メールのワンタイム パスコード機能をオンにし、新しいテナントに対して既定で有効にするように、変更の展開を開始します。 この変更の一環として、B2B コラボレーションの招待の引き換え中に、Microsoft は、管理されていない (「バイラル」) 新しい Azure AD アカウントとテナントの作成を停止します。 休暇中の中断およびデプロイ ロックダウンを最小限に抑えるために、大多数のテナントに 2022 年 1 月に変更が公開されます。 電子メール ワンタイム パスコード機能を有効にするのは、これによりゲスト ユーザーにシームレスなフォールバック認証方法が提供されるからです。 ただし、この機能を自動的に有効にしたくない場合は、[無効にする](one-time-passcode.md#disable-email-one-time-passcode)ことができます。

## <a name="collaborate-with-any-partner-using-their-identities"></a>パートナーの ID を使用してパートナーとコラボレーションする

Azure AD B2B では、パートナーが各自の ID 管理ソリューションを使用するため、組織には外部管理を行うためのオーバーヘッドがありません。 ゲスト ユーザーは、各自の職場、学校、またはソーシャルの ID を使用して、アプリとサービスにサインインします。

- パートナーは、各自の ID と資格情報を使用するため、Azure AD は必要ありません。
- 外部アカウントまたはパスワードを管理する必要はありません。
- アカウントの同期もアカウントのライフ サイクルの管理も必要ありません。  

## <a name="easily-invite-guest-users-from-the-azure-ad-portal"></a>Azure AD ポータルからゲスト ユーザーを簡単に招待する

管理者は、Azure portal でゲスト ユーザーを組織に簡単に追加できます。

- 新しいユーザーを追加するときと同様の方法で Azure AD に[新しいゲスト ユーザーを作成](b2b-quickstart-add-guest-users-portal.md)する。
- ゲスト ユーザーをアプリまたはグループに割り当てる。
- 引き換えリンクを含む招待メールを送信するか、共有するアプリへの直接リンクを送信する。

![[新しいゲスト ユーザー] 招待入力ページを示すスクリーンショット](media/what-is-b2b/add-a-b2b-user-to-azure-portal.png)

- ゲスト ユーザーは、いくつかの簡単な[引き換え手順](redemption-experience.md)に従ってサインインします。

![[アクセス許可の確認] ページのスクリーンショット](media/what-is-b2b/consentscreen.png)


## <a name="use-policies-to-securely-share-your-apps-and-services"></a>ポリシーを使用してアプリとサービスを安全に共有する

承認ポリシーを使用して、会社のコンテンツを保護できます。 多要素認証などの条件付きアクセス ポリシーを次のように適用できます。

- テナント レベルで。
- アプリケーション レベルで。
- 会社のアプリケーションとデータを保護する特定のユーザーに対して。

![[条件付きアクセス] オプションを示すスクリーンショット](media/what-is-b2b/tutorial-mfa-policy-2.png)



## <a name="let-application-and-group-owners-manage-their-own-guest-users"></a>アプリケーションとグループの所有者が自分のゲスト ユーザーを管理できるようにする

ゲスト ユーザーの管理をアプリケーションの所有者に委任できます。これにより、所有者は、共有したいアプリケーションにゲストユーザーを直接追加できるようになります。アプリケーションは Microsoft のapplicationでもそれ以外でもかまいません。

- 管理者は、セルフサービス アプリとグループ管理を設定します。
- 管理者以外のユーザーは、自分の[アクセス パネル](https://myapps.microsoft.com)を使用して、アプリケーションまたはグループにゲスト ユーザーを追加します。

![ゲスト ユーザーのアクセス パネルを示すスクリーンショット](media/what-is-b2b/access-panel-manage-app.png)

## <a name="customize-the-onboarding-experience-for-b2b-guest-users"></a>B2B ゲスト ユーザーのオンボード エクスペリエンスをカスタマイズする

貴社のニーズに応じて、外部のパートナーの受け入れ方法をカスタマイズすることができます。

- [Azure AD のエンタイトルメント管理](../governance/entitlement-management-overview.md)を使用して、[外部ユーザーのアクセスを管理するポリシー](../governance/entitlement-management-external-users.md#how-access-works-for-external-users)を構成します。
- [B2B コラボレーションの招待 API シリーズ](/graph/api/resources/invitation)を使用して、オンボード エクスペリエンスをカスタマイズします。

## <a name="integrate-with-identity-providers"></a>ID プロバイダーと統合する

Azure AD は、Facebook、Microsoft アカウント、Google、エンタープライズ ID プロバイダーなどの外部 ID プロバイダーをサポートしています。 ID プロバイダーとのフェデレーションを設定すると、外部ユーザーは既存のソーシャル アカウントまたはエンタープライズ アカウントを使用してサインインでき、アプリケーション専用の新しいアカウントを作成せずにすみます。 さらに[外部 ID の ID プロバイダー](identity-providers.md)について説明します。

![ID プロバイダーのページを示すスクリーンショット](media/what-is-b2b/identity-providers.png)


## <a name="create-a-self-service-sign-up-user-flow"></a>セルフサービス サインアップのユーザー フローを作成する

セルフサービス サインアップ ユーザー フローを使用すると、アプリにアクセスしようとする外部ユーザーのためにサインアップ エクスペリエンスを作成できます。 サインアップ フローの一部として、さまざまなソーシャル ID プロバイダーまたはエンタープライズ ID プロバイダーのオプションを提供したり、ユーザーに関する情報を収集したりすることができます。 セルフサービス サインアップとその設定方法については、[こちら](self-service-sign-up-overview.md)を参照してください。

また、[API コネクタ](api-connectors-overview.md)を使用して、セルフサービス サインアップ ユーザー フローを外部クラウド システムと統合することもできます。 カスタム承認ワークフローを使用して接続したり、本人確認を実行したり、ユーザー指定の情報を検証したりできます。

![ユーザー フロー ページを示すスクリーンショット](media/what-is-b2b/self-service-sign-up-user-flow-overview.png)
<!--TODO: Add screenshot with API connectors -->

## <a name="next-steps"></a>次のステップ

- [External Identities の価格](external-identities-pricing.md)
- [ポータルで B2B コラボレーションのゲスト ユーザーを追加する](add-users-administrator.md)
- [招待の受諾プロセスを理解する](redemption-experience.md)
