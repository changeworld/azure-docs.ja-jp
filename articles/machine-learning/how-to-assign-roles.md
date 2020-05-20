---
title: ワークスペースでロールを管理する
titleSuffix: Azure Machine Learning
description: ロールベースのアクセス制御 (RBAC) を使用して、Azure Machine Learning ワークスペースにアクセスする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: larryfr
author: Blackmist
ms.date: 03/06/2020
ms.custom: seodec18
ms.openlocfilehash: 127a0a2b7f7573db91df9347169e90de3e14c4c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79232891"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Azure Machine Learning ワークスペースへのアクセスの管理
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

この記事では、Azure Machine Learning ワークスペースへのアクセスを管理する方法について説明します。 Azure リソースへのアクセスの管理には、[ロールベースのアクセス制御 (RBAC)](/azure/role-based-access-control/overview) が使用されます。 Azure Active Directory のユーザーには、リソースへのアクセス権を付与する特定のロールが割り当てられます。 Azure には、組み込みロールと、カスタム ロールを作成する機能の両方が用意されています。

## <a name="default-roles"></a>既定のロール

Azure Machine Learning ワークスペースは Azure リソースの 1 つです。 他の Azure リソースと同様、新しい Azure Machine Learning ワークスペースの作成時には次の 3 つの既定のロールが提供されます。 ワークスペースにユーザーを追加し、これらの組み込みロールのいずれかに割り当てることができます。

| Role | アクセス レベル |
| --- | --- |
| **Reader** | ワークスペースでの読み取り専用のアクション。 閲覧者はワークスペースで資産を一覧および表示できますが、これらの資産を作成または更新することはできません。 |
| **Contributor** | ワークスペース内の資産を表示、作成、編集、削除 (該当する場合) します。 たとえば、共同作成者は実験を作成したり、コンピューティング クラスターを作成またはアタッチしたり、実行を送信したり、Web サービスをデプロイしたりできます。 |
| **[所有者]** | ワークスペース内の資産を表示、作成、編集、削除 (該当する場合) する機能など、ワークスペースへのフル アクセス。 また、ロールの割り当てを変更することができます。 |

