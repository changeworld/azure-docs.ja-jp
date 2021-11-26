---
title: Microsoft Sentinel に対してプレイブックを認証する | Microsoft Docs
description: プレイブックに Microsoft Sentinel へのアクセス権を付与して、是正措置を実行するための認可を行う方法について説明します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 69fb32256df39eb666e40bde777cc1e400ac3de4
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132715247"
---
# <a name="authenticate-playbooks-to-microsoft-sentinel"></a>Microsoft Sentinel へのプレイブックの認証

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Logic Apps の動作のしくみでは、Microsoft Sentinel 自体を含め、対話するすべての種類のすべてのリソースに対し別々に接続して、個別に認証する必要があります。 Azure Logic Apps は、この目的で、各リソースの種類に独自のコネクタを持つ[専用のコネクタ](/connectors/connector-reference/)を使用します。 このドキュメントでは、[Logic Apps Microsoft Sentinel コネクタ](/connectors/azuresentinel/)での接続と認証の種類について説明します。これは、プレイブックでワークスペースのテーブル内の情報にアクセスするための Microsoft Sentinel との対話に使用できます。

これは、[プレイブックでのトリガーとアクションの使用](playbook-triggers-actions.md)に関するガイドと共に、他のプレイブック ドキュメント「[チュートリアル: Microsoft Sentinel でオートメーション ルールとプレイブックを使用する](tutorial-respond-threats-playbook.md)」と併せて使用するドキュメントです。

プレイブックの概要については、「[Microsoft Sentinel のプレイブックを使用して脅威への対応を自動化する](automate-responses-with-playbooks.md)」を参照してください。

Microsoft Sentinel コネクタの完全な仕様については、[Logic Apps コネクタのドキュメント](/connectors/azuresentinel/)を参照してください。

## <a name="authentication"></a>認証

Logic Apps 内の Microsoft Sentinel コネクタとそのコンポーネントのトリガーとアクションは、関連するワークスペースで必要なアクセス許可 (読み取りおよび書き込み) を持つ任意の ID の代わりに使用できます。 コネクタは、次の複数の ID の種類をサポートします。

