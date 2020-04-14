---
title: Azure Active Directory のセキュリティ デフォルト
description: 組織を一般的な攻撃から保護するために設計されたセキュリティ デフォルトのポリシー
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3139d39797c3bc576bb39f1438b7e6d3f37e3c5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "78248848"
---
# <a name="what-are-security-defaults"></a>セキュリティの既定値群とは

ID 関連の一般的な攻撃がますます広まる中で、セキュリティの管理に困難をきたす場合があります。 このような攻撃として、パスワード スプレー、リプレイ、フィッシングなどがあります。

Azure Active Directory (Azure AD) のセキュリティ既定値は、セキュリティの実現をいっそう容易にし、組織を保護するために役立ちます。 セキュリティの既定値群には、一般的な攻撃に対して事前に構成されたセキュリティ設定が含まれています。 

Microsoft は、誰もがセキュリティの既定値群を利用できるよう努めています。 目標は、すべての組織が追加の費用なしで基本レベルのセキュリティを確実に有効にできるようにすることです。 セキュリティの既定値群は、Azure portal で有効にします。

![セキュリティ デフォルトを有効にするためのトグルがある Azure portal のスクリーンショット](./media/concept-fundamentals-security-defaults/security-defaults-azure-ad-portal.png)
 
> [!TIP]
> お使いのテナントが 2019 年 10 月 22 日以降に作成された場合は、新しい "既定のセキュリティ保護" 動作により、セキュリティの既定値群が既にテナントで有効になっている可能性があります。 すべてのユーザーを保護するために、セキュリティの既定値群は、新しく作成されたすべてのテナントにロールアウトされます。

セキュリティの既定値群が使用可能になっている理由の詳細については、Alex Weinert の「[セキュリティの既定値群の導入](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/introducing-security-defaults/ba-p/1061414)」というブログ記事をお読みください。

## <a name="unified-multi-factor-authentication-registration"></a>多要素認証の登録手続きの統一

テナント内のすべてのユーザーは、Azure Multi-Factor Authentication サービスのフォームを使用して多要素認証 (MFA) に登録する必要があります。 ユーザーは 14 日以内に Microsoft Authenticator アプリを使用して Multi-Factor Authentication に登録する必要があります。 14 日が経過すると、ユーザーは Multi-Factor Authentication への登録が完了するまでサインインできなくなります。

セキュリティの既定値群を有効にした直後の 14 日以内に、一部のユーザーが出勤またはログインしない場合があることは理解しています。 ユーザー全員に Multi-Factor Authentication の登録のための十分な時間を与えられるように、14 日の期間はユーザーごとに固有です。 14 日の期間は、セキュリティの既定値群が有効になった後、それぞれのユーザーの対話型サインインが最初に成功した時点から始まります。

## <a name="multi-factor-authentication-enforcement"></a>Multi-Factor Authentication の強制

### <a name="protecting-administrators"></a>管理者の保護

特権アカウントにアクセスできるユーザーは、より自由に環境にアクセスできます。 これらのアカウントには権限があるので、特別な注意を払って対処する必要があります。 特権アカウントの保護を強化するための一般的な方法の 1 つは、サインイン時に、強力な形式のアカウント検証を必須にすることです。 Azure AD では、Multi-Factor Authentication を必須にすることで、アカウント検証を強力にすることができます。

次の 9 つの Azure AD 管理者ロールについては、Multi-Factor Authentication への登録が完了した後、サインインのたびに追加の認証を実行する必要があります。

- 全体管理者
- SharePoint 管理者
- Exchange 管理者
- 条件付きアクセス管理者
- セキュリティ管理者
- ヘルプデスク管理者またはパスワード管理者
- 課金管理者
- ユーザー管理者
- 認証管理者

### <a name="protecting-all-users"></a>すべてのユーザーの保護

認証の追加のレイヤーが必要なアカウントは管理者アカウントだけであると考えがちです。 管理者は、機密情報への広範なアクセス権を持ち、サブスクリプション全体の設定に変更を加えることができます。 しかし、攻撃者はエンド ユーザーをターゲットにする傾向があります。 