> [!IMPORTANT]
> ロール アクセス権は、Azure の複数のレベルにスコープ指定できます。 たとえば、ワークスペースへの所有者アクセス権を持つユーザーであっても、そのワークスペースが含まれるリソース グループへの所有者アクセス権を持っていないことがあります。 詳細については、「[RBAC のしくみ](/azure/role-based-access-control/overview#how-rbac-works)」を参照してください。

特定の組み込みロールの詳細については、「[Azure リソースの組み込みロール](/azure/role-based-access-control/built-in-roles)」を参照してください。

## <a name="manage-workspace-access"></a>ワークスペース アクセスの管理

ワークスペースの所有者は、ワークスペースのロールを追加および削除できます。 ユーザーにロールを割り当てることもできます。 アクセスを管理する方法を確認するには、次のリンクを使用します。
- [Azure portal UI](/azure/role-based-access-control/role-assignments-portal)
- [PowerShell](/azure/role-based-access-control/role-assignments-powershell)
- [Azure CLI](/azure/role-based-access-control/role-assignments-cli)
- [REST API](/azure/role-based-access-control/role-assignments-rest)
- [Azure リソース マネージャーのテンプレート](/azure/role-based-access-control/role-assignments-template)

[Azure Machine Learning CLI](reference-azure-machine-learning-cli.md) をインストール済みの場合は、CLI コマンドを使用してユーザーにロールを割り当てることもできます。

```azurecli-interactive 
az ml workspace share -w <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

`user` フィールドは、ワークスペースの親サブスクリプションが存在する Azure Active Directory のインスタンスにおける既存のユーザーのメール アドレスです。 このコマンドレットを使用する方法の例を次に示します。

```azurecli-interactive 
az ml workspace share -w my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

## <a name="create-custom-role"></a>カスタム ロールの作成

組み込みロールが十分ではない場合は、カスタム ロールを作成できます。 カスタム ロールには、そのワークスペース内のリソースの読み取り、書き込み、削除、コンピューティングのアクセス許可を与えることができます。 ロールは、特定のワークスペース レベル、特定のリソース グループ レベル、または特定のサブスクリプション レベルで使用できるようにすることができます。

> [!NOTE]
> そのリソース内のカスタム ロールを作成するには、そのレベルでのリソースの所有者でなければなりません。

カスタム ロールを作成するには、最初に、ロールのアクセス許可とスコープを指定するロールの定義 JSON ファイルを構築します。 次の例では、特定のワークスペース レベルにスコープ指定された "データ サイエンティスト" という名前のカスタム ロールを定義しています。

`data_scientist_role.json`:
```json
{
    "Name": "Data Scientist",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute.",
    "Actions": ["*"],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace_name>"
    ]
}
```

`AssignableScopes` フィールドを変更し、サブスクリプション レベル、リソース グループ レベル、特定のワークスペース レベルでこのカスタム ロールのスコープを設定することができます。

このカスタム ロールは、次のアクションを除くすべてのアクションをワークスペース内で実行できます。

- コンピューティング リソースを作成または更新できません。
- コンピューティング リソースを削除できません。
- ロールの割り当てを追加、削除、変更できません。
- ワークスペースを削除できません。

このカスタム ロールをデプロイするには、次の Azure CLI コマンドを使用します。

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

デプロイ後、このロールは、指定したワークスペース内で利用可能になります。 これで、このロールを Azure portal で追加し、割り当てることができるようになりました。 または、`az ml workspace share` CLI コマンドを使用して、このロールをユーザーに割り当てることができます。

```azurecli-interactive
az ml workspace share -w my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```

カスタム ロールの詳細については、「[Azure リソースのカスタム ロール](/azure/role-based-access-control/custom-roles)」を参照してください。

カスタム ロールで使用できる操作 (アクション) の詳細については、「[リソース プロバイダー操作](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)」を参照してください。


## <a name="frequently-asked-questions"></a>よく寄せられる質問


### <a name="q-what-are-the-permissions-needed-to-perform-various-actions-in-the-azure-machine-learning-service"></a>Q. Azure Machine Learning service でさまざまなアクションを実行するために必要なアクセス許可はどのようなものですか。

次の表は、Azure Machine Learning アクティビティの概要と、それらを最小のスコープで実行するために必要なアクセス許可をまとめたものです。 たとえば、ワークスペース スコープ (列 4) を使用してアクティビティを実行できる場合は、そのアクセス許可を持つそれより高いすべてのスコープも自動的に機能します。 この表内のすべてのパスは、`Microsoft.MachineLearningServices/` に対する**相対パス**です。

| アクティビティ | サブスクリプション レベルのスコープ | リソース グループレベルのスコープ | ワークスペースレベルのスコープ |
|---|---|---|---|
| 新しいワークスペースの作成 | 必要なし | 所有者または共同作成者 | 該当なし (所有者になるか、作成後に上位のスコープ ロールを継承します) |
| 新しいコンピューティング クラスターの作成 | 必要なし | 必要なし | 所有者、共同作成者、または `workspaces/computes/write` が可能なカスタム ロール |
| 新しい Notebook VM の作成 | 必要なし | 所有者または共同作成者 | サポートできません |
| 新しいコンピューティング インスタンスの作成 | 必要なし | 必要なし | 所有者、共同作成者、または `workspaces/computes/write` が可能なカスタム ロール |
| 実行の送信、データへのアクセス、モデルまたは公開パイプラインのデプロイなどのデータ プレーン アクティビティ | 必要なし | 必要なし | 所有者、共同作成者、または `workspaces/*/write` が可能なカスタム ロール <br/> また、MSI がストレージ アカウントのデータにアクセスできるようにするには、ワークスペースに登録されているデータストアも必要です。 |


### <a name="q-how-do-i-list-all-the-custom-roles-in-my-subscription"></a>Q. サブスクリプション内のすべてのカスタム ロールを一覧表示するにはどうすればよいですか。

Azure CLI で、次のコマンドを実行します。

```azurecli-interactive
az role definition list --subscription <sub-id> --custom-role-only true
```

### <a name="q-how-do-i-find-the-role-definition-for-a-role-in-my-subscription"></a>Q. サブスクリプション内のロールについてのロールの定義を見つけるにはどうすればよいですか。

Azure CLI で、次のコマンドを実行します。 `<role-name>` は、上記のコマンドで返されるのと同じ形式である必要があります。

```azurecli-interactive
az role definition list -n <role-name> --subscription <sub-id>
```

### <a name="q-how-do-i-update-a-role-definition"></a>Q. ロールの定義を更新するにはどうすればよいですか。

Azure CLI で、次のコマンドを実行します。

```azurecli-interactive
az role definition update --role-definition update_def.json --subscription <sub-id>
```

新しいロールの定義のスコープ全体に対するアクセス許可が必要であることに注意してください。 たとえば、この新しいロールが 3 つのサブスクリプションにわたってスコープを持つ場合は、3 つすべてのサブスクリプションに対するアクセス許可が必要です。 

> [!NOTE]
> ロールの更新は、そのスコープ内のすべてのロールの割り当てに適用されるまでに 15 分 ~ 1 時間かかることがあります。
### <a name="q-can-i-define-a-role-that-prevents-updating-the-workspace-edition"></a>Q. ワークスペースのエディションを更新できないようにするロールを定義できますか。 

はい。ワークスペースのエディションの更新を禁止するロールを定義できます。 ワークスペースの更新は、ワークスペース オブジェクトのパッチ呼び出しであるため、JSON 定義の `"NotActions"` 配列に次のアクションを配置して、これを行います。 

`"Microsoft.MachineLearningServices/workspaces/write"`

### <a name="q-what-permissions-are-needed-to-perform-quota-operations-in-a-workspace"></a>Q. ワークスペースでクォータ操作を実行するには、どのようなアクセス許可が必要ですか。 

ワークスペースでクォータ関連の操作を実行するには、サブスクリプション レベルのアクセス許可が必要です。 これは、マネージド コンピューティング リソースに対するサブスクリプション レベルのクォータまたはワークスペース レベルのクォータの設定は、サブスクリプション スコープで書き込みアクセス許可がある場合にのみ可能であることを意味します。 


## <a name="next-steps"></a>次のステップ

- [エンタープライズ セキュリティの概要](concept-enterprise-security.md)
- [仮想ネットワーク内での実験と推論/スコアの安全な実行](how-to-enable-virtual-network.md)
- [チュートリアル: モデルをトレーニングする](tutorial-train-models-with-aml.md)
- [リソース プロバイダー操作](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)
