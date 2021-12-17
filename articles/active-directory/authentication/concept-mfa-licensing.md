---
title: Azure AD Multi-Factor Authentication のバージョンと従量課金プラン
description: Azure AD Multi-Factor Authentication クライアントと、使用可能なさまざまな方法およびバージョンについて説明します。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: dff3c04ee74d17aeb0b96d8d0e2348a9667f9729
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131456311"
---
# <a name="features-and-licenses-for-azure-ad-multi-factor-authentication"></a>Azure AD Multi-Factor Authentication の機能とライセンス

組織内のユーザー アカウントを保護するには、多要素認証を使用する必要があります。 リソースへの特権アクセスが認められているアカウントでは、この機能が特に重要となります。 Microsoft 365 および Azure Active Directory (Azure AD) の全体管理者は、追加料金なしで基本的な多要素認証機能を利用できます。 管理者向けの機能をアップグレードしたり、多要素認証を他のユーザーに拡張したりといった希望がある場合には、いくつかの方法で Azure AD Multi-Factor Authentication を購入できます。

> [!IMPORTANT]
> この記事では、Azure AD Multi-Factor Authentication のライセンスを取得して使用するさまざまな方法について詳しく説明します。 価格と課金の詳細については、[Azure AD の価格に関するページ](https://www.microsoft.com/en-us/security/business/identity-access-management/azure-ad-pricing)を参照してください。

## <a name="available-versions-of-azure-ad-multi-factor-authentication"></a>Azure AD Multi-Factor Authentication の使用可能なバージョン

Azure AD Multi-Factor Authentication は、組織のニーズに応じて、いくつかの異なる方法で使用し、ライセンスを取得することができます。 現在お使いの Azure AD、EMS、Microsoft 365 のライセンスによっては、Azure AD Multi-Factor Authentication を使用する権利を既に持っている場合があります。 たとえば、Azure AD External Identities での最初の 50,000 人の月間アクティブ ユーザーは、MFA および他の Premium P1 または P2 の機能を無料で使用できます。 詳細については、「[Azure Active Directory External Identities の価格](https://azure.microsoft.com/pricing/details/active-directory/external-identities/)」を参照してください。

次の表では、Azure AD Multi-Factor Authentication を入手するさまざまな方法と、それぞれの機能およびユース ケースについて詳しく説明します。

| 次のユーザーの場合 | 機能とユース ケース |
| --- | --- |
| [Microsoft 365 Business Premium](https://www.microsoft.com/microsoft-365/business) および [EMS](https://www.microsoft.com/security/business/enterprise-mobility-security) または [Microsoft 365 E3 と E5](https://www.microsoft.com/microsoft-365/enterprise/compare-office-365-plans) | EMS E3、Microsoft 365 E3、Microsoft 365 Business Premium には Azure AD Premium P1 が含まれています。 EMS E5 または Microsoft 365 E5 には、Azure AD Premium P2 が含まれています。 次のセクションに記載されている同じ条件付きアクセス機能を使用して、ユーザーに多要素認証を提供できます。 |
| [Azure AD Premium P1](../fundamentals/active-directory-get-started-premium.md) | [Azure AD 条件付きアクセス](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)を使用して、ビジネス要件に合わせて特定のシナリオやイベントの際に多要素認証をユーザーに求めることができます。 |
| [Azure AD Premium P2](../fundamentals/active-directory-get-started-premium.md) | 最も強力なセキュリティのポジションと、向上したユーザー エクスペリエンスを提供します。 [リスク ベースの条件付きアクセス](../conditional-access/howto-conditional-access-policy-risk.md)を Azure AD Premium P1 の機能に追加することで、ユーザーのパターンに適応し、多要素認証の回数を最小限に抑えます。 |
| [すべての Microsoft 365 プラン](https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans) | Azure AD Multi-Factor Authentication は、[セキュリティの既定値群](../fundamentals/concept-fundamentals-security-defaults.md)を使用して、すべてのユーザーについて有効にすることができます。 Azure AD Multi-Factor Authentication の管理は、Microsoft 365 ポータルを通じて行います。 ユーザー エクスペリエンスを向上させるには、Azure AD Premium P1 または P2 にアップグレードし、条件付きアクセスを使用します。 詳細については、[多要素認証を使用した Microsoft 365 リソースのセキュリティ保護](/microsoft-365/admin/security-and-compliance/set-up-multi-factor-authentication)に関するページを参照してください。  |
| [Office 365 Free](https://www.microsoft.com/microsoft-365/enterprise/compare-office-365-plans)<br>[Azure AD Free](../verifiable-credentials/how-to-create-a-free-developer-account.md) | 必要に応じて[セキュリティの既定値群](../fundamentals/concept-fundamentals-security-defaults.md)を使用して多要素認証をユーザーに要求できますが、有効となるユーザーまたはシナリオをきめ細かく制御することはできません。ただし、追加のセキュリティ措置を提供することはできます。<br /> すべてのユーザーの多要素認証を有効にするセキュリティの既定値群が使用されていない場合でも、*Azure AD 全体管理者* ロールに割り当てられたユーザーは、多要素認証を使用するように構成できます。 Free レベルのこの機能により、重要な管理者アカウントが多要素認証によって保護されます。 |

## <a name="feature-comparison-based-on-licenses"></a>ライセンスに基づく機能比較

次の表に、さまざまなバージョンの Azure AD Multi-Factor Authentication で使用できる機能の一覧を示します。 ユーザー認証のセキュリティ保護に必要なものを詳しく検討し、その要件を満たす方法を決定します。 たとえば、Azure AD Free は Azure AD Multi-Factor Authentication を提供するセキュリティの既定値群を提供しますが、認証プロンプトに使用できるのはモバイル認証アプリだけであり、電話や SMS は使用できません。 モバイル認証アプリがユーザーの個人のデバイスにインストールされていることを保証できない場合、この方法は制約を受けるかもしれません。 詳細については、後の「[Azure AD Free レベル](#azure-ad-free-tier)」を参照してください。 

| 機能 | Azure AD Free - セキュリティの既定値群 (すべてのユーザーに対して有効) | Azure AD Free - 全体管理者のみ | Office 365 | Azure AD Premium P1 または P2 |
| --- |:---:|:---:|:---:|:---:|
| MFA で Azure AD テナント管理者アカウントを保護する | ● | ● (*Azure AD 全体管理者* アカウントの場合のみ) | ● | ● |
| モバイル アプリを 2 番目の要素にする | ● | ● | ● | ● |
| 音声通話を 2 番目の要素にする | | ● | ● | ● |
| SMS を 2 番目の要素にする | | ● | ● | ● |
| 検証方法の管理制御 | | ● | ● | ● |
| 不正アクセスのアラート | | | | ● |
| MFA レポート | | | | ● |
| 音声通話のカスタムあいさつ文 | | | | ● |
| 音声通話のカスタム発信元 ID | | | | ● |
| 信頼できる IP | | | | ● |
| 信頼済みデバイスの MFA の記憶 | | ● | ● | ● |
| オンプレミス アプリケーション用の MFA | | | | ● |

## <a name="compare-multi-factor-authentication-policies"></a>多要素認証ポリシーを比較する

MFA を適用する方法としては、[条件付きアクセス](../conditional-access/overview.md)の使用が推奨されます。 次の表を見て、ご利用のライセンスに含まれる機能を確認してください。

| のポリシー | セキュリティの既定値群 | 条件付きアクセス | ユーザーごとの MFA |
| --- |:---:|:---:|:---:|
| **管理** | 
| 会社の安全を維持するためのセキュリティ規則の標準セット | ● |  |  |
| ワンクリックのオンまたはオフ | ● |  |  |
| Office 365 ライセンスに含まれる ([ライセンスに関する考慮事項](#available-versions-of-azure-ad-multi-factor-authentication)に関するセクションを参照してください) | ● |  | ● |
| Microsoft 365 管理センター ウィザードでの事前構成済みのテンプレート | ● | ● |  |
| 構成の柔軟性 | | ● |  |
| **機能** | 
| ポリシーからユーザーを除外する | | ● | ● |
| 電話または SMS による認証 | | ● | ● |
| Microsoft Authenticator とソフトウェア トークンによる認証 | ● | ● | ● |
| FIDO2、Windows Hello for Business、およびハードウェア トークンによる認証 | | ● | ● |
| レガシ認証プロトコルのブロック | ● | ● | ● |
| 新しい従業員の自動的な保護 | ● | ● | |
| リスク イベントに基づく動的 MFA トリガー | | ● |  |
| 認証および承認ポリシー | | ● | |
| 場所とデバイスの状態に基づいて構成可能 | | ● | |
| "レポート専用" モードのサポート | | ● | |
| ユーザーまたはサービスを完全にブロックする機能 | | ● | |

## <a name="purchase-and-enable-azure-ad-multi-factor-authentication"></a>Azure AD Multi-Factor Authentication を購入して有効にする

Azure AD Multi-Factor Authentication を使用するには、資格のある Azure AD のレベルに登録するか、ご購入ください。 Azure AD には、Free、Office 365、Premium P1、Premium P2 の 4 つのエディションが用意されています。

Free エディションは、Azure サブスクリプションに含まれます。 セキュリティの既定値群を使用する方法、または *Azure AD 全体管理者* ロールでアカウントを保護する方法についての情報は、[下記のセクション](#azure-ad-free-tier)を参照してください。

Azure AD Premium エディションは、Microsoft の担当者、[Open Volume License プログラム](https://www.microsoft.com/licensing/licensing-programs/open-license.aspx)、および [Cloud Solution Providers プログラム](https://go.microsoft.com/fwlink/?LinkId=614968&clcid=0x409)から入手できます。 Azure および Microsoft 365 のサブスクライバーは Azure Active Directory Premium P1 および P2 をオンラインで購入することもできます。 [サインイン](https://portal.office.com/Commerce/Catalog.aspx)して購入します。

必要な Azure AD レベルを購入した後、[Azure AD Multi-Factor Authentication を計画してデプロイします](howto-mfa-getstarted.md)。

### <a name="azure-ad-free-tier"></a>Azure AD Free レベル

Azure AD Free テナントのすべてのユーザーは、セキュリティの既定値群を使用することにより、Azure AD Multi-Factor Authentication を使用することができます。 モバイル認証アプリは、Azure AD Free のセキュリティの既定値群を使用する場合に Azure AD Multi-Factor Authentication で使用できる、唯一の方法です。

* [Azure AD のセキュリティの既定値群についての詳細情報を確認する](../fundamentals/concept-fundamentals-security-defaults.md)
* [Azure AD Free ユーザーのセキュリティの既定値群を有効にする](../fundamentals/concept-fundamentals-security-defaults.md#enabling-security-defaults)

すべてのユーザーに対して Azure AD Multi-Factor Authentication を有効にしない場合、代わりに *Azure AD 全体管理者* ロールを持つユーザー アカウントのみを保護することを選択できます。 この方法では、重要な管理者アカウントに対して追加の認証プロンプトが表示されます。 使用しているアカウントの種類に応じて、次のいずれかの方法で Azure AD Multi-Factor Authentication を有効にします。

* Microsoft アカウントを使用している場合は、[多要素認証に登録](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification)します。
* Microsoft アカウントを使用していない場合は、[Azure AD のユーザーまたはグループの多要素認証を有効にします](howto-mfa-userstates.md)。

## <a name="next-steps"></a>次のステップ

* コストについて詳しくは、[Azure AD の価格](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)に関するページをご覧ください。
* [条件付きアクセスとは](../conditional-access/overview.md)
* MFA は、[ユーザーごとに有効にする](howto-mfa-userstates.md)こともできます

