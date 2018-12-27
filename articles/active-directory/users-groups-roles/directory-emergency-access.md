---
title: Azure AD で緊急アクセス用管理者アカウントを管理する | Microsoft Docs
description: この記事では、既存の Azure Active Directory 環境内での特権アクセスを制限するのに役立つ緊急アクセス用アカウントを使う方法について説明します。
services: active-directory
author: markwahl-msft
ms.author: billmath
ms.date: 12/13/2017
ms.topic: article-type-from-white-list
ms.service: active-directory
ms.workload: identity
ms.custom: it-pro
ms.reviewer: markwahl-msft
ms.openlocfilehash: 4f3772abc1cdbd3b35b8b1f16e7a47c0f1a17783
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38595656"
---
# <a name="manage-emergency-access-administrative-accounts-in-azure-ad"></a>Azure AD で緊急アクセス用管理者アカウントを管理する 

ほとんどの日常的なアクティビティでは、ユーザーに "*グローバル管理者*" 権限は必要ありません。 ユーザーが本来持つべき権限より高い権限を必要とするタスクを誤って実行する可能性があるため、ユーザーにロールを永続的に割り当てないでください。 ユーザーは、グローバル管理者として作業する必要がないときは、ユーザー自身のアカウントまたは別の管理者アカウントで、Azure Active Directory (Azure AD) Privileged Identity Management (PIM) を使って、ロールの割り当てをアクティブ化する必要があります。

ユーザーが自分で管理者アクセス権を取得することに加えて、管理者がサインインできず、既存の個別ユーザーのアカウントを管理者としてアクティブ化することもできないために、誤って Azure AD テナントを管理できなくなることも防ぐ必要があります。 複数の "*緊急アクセス用アカウント*" をテナントに保持すると、不注意で管理アクセスが不可能になることによる影響を軽減できます。

緊急アクセス用アカウントは、組織において既存の Azure Active Directory 環境内での特権アクセスを制限するのに役立ちます。 このようなアカウントは高い特権を持っており、特定のユーザーには割り当てられません。 緊急アクセス用アカウントは、通常の管理者アカウントを使うことができない緊急事態に制限されます。 組織は、緊急用アカウントは必要なときにだけ使うという制限を守る必要があります。

次のような場合に緊急アクセス用アカウントの使用が必要になることがあります。

 - ユーザー アカウントがフェデレーションされており、携帯ネットワークの途絶または ID プロバイダーの停止のためにフェデレーションを現在使用できない場合。 たとえば、環境内の ID プロバイダー ホストがダウンしている場合、Azure AD が ID プロバイダーにリダイレクトしたときにユーザーはサインインできない可能性があります。 
 - 管理者が Azure Multi-Factor Authentication によって登録されていて、すべての個別デバイスを利用できない場合。 ユーザーは、ロールをアクティブ化する Multi-Factor Authentication を完了できない可能性があります。 たとえば、携帯ネットワークが停止すると、ユーザーがデバイスに対して登録したただ 2 つの認証メカニズムである、電話呼び出しへの応答も、テキスト メッセージの受信も、できなくなります。 
 - 最後にグローバル管理者アクセス権を持っていたユーザーが組織からいなくなった場合。 Azure AD では最後の "*グローバル管理者*" アカウントを削除できないようになっていますが、オンプレミスでアカウントが削除または無効化されるのを防ぐことはできません。 いずれの場合も、アカウントを復旧できなくなる可能性があります。

## <a name="initial-configuration"></a>初期構成

複数の緊急アクセス用アカウントを作成します。 これらは、\*.onmicrosoft.com ドメインを使い、オンプレミス環境からフェデレーションまたは同期されていないクラウド専用のアカウントである必要があります。 

これらのアカウントを組織内の個別ユーザーと関連付けることはできません。 これらのアカウントの資格情報は安全に保管し、それらを使うことを許可されたユーザーのみに知らせる必要があります。 

> [!NOTE]
> 緊急アクセス用アカウントのアカウント パスワードは、通常、2 または 3 つの部分に分けて、異なる紙に書き記し、個別に安全な耐火性の場所に保管します。 
>
> 緊急アクセス用アカウントが、従業員が所有する携帯電話、従業員が出張で持ち歩くハードウェア トークン、またはその他の従業員固有の資格情報と結び付けられていないことを確認します。 この予防策には、資格情報が必要なときに従業員がそれを入手できないような状況が該当します。 

### <a name="initial-configuration-with-permanent-assignments"></a>永続的な割り当てによる初期構成

1 つのオプションは、ユーザーを "*グローバル管理者*" ロールの永続的なメンバーにすることで。 このオプションは、Azure AD Premium P2 サブスクリプションを持っていない組織に適しています。

