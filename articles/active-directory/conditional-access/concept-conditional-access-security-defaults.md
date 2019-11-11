---
title: Azure Active Directory のセキュリティ デフォルト
description: 組織を一般的な攻撃から保護するために設計されたセキュリティ デフォルトのポリシー
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: b4921f0605f16a601f6e2ee9a15b71b50d253201
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2019
ms.locfileid: "72452925"
---
# <a name="what-are-security-defaults"></a>セキュリティ デフォルトとは

パスワード スプレー、リプレイ、フィッシングなど、ID 関連の一般的な攻撃がますます広まる中で、セキュリティの管理に困難をきたす場合があります。 これらの一般的な攻撃から組織をより強固に保護するための、より簡単な方法を提供することが、Azure Active Directory (AD) のセキュリティ デフォルトの目標です。 セキュリティ デフォルトは、セキュリティの実現をいっそう容易にし、一般的な攻撃から組織を保護するために役立ちます。 セキュリティ デフォルトには、これらの一般的な攻撃に対して事前に構成されたセキュリティ設定が含まれています。 Microsoft では、誰もがセキュリティ デフォルトを利用できるようにしています。 目標は、すべての組織が追加の費用なしで基本レベルのセキュリティを確実に有効にできるようにすることです。

![新しいセキュリティ デフォルト UX のスクリーンショット](./media/concept-conditional-access-security-defaults/security-defaults-azure-ad-portal.png)
 
テナントでは、次のセキュリティ構成が有効になります。 

## <a name="unified-mfa-registration"></a>統合 MFA 登録

テナントのすべてのユーザーは、Azure 多要素認証 (MFA) に登録する必要があります。 ユーザーは Microsoft Authenticator アプリを使用して 14 日以内に Azure MFA に登録します。 14 日が経過すると、ユーザーは MFA 登録が完了するまでサインインできなくなります。

ユーザーによっては、セキュリティ デフォルトを有効にした直後の 14 日以内に出勤またはログインしない場合があることは理解しています。 すべてのユーザーに MFA 登録のための十分な時間を与えられるよう、14 日の期限はユーザーごとに固有です。 各ユーザーの 14 日の期限は、セキュリティ デフォルトが有効になった後、ユーザーの対話型ログインが最初に成功した時点から始まります。

## <a name="mfa-enforcement"></a>MFA の執行

### <a name="protecting-administrators"></a>管理者の保護

特権アカウントにアクセスできるユーザーは、より自由に環境にアクセスできます。 これらのアカウントには権限があるので、特別な注意を払って対処する必要があります。 特権アカウントの保護を向上するための一般的な方法の 1 つは、特権アカウントがサインインに使用されるときに、強力な形式のアカウント検証を必須にすることです。 Azure Active Directory では、多要素認証を必須にすることで、アカウント検証を強力にすることができます。

MFA の登録が完了した後、サインインのたびに MFA を実行するためには、次の 9 つの Azure AD 管理者ロールが必要になります。

- 全体管理者
- SharePoint 管理者
- Exchange 管理者
- 条件付きアクセス管理者
- セキュリティ管理者
- ヘルプデスク管理者/パスワード管理者
- 課金管理者
- ユーザー管理者
- 認証管理者

### <a name="protecting-all-users"></a>すべてのユーザーの保護

多要素認証 (MFA) による保護が必要なアカウントは管理者アカウントだけであると考えがちです。 管理者は、機密情報への広範なアクセス権を持ち、サブスクリプション全体の設定に変更を加えることができます。 しかし、攻撃者はエンド ユーザーをターゲットにする傾向があります。 このような攻撃者は、アクセス権を取得した後、元々のアカウント所有者に代わって機密性の高い情報へのアクセスを要求したり、ディレクトリ全体をダウンロードして組織全体にフィッシング攻撃を仕掛けたりする可能性があります。 すべてのユーザーを対象にした保護を向上するための一般的な方法の 1 つは、全員を対象にした多要素認証 (MFA) など、より強力な形式のアカウント検証を要求することです。 MFA 登録が完了した後、ユーザーは必要に応じて MFA を要求されます。

### <a name="blocking-legacy-authentication"></a>レガシ認証をブロックする

指定したユーザーがお使いのクラウド アプリに簡単にアクセスできるように、Azure Active Directory (Azure AD) ではレガシ認証を含め、幅広い認証プロトコルをサポートしています。 レガシ認証は、次のものによって行われる認証要求を指す用語です。

- 先進認証を使用していない古い Office クライアント (Office 2010 クライアントなど)
- IMAP/SMTP/POP3 などの従来のメール プロトコルを使用しているすべてのクライアント

