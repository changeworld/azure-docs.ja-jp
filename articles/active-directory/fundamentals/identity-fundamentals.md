---
title: Azure の ID およびアクセス管理の基礎とは - Azure Active Directory | Microsoft Docs
description: Azure Active Directory Premium エディションで利用できる高度な保護機能およびその他のツールについて説明します。
services: active-directory
author: eross-msft
manager: mtillman
ms.author: lizross
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/13/2018
ms.reviewer: jsnow
ms.custom: it-pro
ms.openlocfilehash: f7baa29c77ae4af9813bfc755a39cc07288a3ad2
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2018
ms.locfileid: "45734677"
---
# <a name="what-are-the-fundamentals-of-azure-identity-and-access-management"></a>Azure の ID およびアクセス管理の基礎とは
Azure AD Premium は高度な保護機能を備えた、クラウド ベースの ID およびアクセス管理ソリューションです。 これらの高度な機能により、ご利用のすべてのアプリに対するセキュリティで保護された ID、ID 保護 ([Microsoft インテリジェンス セキュリティ グラフで強化された](https://www.microsoft.com/security/intelligence))、および [Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) を容易に実現できます。 Azure AD を使用するとユーザーの ID をリアルタイムで保護することができ、これは、組織のデータに関して適応性の高いリスク ベースのアクセス ポリシーを作成するのに役立ちます。

Azure AD の ID 管理および保護の概要については、この簡単なビデオをご覧ください。
>[!VIDEO https://www.youtube.com/embed/9LGIJ2-FKIM]

Azure AD にはまた、ご利用の環境のセキュリティ保護、自動化、および管理 (パスワードのリセット、ユーザーおよびグループの管理、アプリの要求など) を支援する一連のツールが用意されています。 さらに Azure AD を使用すると、ユーザー所有のデバイスを管理、およびサービスとしてのソフトウェア (SaaS) アプリに対するアクセスと制御を管理するのが楽になります。

Azure Active Directory Premium エディションと関連ツールのコストの詳細については、「[Azure Active Directory の価格](https://azure.microsoft.com/pricing/details/active-directory/)」を参照してください。

## <a name="connect-on-premises-active-directory-with-azure-ad-and-office-365"></a>オンプレミスの Active Directory を Azure AD と Office 365 に接続する
ご利用のオンプレミスのディレクトリと Azure AD を[ハイブリッド ID 管理](https://aka.ms/aadframework)を通して統合することにより、ご利用のオンプレミスの Active Directory 実装をクラウドに拡張することができます。 この統合は [Azure AD Connect](../connect/active-directory-aadconnect.md) によって実現されます。これにより、ユーザーは単一の ID およびシングル サインオン (SSO) を使用して、貴社のオンプレミス リソースとクラウド サービス (Office 365 など) の両方にアクセスできるようになります。

Azure AD Connect は、DirSync や Azure AD Sync などの古いバージョンの ID 統合ツールに代わるものであり、オンプレミスと Azure AD の間での貴社の ID 同期ニーズをサポートするのに役立ちます。 Azure AD Connect の同期は、次の方法によって有効にされます。

- **同期。** ユーザー、グループ、およびその他のオブジェクトを作成する役割を果たします。 さらに、オンプレミス ユーザーの ID 情報を Azure AD 内のものと一致させる働きをします。 ユーザーが Azure AD 内でパスワードを更新したときにオンプレミスのディレクトリを同期した状態にしておくために、パスワード ライトバックも有効にすることもできます。

- 認証 使用する Azure AD ハイブリッド ID ソリューションを設定する際は、正しい認証方法を選択することが重要です。 組織用に選択できるのは、クラウド認証 (パスワード ハッシュ同期/パススルー認証) またはフェデレーション認証 (AD FS) です。 利用できるオプションの詳細については、「[Azure Active Directory ハイブリッド ID ソリューションの適切な認証方法を選択する](https://aka.ms/auth-options)」を参照してください。

- **正常性の監視。** Azure AD Connect Health では監視機能が提供され、Azure portal 上の中央の場所でこのアクティビティを確認することができます。 詳細については、「[クラウド内のオンプレミスの ID インフラストラクチャと同期サービスの監視](../connect-health/active-directory-aadconnect-health.md)」を参照してください。

## <a name="increase-productivity-and-reduce-helpdesk-costs-with-self-service-and-single-sign-on-experiences"></a>セルフサービスとシングル サインオン エクスペリエンスで生産性の向上とヘルプデスクのコスト削減を実現する
複数のユーザーが単一のユーザー名とパスワードを使用し、すべてのデバイス上のエクスペリエンスに一貫性があれば、それらのユーザーは時間の節約になります。 また、[忘れたパスワードのリセット](../user-help/active-directory-passwords-update-your-own-password.md)やアプリケーションへのアクセスの要求などのタスクを、ヘルプデスクの支援を待たずにセルフサービスで実行することにより、時間の節約になります。

SSO および一貫したエクスペリエンスを促進することにより、Azure AD では[オンプレミスの Active Directory](../connect/active-directory-aadconnect.md) がクラウドに拡張されます。これにより、ユーザーは、ドメイン参加デバイスと会社のリソースにも、業務で使用する必要があるすべての Web アプリケーションと SaaS アプリケーションにもプライマリ組織アカウントを使用できるようになります。 

さらに、グループ メンバーシップおよびユーザーの従業員ステータスに基づいて、アプリケーション アクセスを自動的にプロビジョニング (またはプロビジョニング解除) することができます、これにより、ギャラリー アプリへのアクセス、または開発してから [Azure AD アプリケーション プロキシ](../manage-apps/application-proxy.md)を介して発行した独自のオンプレミスのアプリへのアクセスを制御するのが容易になります。

## <a name="manage-and-control-access-to-your-organizational-resources"></a>ご自分の組織のリソースへのアクセスを管理および制御する
Microsoft ID およびアクセス管理ソリューションは、組織のデータ センター全体およびクラウドのアプリとリソースへのアクセスを保護するのに役立ちます。 このアクセス管理は、[Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md) や[条件付きアクセス ポリシー](../conditional-access/overview.md)などの追加レベルの検証を行うのに役立ちます。 高度なセキュリティ報告、監査、および警告によって疑わしいアクティビティを監視することは、潜在的なセキュリティ上の問題の軽減にも役立ちます。

Azure AD Premium での条件付きアクセス ポリシーを使用すると、Azure AD に接続された任意のアプリ (SaaS アプリ、クラウド内またはオンプレミスで実行されているカスタム アプリ、Web アプリなど) に対してポリシー ベースのアクセス規則を作成することができます。 それらの規則は、Azure AD によってリアルタイムで評価され、ユーザーがアプリにアクセスしようとするたびに適用されます。 Azure ID 保護ポリシーを使用すると、不審なアクティビティが検出された場合にアクションが自動的に実行されるようにすることができます (アクセスのブロック、Multi-Factor Authentication の適用、またはユーザー パスワードのリセットによって)。

## <a name="azure-active-directory-privileged-identity-management"></a>Azure Active Directory Privileged Identity Management
Azure Active Directory Premium 2 エディションに含まれている [Privileged Identity Management (PIM)](../privileged-identity-management/pim-getting-started.md) は、管理者アカウントと、Azure Active Directory およびその他の Microsoft Online Services 内のリソースへのそれらのアクセスを検出、制限、監視するのに役立ちます。 また、PIM は必要とされる正確な期間にわたって、オンデマンドの管理アクセスを管理するためにも役立ちます。つまり、多要素認証されていて、事前に構成された期間の一時的な特権の昇格を要求することを管理者に許可し、その期間が経過するとそのアカウントが通常のユーザーの状態に戻るようにすることができます。

## <a name="next-steps"></a>次の手順
Azure AD アーキテクチャの詳細については、[Azure AD のアーキテクチャ](active-directory-architecture.md)に関するページを参照してください。