侵害されたパスワードによる攻撃のリスクを減らすため、すべての個別ユーザーに対して多要素認証を使うことをお勧めします。 このグループには、管理者と、アカウントが侵害されると重大な影響のある他のすべてのユーザー (たとえば財務責任者) を含める必要があります。 

ただし、組織に共有デバイスがない場合、これらの緊急アクセス用アカウントには多要素認証を使うことができない可能性があります。 Azure AD および他の接続されているサービスとしてのソフトウェア (SaaS) アプリの[すべての管理者に対して多要素認証の登録](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states)を要求する条件付きアクセス ポリシーを構成する場合は、緊急アクセス用アカウントをこの要件から除外するようにポリシーの除外を構成することが必要になる場合があります。

### <a name="initial-configuration-with-approvals"></a>承認が必要な初期構成

もう 1 つのオプションは、"*グローバル管理者*" ロールをアクティブ化する資格のある承認者としてユーザーを構成することです。 このオプションを使うには、Azure AD Premium P2 サブスクリプションが必要です。 また、複数のユーザーとネットワーク環境の間での共有使用に適した多要素認証オプションも必要です。 これらのことが必要なのは、"*グローバル管理者*" ロールのアクティブ化にはユーザーがそれ以前に多要素認証を実行している必要があるためです。 詳しくは、「[Azure AD Privileged Identity Management で MFA を要求する方法](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-how-to-require-mfa)」をご覧ください。

緊急アクセス用アカウントの場合は、個人デバイスに関連付けられている多要素認証は使わないようにすることをお勧めします。 実際の緊急時には、多要素認証に登録されたデバイスにアクセスする必要のあるユーザーが、個人デバイスの所有者ではない可能性があります。 

また、脅威の状況も検討する必要があります。 たとえば、自然災害などの予期しない状況が発生して、携帯電話や他のネットワークが利用できなくなる可能性があります。 Azure AD との複数の通信手段がある既知の安全な場所に、登録済みのデバイスを保管しておくことが重要です。

## <a name="ongoing-monitoring"></a>継続的な監視

[Azure AD のサインイン ログと監査ログ](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins)で、緊急アクセス用アカウントからのサインインと監査のアクティビティを監視します。 通常、これらのアカウントにサインインしてはならず、アカウントを変更してはならないので、アカウントの使用は異常なことであり、セキュリティ調査を行う必要があります。

## <a name="account-check-validation-must-occur-at-regular-intervals"></a>アカウントチェックの検証を定期的に実行する必要があります。

アカウントを検証するには、少なくとも次の手順を実行します。
- 90 日ごと。
- 異動、退職、新規採用など、最近 IT スタッフの変更があったとき。
- 組織の Azure AD サブスクリプションが変更されたとき。

緊急アクセス用アカウントを使うスタッフのトレーニングは、次の手順で行います。

* アカウントチェック アクティビティが行われていることをセキュリティ監視スタッフが認識していることを確認します。
* クラウド ユーザー アカウントがサインインしてロールをアクティブ化できること、および緊急時にこれらの手順を実行する必要のあるユーザーがプロセスのトレーニングを受けていることを、検証します。
* ユーザーが、ユーザーの個人デバイスや個人情報に多要素認証またはセルフサービスのパスワード リセット (SSPR) を登録していないことを確認します。 
* デバイスに対する多要素認証にアカウントが登録されている場合、ロールのアクティブ化で使うには、緊急時にデバイスを使う必要があるすべての管理者がデバイスにアクセスできることを確認します。 また、同じ障害モードを共有していない少なくとも 2 つのメカニズムを通じて、デバイスが登録されていることを確認します。 たとえば、デバイスが施設のワイヤレス ネットワークと携帯電話会社ネットワークの両方を介してインターネットに通信できるようにします。
* アカウントの資格情報を更新します。

## <a name="next-steps"></a>次の手順
- [クラウドベースのユーザーを追加し](../fundamentals/add-users-azure-active-directory.md)、[新しいユーザーをグローバル管理者ロールに割り当てます](../fundamentals/active-directory-users-assign-role-azure-portal.md)。
- まだサインアップしていない場合は、[Azure Active Directory Premium にサインアップ](../fundamentals/active-directory-get-started-premium.md)します。
- [管理者として割り当てられたユーザーに Azure Multi-Factor Authentication を使うよう要求します](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states)。
- Office 365 を使っている場合は、[Office 365 のグローバル管理者に追加の保護を構成](https://support.office.com/article/Protect-your-Office-365-global-administrator-accounts-6b4ded77-ac8d-42ed-8606-c014fd947560)します。
- [グローバル管理者のアクセス レビューを実行](../privileged-identity-management/pim-how-to-start-security-review.md)し、[既存のグローバル管理者をより明確な管理者ロールに移行](directory-assign-admin-roles.md)します。