- [マネージド ID (プレビュー)](#authenticate-with-managed-identity)
- [Azure AD ユーザー](#authenticate-as-an-azure-ad-user)
- [サービス プリンシパル (Azure AD アプリケーション)](#authenticate-as-a-service-principal-azure-ad-application)

    ![認証オプション](media/authenticate-playbooks-to-sentinel/auth-methods.png)

### <a name="permissions-required"></a>必要なアクセス許可

| ロールまたはコネクタ コンポーネント | トリガー | "Get" アクション | インシデントの更新、<br>コメントの追加 |
| ------------- | :-----------: | :------------: | :-----------: |
| **[Microsoft Sentinel 閲覧者](../role-based-access-control/built-in-roles.md#microsoft-sentinel-reader)** | &#10003; | &#10003; | &#10007; |
| **Microsoft Sentinel [レスポンダー](../role-based-access-control/built-in-roles.md#microsoft-sentinel-responder)/[共同作成者](../role-based-access-control/built-in-roles.md#microsoft-sentinel-contributor)** | &#10003; | &#10003; | &#10003; |
| 

[Microsoft Sentinel のアクセス許可の詳細を参照してください](./roles.md)。

### <a name="authenticate-with-managed-identity"></a>マネージド ID による認証

この認証方法を使用すると、プレイブック (Logic Apps ワークフロー リソース) にアクセス許可を直接付与できるため、プレイブックによって実行される Microsoft Sentinel コネクタのアクションは、Microsoft Sentinel に対する独自のアクセス許可を持つ独立したオブジェクトであるかのように、プレイブックに代わって動作します。 この方法を使用すると、管理する必要がある ID の数が減少します。 

> [!NOTE]
> マネージド ID に他のリソース (Microsoft Sentinel ワークスペースなど) へのアクセスを許可するには、サインインしているユーザーに、Microsoft Sentinel ワークスペースの所有者やユーザー アクセス管理者などのロールの割り当てを書き込むためのアクセス許可を持つロールが必要です。

マネージド ID を使用して認証するには、次の手順を実行します。

1. Azure Logic Apps ワークフロー リソースで[マネージド ID を有効](../logic-apps/create-managed-service-identity.md#enable-system-assigned-identity-in-azure-portal)にします。 まとめ

    - ロジック アプリのメニューの **[設定]** で、 **[ID]** を選択します。 **[システム割り当て済み]、[On]、[保存]** の順に選択します。 確認を求めるメッセージが表示されたら、 **[はい]** を選択します。

    - これで、ロジック アプリでシステム割り当て ID を使用できるようになりました。これは Azure AD に登録され、オブジェクト ID で表されます。

1. Microsoft Sentinel ワークスペースへの[アクセス許可をその ID に付与](../logic-apps/create-managed-service-identity.md#give-identity-access-to-resources)します。
    1. Microsoft Sentinel のメニューで **[設定]** を選択します。
    1. **[ワークスペースの設定]** タブを選択します。[ワークスペース] メニューで **[アクセス制御 (IAM)]** を選択します。
    1. 上部のボタン バーから、 **[追加]** を選択し、 **[ロールの割り当ての追加]** を選択します。 **[ロールの割り当ての追加]** オプションが無効な場合は、ロールを割り当てるためのアクセス許可がありません。
    1. 表示される新しいパネルで、適切なロールを割り当てます。

        | Role | 状況 |
        | --- | --- |
        | [**Microsoft Sentinel レスポンダー**](../role-based-access-control/built-in-roles.md#microsoft-sentinel-responder) | プレイブックには、インシデントまたはウォッチリストを更新するための手順が記載されています |
        | [**Microsoft Sentinel 閲覧者**](../role-based-access-control/built-in-roles.md#microsoft-sentinel-reader) | プレイブックはインシデントのみを受け取ります |
        |

        [Microsoft Sentinel で利用可能なロール](./roles.md)の詳細について参照してください。
    1. **[アクセスの割り当て先]** で **[ロジック アプリ]** を選択します。
    1. プレイブックが属しているサブスクリプションを選択し、プレイブック名を選択します。
    1. **[保存]** を選択します。

1. Microsoft Sentinel Logic Apps コネクタで、マネージド ID の認証方法を有効にします。

    1. Logic Apps デザイナーで、Microsoft Sentinel Logic Apps コネクタの手順を追加します。 コネクタが既存の接続に対して既に有効になっている場合は、 **[接続の変更]** リンクをクリックします。

        ![接続の変更](media/authenticate-playbooks-to-sentinel/change-connection.png)

    1. 結果の接続一覧で、下部にある **[新規追加]** を選択します。 

    1. **[マネージド ID を使用して接続する] (プレビュー)** を選択して、新しい接続を作成します。

        ![マネージド ID オプション](media/authenticate-playbooks-to-sentinel/auth-methods-msi-choice.png)

    1. この接続の名前を入力し、 **[システム割り当て済みマネージド ID]** を選択し、 **[作成]** を選択します。

        ![マネージド ID を使用して接続する](media/authenticate-playbooks-to-sentinel/auth-methods-msi.png)

### <a name="authenticate-as-an-azure-ad-user"></a>Azure AD ユーザーとして認証する

接続を確立するには、 **[サインイン]** を選択します。 アカウント情報を提供するよう求められます。 完了したら、画面の残りの手順に従って接続を作成します。

### <a name="authenticate-as-a-service-principal-azure-ad-application"></a>サービス プリンシパルとしての認証 (Azure Active Directory アプリケーション)

サービス プリンシパルは、Azure Active Directory アプリケーションを登録することによって作成できます。 ユーザー アカウントを使用する代わりに、登録済みのアプリケーションをコネクタの ID として使用することを **お勧めします**。これは、アクセス許可を制御し、資格情報を管理し、コネクタの使用に関する特定の制限を有効にできるよりよい方法であるためです。

Microsoft Sentinel コネクタで独自のアプリケーションを使用するには、次の手順を実行します。

1. Azure Active Directory にアプリケーションを登録し、サービス プリンシパルを作成します。 方法については、[こちら](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)をご覧ください。

1. 資格情報を取得します (将来の認証用)。

    登録済みアプリケーション ブレードで、サインインするためのアプリケーションの資格情報を取得します。

    - **[クライアント ID]** : **[概要]** の下
    - **[クライアント シークレット]** : **[証明書とシークレット]** の下。

1. Microsoft Sentinel ワークスペースに対するアクセス許可を付与します。

    この手順では、アプリによって Microsoft Sentinel ワークスペースを操作するためのアクセス許可が取得されます。

    1. Microsoft Sentinel ワークスペースで、 **[設定]**  ->  **[ワークスペース設定]**  ->  **[アクセス制御 (IAM)]** に移動します

    1. **[ロールの割り当ての追加]** を選択します。

    1. アプリケーションに割り当てるロールを選択します。 たとえば、インシデントの更新など、Sentinel ワークスペースで変更を行うアクションをアプリケーションで実行できるようにするには、**Microsoft Sentinel 共同作成者** ロールを選択します。 データの読み取りのみを行うアクションの場合、**Microsoft Sentinel 閲覧者** ロールで十分です。 [Microsoft Sentinel で利用可能なロールの詳細について参照してください](./roles.md)。

    1. 必要なアプリケーションを見つけて保存します。 既定では、Azure AD アプリケーションは、使用可能なオプションに表示されません。 アプリケーションを見つけるには、名前を検索し、その名前を選択します。

1. 認証

    この手順では、アプリの資格情報を使用して、Azure Logic Apps で Azure Sentinel コネクタを認証します。

    - **[サービス プリンシパルを使用して接続する]** を選択します。

        ![[サービス プリンシパル] オプション](media/authenticate-playbooks-to-sentinel/auth-methods-spn-choice.png)

    - 必要なパラメーターを入力します (登録済みアプリケーション ブレードにあります)。
        - **[テナント]** : **[概要]** の下
        - **[クライアント ID]** : **[概要]** の下
        - **[クライアント シークレット]** : **[証明書とシークレット]** の下
        
        ![サービス プリンシパルを使用して接続する](media/authenticate-playbooks-to-sentinel/auth-methods-spn.png)

### <a name="manage-your-api-connections"></a>API 接続を管理する

認証が初めて作成されるたびに、種類が API 接続の新しい Azure リソースが作成されます。 同じ API 接続を、同じリソース グループ内のすべての Microsoft Sentinel のアクションとトリガーで使用できます。

すべての API 接続は、**API 接続** ブレード (Azure portal で *API 接続* を検索) で見つかります。

また、 **[リソース]** ブレードに移動し、種類 *[API 接続]* で表示をフィルター処理することでも見つけられます。 この方法で、一括操作で複数の接続を選択できます。

既存の接続の承認を変更するには、接続リソースを入力し、 **[API 接続の編集]** を選択します。

## <a name="next-steps"></a>次のステップ

この記事では、Microsoft Sentinel に Logic Apps ベースのプレイブックを認証するためのさまざまな方法について説明しました。

- [プレイブックでのトリガーとアクションの使用](playbook-triggers-actions.md)方法の詳細について参照してください。
