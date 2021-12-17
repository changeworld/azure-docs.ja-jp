---
title: インスタンスと認証を設定する (ポータル)
titleSuffix: Azure Digital Twins
description: Azure portal を使用して、Azure Digital Twins サービスのインスタンスを設定する方法について説明します
author: baanders
ms.author: baanders
ms.date: 9/2/2021
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy21q2, subject-rbac-steps
ms.openlocfilehash: 2b7522c91ce5cb60683ce9be905a263a5cb99e52
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "129984822"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-portal"></a>Azure Digital Twins インスタンスと認証を設定する (ポータル)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

この記事では、**新しい Azure Digital Twins インスタンスを設定する** 手順 (インスタンスの作成と認証の設定を含む) について説明します。 この記事を完了すると、Azure Digital Twins インスタンスのプログラミングを開始する準備が整います。

この記事のこのバージョンでは、Azure portal を使用して、これらの手順を 1 つずつ手動で実行します。 Azure portal は、コマンドライン ツールに代えて使用できる、Web ベースの統合コンソールです。

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]

## <a name="create-the-azure-digital-twins-instance"></a>Azure Digital Twins インスタンスを作成する

[!INCLUDE [digital-twins-setup-portal.md](../../includes/digital-twins-setup-portal.md)]

3. 以下の **[リソースの作成]** ページで、次に示した値を入力します。
    * **サブスクリプション**:使用している Azure サブスクリプション
      - **[リソース グループ]** :インスタンスのデプロイ先にするリソース グループ。 検討できる既存のリソース グループがまだない場合は、 *[新規作成]* リンクを選択して新しいリソース グループの名前を入力することで、ここで作成することができます
    * **[場所]** :デプロイする Azure Digital Twins 対応のリージョン。 リージョンのサポートに関する詳細については、"[リージョン別の利用可能な Azure 製品 (Azure Digital Twins)](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins) " に関するページを参照してください。
    * **[リソース名]** : Azure Digital Twins インスタンスの名前。 サブスクリプションのリージョンに、指定した名前がすでに使用されている別の Azure Digital Twins インスタンスがある場合は、別の名前を選択するように求められます。
    * **[リソースへのアクセスを許可する]** : このセクションのボックスをオンにすると、インスタンス内のデータにアクセスして管理するためのアクセス許可が Azure アカウントに付与されます。 ご自分でインスタンスを管理する場合は、ここでこのチェック ボックスをオンにしてください。 サブスクリプションにアクセス許可がないため、これが淡色表示されている場合は、リソースの作成を続行し、後で必要なアクセス許可を持つ人にロールを付与してもらうことができます。 このロールとインスタンスへのロールの割り当ての詳細については、次のセクション「[ユーザーのアクセス許可を設定する](#set-up-user-access-permissions)」を参照してください。

    :::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins-2.png" alt-text="Azure portal の Azure Digital Twins の [リソースの作成] プロセスのスクリーンショット。記述された値が入力されます。":::

4. 操作を完了し、インスタンスの設定をこれ以上構成しない場合は、 **[確認と作成]** を選択できます。 これにより、[概要] ページに移動し、入力したインスタンスの詳細を確認して **[作成]** で完了できます。 

    インスタンスの詳細をさらに構成する場合は、次のセクションに残りの設定タブの説明があります。

### <a name="additional-setup-options"></a>その他の設定オプション

ここでは、 **[リソースの作成]** プロセスで他のタブを使用して、設定中に構成できる追加のオプションを示します。

* **ネットワーク**:このタブでは、[Azure Private Link](../private-link/private-link-overview.md) でプライベート エンドポイントを有効にして、インスタンスがパブリック ネットワークに露出されないようにすることができます。 手順については、「[Private Link を使用してプライベート アクセスを有効にする (プレビュー)](./how-to-enable-private-link.md?tabs=portal#add-a-private-endpoint-during-instance-creation)」を参照してください。
* **[詳細設定]** :このタブでは、エンドポイントにイベントを転送するときに使用できる、インスタンスの [システムマネージド ID](concepts-route-events.md) を有効にすることができます。 Azure Digital Twins でシステム マネージド ID を使用する方法の詳細については、[Azure Digital Twins ソリューションのセキュリティ](concepts-security.md#managed-identity-for-accessing-other-resources)に関するページを参照してください。
* **タグ**:このタブでは、Azure リソース間での整理に役立てるために、インスタンスにタグを追加できます。 Azure リソース タグの詳細については、[論理的な組織化のためのリソース、リソース グループ、およびサブスクリプションへのタグ付け](../azure-resource-manager/management/tag-resources.md)に関する記事を参照してください。

### <a name="verify-success-and-collect-important-values"></a>正常に実行されたことを確認して重要な値を収集する

**[作成]** を選択してインスタンスの設定を終了すると、ポータル アイコン バーの Azure 通知にインスタンスのデプロイの状態が表示されます。 デプロイが成功すると通知が表示され、その時点で、作成したインスタンスを表示するための _[リソースに移動]_ ボタンを選択できるようになります。

:::image type="content" source="media/how-to-set-up-instance/portal/notifications-deployment.png" alt-text="Azure portal での、デプロイの成功の表示と [リソースに移動] ボタンの強調表示がある Azure 通知のスクリーンショット。":::

デプロイが失敗した場合は、その理由が通知に示されます。 エラー メッセージの通知内容を確認して、インスタンスの作成を再試行します。

>[!TIP]
>インスタンスが作成されたら、Azure portal の検索バーでインスタンスの名前を検索して、いつでもそのページに戻ることができます。

インスタンスの *[概要]* ページで、 **[名前]** 、 **[リソース グループ]** 、および **[ホスト名]** をメモします。 これらの値はすべて重要であり、Azure Digital Twins インスタンスの操作を続行する際に使用が必要になる可能性のある重要な値です。 他のユーザーがそのインスタンスに対してプログラミングする場合は、これらの値を彼らと共有する必要があります。

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="Azure Digital Twins インスタンスの [概要] ページで重要な値が強調表示されている Azure portal のスクリーンショット。":::

これで、Azure Digital Twins インスタンスの準備が完了しました。 次に、適切な Azure ユーザーにそれを管理するためのアクセス許可を付与します。

## <a name="set-up-user-access-permissions"></a>ユーザーのアクセス許可を設定する

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

Azure Digital Twins でユーザーへのロールの割り当てを作成するには、次の 2 つの方法があります。
* [Azure Digital Twins インスタンスの作成時](#assign-the-role-during-instance-creation)
* [Azure Identity Management (IAM) を使用する](#assign-the-role-using-azure-identity-management-iam)

どちらも同じアクセス許可が必要です。

### <a name="prerequisites-permission-requirements"></a>前提条件:権限の要件

[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

### <a name="assign-the-role-during-instance-creation"></a>インスタンスの作成時にロールを割り当てる

[この記事で前述した](#create-the-azure-digital-twins-instance)プロセスを使用して Azure Digital Twins リソースを作成する際に、 **[リソースへのアクセスを許可する]** で **[Azure Digital Twins データ所有者ロールを割り当てる]** を選択します。 これにより、データ プレーン API へのフル アクセスが許可されます。

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins-2-role.png" alt-text="Azure portal での Azure Digital Twins の [リソースの作成] プロセスのスクリーンショット。[リソースへのアクセスを許可する] の下のチェック ボックスが強調表示されています。":::

ID にロールを割り当てるアクセス許可を持ってない場合、ボックスは淡色表示になります。

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins-2-role-greyed.png" alt-text="Azure portal での Azure Digital Twins の [リソースの作成] プロセスのスクリーンショット。[リソースへのアクセスを許可する] の下のチェック ボックスが無効になっています。":::

その場合でも、引き続き Azure Digital Twins リソースを正常に作成できますが、適切なアクセス許可を持つ人に、このロールを自分またはインスタンスのデータを管理する人に割り当ててもらう必要があります。

### <a name="assign-the-role-using-azure-identity-management-iam"></a>Azure Identity Management (IAM) を使用してロールを割り当てる

Azure Identity Management (IAM) のアクセス制御オプションを使用して、**Azure Digital Twins データ所有者** ロールを割り当てることもできます。

1. 最初に、Azure portal 上の Azure Digital Twins インスタンスのページを開きます。 

1. **[アクセス制御 (IAM)]** を選択します。

1. **[追加]**  >  **[ロールの割り当ての追加]** を選択して、[ロールの割り当ての追加] ページを開きます。

1. **Azure Digital Twins データ所有者** ロールを割り当てます。 詳細な手順については、「[Azure portal を使用して Azure ロールを割り当てる](../role-based-access-control/role-assignments-portal.md)」を参照してください。
    
    | 設定 | 値 |
    | --- | --- |
    | Role | [Azure Digital Twins データ所有者](../role-based-access-control/built-in-roles.md#azure-digital-twins-data-owner) |
    | アクセスの割り当て先 | ユーザー、グループ、またはサービス プリンシパル |
    | メンバー | 割り当てるユーザーの名前または電子メール アドレスを検索します。 |
    
    ![[ロールの割り当ての追加] ページ](../../includes/role-based-access-control/media/add-role-assignment-page.png)

### <a name="verify-success"></a>成功を確認する

*[アクセス制御 (IAM)] > [ロールの割り当て]* 下で設定したロールの割り当てを確認できます。 一覧に *"Azure Digital Twins Data Owner" (Azure Digital Twins データ所有者)* のロールを持つユーザーが表示されます。 

:::image type="content" source="media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Azure portal での Azure Digital Twins インスタンスのロールの割り当てのスクリーンショット。":::

これで、Azure Digital Twins インスタンスの準備が完了し、これを管理するためのアクセス許可が割り当てられました。

## <a name="next-steps"></a>次のステップ

Azure Digital Twins CLI コマンドを使用して、インスタンスでの個別の REST API 呼び出しをテストします。 
* [az dt reference](/cli/azure/dt?view=azure-cli-latest&preserve-view=true)
* [Azure Digital Twins CLI コマンド セット](concepts-cli.md)

または、認証コードを使用してクライアント アプリケーションをインスタンスに接続する方法を確認します。
* [アプリ認証コードを作成する](how-to-authenticate-client.md)