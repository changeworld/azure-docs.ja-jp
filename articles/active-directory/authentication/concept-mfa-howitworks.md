---
title: Azure Multi-Factor Authentication - しくみ
description: Azure Multi-Factor Authentication を使えば、シンプルなサインイン プロセスを好むユーザーのニーズに応えながら、データやアプリケーションへのアクセスを効果的に保護することが可能です。
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: 25af61ce51828691d08210f4089afa0cf56b9846
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2018
ms.locfileid: "39257853"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>しくみ: Azure Multi-Factor Authentication

2 段階認証のセキュリティは、レイヤー アプローチによってもたらされます。 攻撃者にとって、多要素認証を侵害することはかなり困難です。 攻撃者がユーザーのパスワードを手に入れても、付加的な認証方法がなければ役に立ちません。 多要素認証は次の認証方法のうち 2 つ以上を要求することで機能します。

* ユーザーが知っているもの (通常はパスワード)
* ユーザーが持っているもの (携帯電話など、簡単には複製できない信頼できるデバイス)
* ユーザー自身 (生体認証)

<center>![認証方法の概念イメージ](./media/concept-mfa-howitworks/methods.png)</center>

Azure Multi-Factor Authentication (MFA) を利用すれば、データやアプリケーションへのアクセスを保護し、ユーザーには簡単なサインイン プロセスを提供できます。 2 つ目の認証を要求することでセキュリティを追加し、使い勝手の良いさまざまな[認証方法](concept-authentication-methods.md)によって強力な認証を与えます。

## <a name="how-to-get-multi-factor-authentication"></a>Multi-Factor Authentication の入手方法

Multi-Factor Authentication は以下のサービスに付属します。

* **Azure Active Directory Premium ライセンス** - Azure Multi-Factor Authentication Service (クラウド) または Azure Multi-Factor Authentication Server (オンプレミス) のすべての機能を使用できます。
   * **Azure MFA Service (クラウド)** - **このオプションは新しいデプロイに推奨されます**。 クラウドの Azure MFA はオンプレミス インフラストラクチャを必要とせず、フェデレーション ユーザーまたはクラウド専用ユーザーと共に利用できます。
   * **Azure MFA Server** - 関連付けられているインフラストラクチャ要素の管理を組織が望み、オンプレミス環境に AD FS をデプロイしている場合、この方法が選択肢に入ります。
* **Office 365 の Multi-Factor Authentication** - Azure Multi-Factor Authentication の一部の機能を集めたものであり、サブスクリプションの一環として利用できます。 MFA for Office 365 の詳細については、「[Office 365 展開用の多要素認証の計画](https://support.office.com/article/plan-for-multi-factor-authentication-for-office-365-deployments-043807b2-21db-4d5c-b430-c8a6dee0e6ba)」という記事を参照してください。
* **Azure Active Directory 全体管理者** - Azure Multi-Factor Authentication の一部の機能を集めたものであり、全体管理者アカウントを保護するための手段として利用されます。

### <a name="auth-provider-or-mfa-license"></a>認証プロバイダーまたは MFA ライセンス

Azure AD Premium をお持ちか、Azure AD Premium を含むライセンス バンドルをお持ちの場合、Azure MFA を既に与えられています。 組織では、すべてのユーザーを 2 段階認証機能の対象にするために特別な操作を行う必要はありません。 ユーザーにライセンスを割り当てるだけで、MFA をオンにすることができます。

Azure MFA を含むライセンスをお持ちでないか、お持ちのライセンスでは全ユーザーを扱えない場合、[MFA 認証プロバイダー](concept-mfa-authprovider.md)を作成し、MFA の全機能を、それを必要とするユーザーまで拡張できます。 

> [!IMPORTANT]
> 一部のユーザーにライセンスがない場合は、組織内の残りのユーザーを対象とするユーザーごとの Muti-Factor Auth Provider を作成できます。 認証ごとの Multi-Factor Auth Provider は作成しないでください。 認証ごとに Multi-Factor Auth Provider を作成すると、既にライセンスを持っているユーザーからの認証要求に対しても支払いすることになる可能性があります。

## <a name="supportability"></a>サポート

ユーザーの多くはパスワードのみを使用した認証に慣れているため、組織はこのプロセスに関してすべてのユーザーに通達することが重要です。 通達により、ユーザーが MFA 関連の重要でない問題についてヘルプ デスクに問い合わせる可能性が低くなります。 ただし、一部のシナリオでは MFA を一時的に無効にすることが必要になる場合があります。 次のガイドラインに従って、これらのシナリオへの対処方法を理解してください。

* 認証方法にアクセスできないか、認証方法が正しく動作せず、ユーザーがサインインできない状況に対応できるよう、サポート スタッフをトレーニングしてください。
   * サポート スタッフは Azure MFA Service の条件付きアクセス ポリシーを利用することで、MFA を要求するポリシーから除外されるグループにユーザーを追加できます。
   * サポート スタッフは Azure MFA Server ユーザーの一時的なワンタイム バイパスを有効にし、2 段階認証なしの認証をユーザーに許可できます。 このバイパスは一時的なものであり、指定された秒数が経過すると無効になります。
   * サポート スタッフは Azure MFA Service の条件付きアクセス ポリシーを利用することで、MFA を要求するポリシーから除外されるグループにユーザーを追加できます。
* 2 段階検証の要求を最小限に抑える方法として、信頼できる IP や名前付きの場所の利用を検討します。 この機能を使用すると、管理者常駐型テナントまたはフェデレーション テナントの管理者は、組織イントラネットなど、信頼されたネットワークの場所からサインインするユーザーの 2 段階認証をバイパスできます。
* [Azure AD Identity Protection](../active-directory-identityprotection.md) をデプロイし、リスク イベントに基づいて 2 段階検証をトリガーします。

## <a name="next-steps"></a>次の手順

- ステップバイステップの MFA [デプロイ プラン](https://aka.ms/MFADeploymentPlan)を見る

- [ユーザーにライセンスを供与する](concept-mfa-licensing.md)方法に関する詳細を読む

- [デプロイするバージョン](concept-mfa-whichversion.md)に関する詳細を読む

- [よく寄せられる質問](multi-factor-authentication-faq.md)の回答を探す