現在、危険にさらそうとするすべてのサインイン試行の大部分はレガシ認証から来ています。 レガシ認証は、多要素認証 (MFA) をサポートしていません。 ディレクトリ上で MFA ポリシーが有効になっている場合でも、悪意のあるアクターはレガシ プロトコルを使用して認証し、MFA を迂回できます。 テナントでセキュリティ デフォルトが有効になった後、レガシ プロトコルによって行われた認証要求は、要求先を問わずすべてブロックされます。 セキュリティ デフォルトは Exchange ActiveSync はブロックしません。

### <a name="protecting-privileged-actions"></a>特権アクションの保護

組織では、Azure Resource Manager API によって管理される、次のような各種の Azure サービスを使用します。

- Azure ポータル 
- Azure PowerShell 
- Azure CLI

Azure Resource Manager を使用してご自身のサービスを管理する操作は、高い権限が与えられているアクションです。 Azure Resource Manager では、サービス設定、サブスクリプションの課金など、テナント全体の構成を変更できます。 単一要素認証は、フィッシング、パスワード スプレーなどの幅広い攻撃に対して脆弱です。 したがって、Azure Resource Manager へのアクセスと構成の更新を求めるユーザーに対しては、アクセスを許可する前に、多要素認証を要求してそのユーザーの ID を確認することが重要です。

テナントでセキュリティ デフォルトが有効になった後、Azure portal、Azure PowerShell、または Azure CLI にアクセスするユーザーはすべて、多要素認証を完了するよう要求されます。 このポリシーは、Azure Resource Manager にアクセスするすべてのユーザーに対して、そのユーザーが管理者またはユーザーかどうかに関係なく適用されます。 ユーザーが MFA に登録されていない場合、そのユーザーが操作を続行するには、Microsoft Authenticator アプリを使用してユーザー登録を行う必要があります。 14 日の MFA 登録期間は提供されません。

## <a name="deployment-considerations"></a>デプロイに関する考慮事項

テナントのセキュリティ デフォルトのデプロイに関連したその他の考慮事項を次に示します。

### <a name="legacy-protocols"></a>レガシ プロトコル

レガシ認証プロトコル (IMAP、SMTP、POP3 など) は、認証要求を行うためにメール クライアントによって使用されます。 これらのプロトコルは、MFA をサポートしていません。 Microsoft によって確認されているアカウントのセキュリティ侵害のほとんどでは、攻撃者がレガシ プロトコルを攻撃することによって MFA のバイパスを試みています。 管理アカウントにログインするときに確実に MFA を要求し、攻撃者が MFA をバイパスできないようにするために、セキュリティ デフォルトは、レガシ プロトコルから管理者アカウントに対して行われるすべての認証要求をブロックします。

> [!WARNING]
> この設定を有効にする前に、管理者がレガシ認証プロトコルを使用していないことを確認してください。 詳細については、 [レガシ認証から移行する方法](concept-conditional-access-block-legacy-authentication.md)に関する記事を参照してください。

### <a name="conditional-access"></a>条件付きアクセス

セキュリティ デフォルトによって有効化されるものと同じ動作を提供するポリシーは、条件付きアクセスを使用して構成できます。 条件付きアクセスを使用しており、条件付きアクセス ポリシーを環境で有効にしている場合、セキュリティ デフォルトは使用できません。 条件付きアクセスを提供するライセンスがあるが、条件付きアクセス ポリシーが環境で有効になっていない場合、条件付きアクセス ポリシーを有効にするまではセキュリティ デフォルトを使用できます。

![セキュリティ デフォルトと条件付きアクセスは併用不可](./media/concept-conditional-access-security-defaults/security-defaults-conditional-access.png)

ここでは、条件付きアクセスを使用して同等のポリシーを構成する方法について、ステップ形式で説明します。

- [管理者に対して MFA を必須にする](howto-conditional-access-policy-admin-mfa.md)
- [Azure 管理のために MFA を必須にする](howto-conditional-access-policy-azure-management.md)
- [レガシ認証をブロックする](howto-conditional-access-policy-block-legacy.md)

## <a name="enabling-security-defaults"></a>セキュリティ デフォルトの有効化

ディレクトリでセキュリティ デフォルトを有効にするには、次のようにします。

1. セキュリティ管理者、条件付きアクセス管理者、またはグローバル管理者として、 [Azure portal](https://portal.azure.com)  にサインインします。
1.  **[Azure Active Directory]**  > **[プロパティ]** を参照します
1. **[Manage security defaults]\(セキュリティ デフォルトの管理\)** を選択します
1. **[Enable security defaults]\(セキュリティ デフォルトを有効にする\)** トグルを **[はい]** に設定します。
1. [保存] をクリックします。

## <a name="next-steps"></a>次の手順

[一般的な条件付きアクセス ポリシー](concept-conditional-access-policy-common.md)

[条件付きアクセスとは](overview.md)
