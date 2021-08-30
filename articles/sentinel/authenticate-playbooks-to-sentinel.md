---
title: Azure Sentinel に対してプレイブックを認証する | Microsoft Docs
description: プレイブックに Azure Sentinel へのアクセス権を付与して、是正措置を実行するための承認方法について説明します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2021
ms.author: yelevin
ms.openlocfilehash: 5fbe518e894cf6b1dad1407edcc241dc141ef546
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2021
ms.locfileid: "114284164"
---
# <a name="authenticate-playbooks-to-azure-sentinel"></a>Azure Sentinel に対してプレイブックを認証する

Azure Logic Apps の動作のしくみは、Azure Sentinel 自体を含めて、連携するすべての種類のすべてのリソースに対し、独立した接続を持ち、個別に認証する必要があります。 Azure Logic Apps は、この目的で、各リソースの種類に独自のコネクタを持つ[専用のコネクタ](/connectors/connector-reference/)を使用します。 このドキュメントでは、[Azure Logic Apps Azure Sentinel コネクタ](/connectors/azuresentinel/)での接続と認証の種類について説明します。これは、プレイブックがワークスペースのテーブル内の情報にアクセスするための Microsoft Azure Sentinel との対話に使用できます。

このドキュメントは、[プレイブックのトリガーとアクションを使用する](playbook-triggers-actions.md)ためのガイドと共に、他のプレイブック ドキュメント「[チュートリアル: Azure Sentinel で Automation ルールを使用してプレイブックを使用する](tutorial-respond-threats-playbook.md)」と併せて使用するガイドです。

プレイブックの概要については、「[Azure Sentinel でプレイブックを使用して脅威の応答を自動化する](automate-responses-with-playbooks.md)」を参照してください。

Azure Sentinel コネクタの完全な仕様については、[Azure Logic Apps のドキュメント](/connectors/azuresentinel/)を参照してください。

## <a name="authentication"></a>認証

Azure Logic Apps 内の Azure Sentinel コネクタとそのコンポーネントのトリガーとアクションは、関連するワークスペースで必要なアクセス許可 (読み取りおよび書き込み) を持つ任意の ID の代わり使用できます。 コネクタは、次の複数の ID の種類をサポートします。

- [マネージド ID (プレビュー)](#authenticate-with-managed-identity)
- [Azure AD ユーザー](#authenticate-as-an-azure-ad-user)
- [サービス プリンシパル (Azure AD アプリケーション)](#authenticate-as-a-service-principal-azure-ad-application)

    ![認証オプション](media/authenticate-playbooks-to-sentinel/auth-methods.png)

### <a name="permissions-required"></a>必要なアクセス許可

| ロールまたはコネクタ コンポーネント | トリガー | "Get" アクション | インシデントの更新、<br>コメントの追加 |
| ------------- | :-----------: | :------------: | :-----------: |
| **[Azure Sentinel 閲覧者](../role-based-access-control/built-in-roles.md#azure-sentinel-reader)** | &#10003; | &#10003; | &#10007; |
| **Azure Sentinel [レスポンダー](../role-based-access-control/built-in-roles.md#azure-sentinel-responder)/[共同作成者](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)** | &#10003; | &#10003; | &#10003; |
| 

「[Azure Sentinel のアクセス許可](./roles.md)」を参照してください。

### <a name="authenticate-with-managed-identity"></a>マネージド ID による認証

この認証方法を使用すると、プレイブック (Azure Logic Apps ワークフロー リソース) にアクセス許可を直接付与できるため、プレイブックによって実行される Azure Sentinel コネクタのアクションは、Azure Sentinel に対する独自のアクセス許可を持つ独立したオブジェクトであるかのように、プレイブックに代わって動作します。 この方法を使用すると、管理する必要がある ID の数が減少します。 

マネージド ID を使用して認証するには、次の手順を実行します。

1. Azure Logic Apps ワークフロー リソースで[マネージド ID を有効](../logic-apps/create-managed-service-identity.md#enable-system-assigned-identity-in-azure-portal)にします。 まとめ

    - ロジック アプリのメニューの **[設定]** で、 **[ID]** を選択します。 **[システム割り当て済み]、[On]、[保存]** の順に選択します。 確認を求めるメッセージが表示されたら、 **[はい]** を選択します。

    - これで、ロジック アプリでシステム割り当て ID を使用できるようになりました。これは Azure AD に登録され、オブジェクト ID で表されます。

1. [Azure Sentinel 共同作業者](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)ロールを割り当てることにより、Azure Sentinel ワークスペースへの[アクセス権をその ID に付与](../logic-apps/create-managed-service-identity.md#give-identity-access-to-resources)します。

    [Azure Sentinel で利用可能なロール](./roles.md)の詳細について参照してください。

1. Azure Sentinel Logic Apps コネクタで、マネージド ID の認証方法を有効にします。

    1. Azure Logic Apps デザイナーで、Azure Sentinel Logic Apps コネクタの手順を追加します。 コネクタが既存の接続に対して既に有効になっている場合は、 **[接続の変更]** リンクをクリックします。

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

Azure Sentinel コネクタで独自のアプリケーションを使用するには、次の手順を実行します。

1. Azure Active Directory にアプリケーションを登録し、サービス プリンシパルを作成します。 方法については、[こちら](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)をご覧ください。

1. 資格情報を取得します (将来の認証用)。

    登録済みアプリケーション ブレードで、サインインするためのアプリケーションの資格情報を取得します。

    - **[クライアント ID]** : **[概要]** の下
    - **[クライアント シークレット]** : **[証明書とシークレット]** の下。

1. Azure Sentinel ワークスペースに対するアクセス許可を付与します。

    この手順では、アプリは Azure Sentinel ワークスペースを操作するためのアクセス許可を取得します。

    1. Azure Sentinel ワークスペースで、 **[設定]**  ->  **[ワークスペース設定]**  ->  **[アクセス制御 (IAM)]** に移動

    1. **[ロールの割り当ての追加]** を選択します。

    1. アプリケーションに割り当てるロールを選択します。 たとえば、インシデントの更新など、Sentinel ワークスペースで変更を行うアクションをアプリケーションが実行できるようにするには、**Azure Sentinel 共同作成者** ロールを選択します。 データの読み取りのみを行うアクションの場合、**Azure Sentinel 閲覧者** ロールで十分です。 [Azure Sentinel で利用可能なロールの詳細について参照してください](./roles.md)。

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

認証が初めて作成されるたびに、種類が API 接続の新しい Azure リソースが作成されます。 同じ API 接続を、同じリソース グループ内のすべての Azure Sentinel のアクションとトリガーで使用できます。

すべての API 接続は、**API 接続** ブレード (Azure portal で *API 接続* を検索) で見つかります。

また、 **[リソース]** ブレードに移動し、種類 *[API 接続]* で表示をフィルター処理することでも見つけられます。 この方法で、一括操作で複数の接続を選択できます。

既存の接続の承認を変更するには、接続リソースを入力し、 **[API 接続の編集]** を選択します。

## <a name="next-steps"></a>次のステップ

この記事では、Azure Sentinel に Azure Logic Apps ベースのプレイブックを認証するためのさまざまな方法について説明しました。
- [プレイブックでのトリガーとアクションの使用](playbook-triggers-actions.md)方法の詳細について参照してください。