これらの攻撃者は、アクセス権を取得した後、元のアカウント所有者に代わって機密性の高い情報へのアクセスを要求できます。 ディレクトリ全体をダウンロードして、組織全体に対してフィッシング攻撃を実行することさえできます。 

すべてのユーザーを対象にした保護を向上させるための一般的な方法の 1 つは、全員に Multi-Factor Authentication を要求するなど、より強力な形式のアカウント検証を要求することです。 ユーザーが Multi-Factor Authentication の登録を完了すると、必要に応じて追加の認証を求められるようになります。

### <a name="blocking-legacy-authentication"></a>レガシ認証をブロックする

ユーザーがクラウド アプリに簡単にアクセスできるように、Azure AD ではレガシ認証を含め、幅広い認証プロトコルがサポートされています。 "*レガシ認証*" は、以下のものによって行われる認証要求を指す用語です。

- 先進認証を使用していないクライアント (Office 2010 クライアントなど)。
- IMAP、SMTP、POP3 などの古いメール プロトコルを使用しているクライアント。

今日では、不正侵入を意図したサインイン試行の大部分がレガシ認証によるものです。 レガシ認証では、Multi-Factor Authentication がサポートされていません。 ディレクトリで Multi-Factor Authentication ポリシーが有効になっている場合でも、攻撃者は古いプロトコルを使用して認証を受け、Multi-Factor Authentication をバイパスできます。 

テナントでセキュリティ デフォルトが有効になった後は、古いプロトコルによるすべての認証要求がブロックされます。 セキュリティ既定値は、Exchange Active Sync 基本認証をブロックします。

> [!WARNING]
> セキュリティの既定値群を有効にする前に、管理者が古い認証プロトコルを使用していないことを確認してください。 詳細については、[レガシ認証から移行する方法](concept-fundamentals-block-legacy-authentication.md)に関するページを参照してください。

### <a name="protecting-privileged-actions"></a>特権アクションの保護

組織では、Azure Resource Manager API によって管理される、次のような各種の Azure サービスを使用します。

- Azure portal 
- Azure PowerShell 
- Azure CLI

Azure Resource Manager を使用してご自身のサービスを管理する操作は、高い権限が与えられているアクションです。 Azure Resource Manager では、サービス設定、サブスクリプションの課金など、テナント全体の構成を変更できます。 単一要素認証は、フィッシング、パスワード スプレーなどの各種の攻撃に対して脆弱です。 

Azure Resource Manager にアクセスして構成を更新しようとするユーザーの ID を検証することが重要です。 アクセスを許可する前に、追加の認証を要求して ID を検証します。

テナントでセキュリティの既定値群を有効にすると、Azure portal、Azure PowerShell、または Azure CLI にアクセスしようとしているユーザーがいずれも、追加の認証を完了しなければならなくなります。 このポリシーは、Azure Resource Manager にアクセスしようとしているユーザーであれば、管理者であるかユーザーであるかに関係なく全員に適用されます。 

ユーザーが Multi-Factor Authentication に登録していない場合、そのユーザーが操作を続行するには、Microsoft Authenticator アプリを使用してユーザー登録を行う必要があります。 Multi-Factor Authentication に登録するための 14 日の期間は提供されません。

> [!NOTE]
> 2017 年より前の Exchange Online テナントでは、先進認証が既定で無効になっています。 これらのテナントを通じて認証を行うときにログイン ループの可能性を回避するために、[先進認証を有効にする](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online)必要があります。

> [!NOTE]
> Azure AD Connect の同期アカウントはセキュリティの既定値群から除外されるため、Multi-Factor Authentication の登録または実行を求められることはありません。 組織は、このアカウントを他の目的で使用しないでください。

## <a name="deployment-considerations"></a>デプロイに関する考慮事項

テナントに対するセキュリティの既定値群のデプロイに関連したその他の考慮事項を次に示します。

### <a name="authentication-methods"></a>認証方法

