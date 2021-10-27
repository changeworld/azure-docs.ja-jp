---
title: インスタンスと認証を設定する (CLI)
titleSuffix: Azure Digital Twins
description: CLI を使用して Azure Digital Twins サービスのインスタンスを設定する方法について説明します。
author: baanders
ms.author: baanders
ms.date: 10/13/2021
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy22q2
ms.openlocfilehash: 615680552bc1854c650477f314c4ab14815dbc01
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "130000216"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-cli"></a>Azure Digital Twins インスタンスと認証を設定する (CLI)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

この記事では、**新しい Azure Digital Twins インスタンスを設定する** 手順 (インスタンスの作成と認証の設定を含む) について説明します。 この記事を完了すると、Azure Digital Twins インスタンスのプログラミングを開始する準備が完了します。

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="set-up-cloud-shell-session"></a>Cloud Shell セッションの設定
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="create-the-azure-digital-twins-instance"></a>Azure Digital Twins インスタンスを作成する

このセクションでは、Cloud Shell コマンドを使用して、**Azure Digital Twins の新しいインスタンスを作成** します。 次のものを指定する必要があります。
* インスタンスがデプロイされるリソース グループ。 既存のリソース グループがまだ念頭にない場合は、ここで次のコマンドを使用して作成できます。
    ```azurecli-interactive
    az group create --location <region> --name <name-for-your-resource-group>
    ```
* デプロイのためのリージョン。 Azure Digital Twins をサポートしているリージョンを確認するには、「[リージョン別の利用可能な Azure 製品](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins)」を参照してください。
* インスタンスの名前。 サブスクリプションのリージョンに、指定した名前がすでに使用されている別の Azure Digital Twins インスタンスがある場合は、別の名前を選択するように求められます。

これらの値を使用して、[az dt command](/cli/azure/dt?view=azure-cli-latest&preserve-view=true) コマンドでインスタンスを作成します。

```azurecli-interactive
az dt create --dt-name <name-for-your-Azure-Digital-Twins-instance> --resource-group <your-resource-group> --location <region>
```

### <a name="verify-success-and-collect-important-values"></a>正常に実行されたことを確認して重要な値を収集する

インスタンスが正常に作成された場合、Cloud Shell の結果は次のようになり、作成したリソースに関する情報が出力されます。

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/create-instance.png" alt-text="Azure portal でリソース グループと Azure Digital Twins インスタンスが正常に作成された Cloud Shell ウィンドウのスクリーンショット。":::

Azure Digital Twins インスタンスの **hostName**、**name**、**resourceGroup** を出力から確認します。 これらの値はすべて重要であり、Azure Digital Twins インスタンスの認証および関連する Azure リソースを設定する引き続きの操作で、必要になる可能性があります。 他のユーザーがこのインスタンスに対してプログラミングする場合は、これらの値をそのユーザーと共有する必要があります。

> [!TIP]
> これらのプロパティは、インスタンスのすべてのプロパティと共に、`az dt show --dt-name <your-Azure-Digital-Twins-instance>` を実行することでいつでも確認できます。

これで、Azure Digital Twins インスタンスの準備が完了しました。 次に、適切な Azure ユーザーにそれを管理するためのアクセス許可を付与します。

## <a name="set-up-user-access-permissions"></a>ユーザーのアクセス許可を設定する

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

### <a name="prerequisites-permission-requirements"></a>前提条件:権限の要件

[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

### <a name="assign-the-role"></a>ロールを割り当てる

ユーザーに Azure Digital Twins インスタンスを管理するためのアクセス許可を付与するには、そのインスタンス内の **"Azure Digital Twins Data Owner" (Azure Digital Twins データ所有者)** ロールをそのユーザーに割り当てる必要があります。

ロールを割り当てるには、次のコマンドを使用します (Azure サブスクリプションで、[十分なアクセス許可](#prerequisites-permission-requirements)を持つユーザーによって実行される必要があります)。 このコマンドを実行するには、ロールを割り当てる必要があるユーザーの Azure AD アカウントで、"*ユーザー プリンシパル名*" を渡す必要があります。 ほとんどの場合、これの値は Azure AD アカウントのユーザーの電子メールと同じです。

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<Azure-AD-user-principal-name-of-user-to-assign>" --role "Azure Digital Twins Data Owner"
```

このコマンドの結果として、作成されたロールの割り当てに関する情報が出力されます。

> [!NOTE]
> このコマンドによって、CLI が **グラフ データベースでユーザーまたはサービス プリンシパルを見つけることができない** というエラーが返される場合は、次のことを行います。
>
> 代わりに、ユーザーの "*オブジェクト ID*" を使用して、ロールを割り当てます。 これは、個人の [Microsoft アカウント (MSA)](https://account.microsoft.com/account) を使用するユーザーの場合に当てはまります。 
>
> [Azure Active Directory ユーザーの Azure portal ページ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers)を使用して、ユーザー アカウントを選択し、その詳細を開きます。 そのユーザーの "*ObjectID*" をコピーします。
>
> :::image type="content" source="media/includes/user-id.png" alt-text="[オブジェクト ID] フィールドで GUID が強調表示されている、Azure portal のユーザー ページのスクリーンショット。" lightbox="media/includes/user-id.png":::
>
> 次に、前述の `assignee` パラメーターにユーザーの "*オブジェクト ID*" を使用して、ロールの割り当てリスト コマンドを繰り返します。

### <a name="verify-success"></a>成功を確認する

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

これで Azure Digital Twins インスタンスの準備が完了し、それを管理するためのアクセス許可が割り当てられました。

## <a name="next-steps"></a>次のステップ

Azure Digital Twins CLI コマンドを使用して、インスタンスでの個別の REST API 呼び出しをテストします。 
* [az dt reference](/cli/azure/dt?view=azure-cli-latest&preserve-view=true)
* [Azure Digital Twins CLI コマンド セット](concepts-cli.md)

または、認証コードを使用してクライアント アプリケーションをインスタンスに接続する方法を確認します。
* [アプリ認証コードを作成する](how-to-authenticate-client.md)