セキュリティの既定値群を使用すると、**通知を使用する Microsoft Authenticator アプリのみを使用して**、Azure Multi-Factor Authentication の登録と使用を行うことができます。 条件付きアクセスでは、管理者が有効にする任意の認証方法を使用できます。

|   | セキュリティの既定値群 | 条件付きアクセス |
| --- | --- | --- |
| モバイル アプリでの通知 | X | X |
| モバイル アプリからの確認コードまたはハードウェア トークン |   | X |
| 電話へのテキスト メッセージ |   | X |
| 電話の呼び出し |   | X |
| アプリ パスワード |   | X** |

** アプリ パスワードは、管理者が有効にした場合にのみ、レガシ認証シナリオでのユーザーごとの MFA でのみ使用できます。

### <a name="conditional-access"></a>条件付きアクセス

条件付きアクセスを使用して、セキュリティの既定値群に似たポリシーを構成できますが、厳密にはセキュリティの既定値群では利用できないユーザーの除外も含まれます。 条件付きアクセスを使用しており、環境で条件付きアクセス ポリシーを有効にしている場合、セキュリティの既定値群は使用できません。 条件付きアクセスが利用できるライセンスを持っていても、環境で条件付きアクセス ポリシーが有効になっていない場合には、条件付きアクセス ポリシーを有効にしない限り、セキュリティの既定値群を使用できます。 Azure AD ライセンスの詳細については、[Azure AD の価格に関するページ](https://azure.microsoft.com/pricing/details/active-directory/)を参照してください。

![セキュリティ既定値と条件付きアクセスは併用不可であるという警告メッセージ](./media/concept-fundamentals-security-defaults/security-defaults-conditional-access.png)

以下は、条件付きアクセスを使用して同等のポリシーを構成する方法のステップバイステップ ガイドです。

- [管理者に対して MFA を必須にする](../conditional-access/howto-conditional-access-policy-admin-mfa.md)
- [Azure 管理のために MFA を必須にする](../conditional-access/howto-conditional-access-policy-azure-management.md)
- [レガシ認証をブロックする](../conditional-access/howto-conditional-access-policy-block-legacy.md)
- [すべてのユーザーに対して MFA を必須にする](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)
- [Azure MFA への登録を必須とする](../identity-protection/howto-identity-protection-configure-mfa-policy.md) - Azure AD Identity Protection が必要です

## <a name="enabling-security-defaults"></a>セキュリティの既定値群の有効化

ディレクトリでセキュリティの既定値群を有効にするには、次のようにします。

1. セキュリティ管理者、条件付きアクセス管理者、またはグローバル管理者として、 [Azure portal](https://portal.azure.com)  にサインインします。
1.  **[Azure Active Directory]**  > **[プロパティ]** の順に移動します。
1. **[セキュリティの既定値群の管理]** を選択します。
1. **[セキュリティの既定値群の有効化]** トグルを **[はい]** に設定します。
1. **[保存]** を選択します。

## <a name="disabling-security-defaults"></a>セキュリティの既定値群を無効にする

セキュリティの既定値群を置き換える条件付きアクセス ポリシーを実装する組織では、セキュリティの既定値群を無効にする必要があります。 

![条件付きアクセス ポリシーを有効にするためにセキュリティの既定値を無効にする警告メッセージ](./media/concept-fundamentals-security-defaults/security-defaults-disable-before-conditional-access.png)

ディレクトリでセキュリティの既定値群を無効にするには、次のようにします。

1. セキュリティ管理者、条件付きアクセス管理者、またはグローバル管理者として、 [Azure portal](https://portal.azure.com)  にサインインします。
1.  **[Azure Active Directory]**  > **[プロパティ]** の順に移動します。
1. **[セキュリティの既定値群の管理]** を選択します。
1. **[セキュリティの既定値群の有効化]** トグルを **[いいえ]** に設定します。
1. **[保存]** を選択します。

## <a name="next-steps"></a>次のステップ

[一般的な条件付きアクセス ポリシー](../conditional-access/concept-conditional-access-policy-common.md)
