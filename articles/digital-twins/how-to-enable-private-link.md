---
title: Private Link を使用してプライベート アクセスを有効にする (プレビュー)
titleSuffix: Azure Digital Twins
description: Private Link を使用して Azure Digital Twins ソリューション用のプライベート アクセスを有効にする方法を説明します。
author: baanders
ms.author: baanders
ms.date: 7/12/2021
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy22q1
ms.openlocfilehash: 93435355a2d75a2346d076c44b377726789431a3
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114452337"
---
# <a name="enable-private-access-with-private-link-preview"></a>Private Link を使用してプライベート アクセスを有効にする (プレビュー)

この記事では、[Azure Digital Twins インスタンス用のプライベート エンドポイントを使用して Private Link を有効にする](concepts-security.md#private-network-access-with-azure-private-link-preview)さまざまな方法について説明します (現在プレビュー段階)。 Azure Digital Twins インスタンス用のプライベート エンドポイントを構成することで、Azure Digital Twins インスタンスをセキュリティで保護して公開されないようにすることに加え、[Azure Virtual Network (VNet)](../virtual-network/virtual-networks-overview.md) からのデータ流出を回避できます。

この記事では、次の手順を説明します。 
1. Private Link を有効にし、Azure Digital Twins インスタンス用のプライベート エンドポイントを構成します。
1. インスタンスのプライベート エンドポイントを表示、編集、または削除します。
1. パブリック ネットワーク アクセス フラグを無効または有効にして、Private Link 接続のみに API アクセスを制限します。

## <a name="prerequisites"></a>前提条件

プライベート エンドポイントをセットアップする前に、エンドポイントをデプロイできる [Azure Virtual Network (VNet)](../virtual-network/virtual-networks-overview.md)  が必要です。 VNet がまだない場合は、[Azure Virtual Network のいずれかのクイックスタート](../virtual-network/quick-create-portal.md)に従って、これを設定できます。

## <a name="add-a-private-endpoint-to-azure-digital-twins"></a>Azure Digital Twins にプライベート エンドポイントを追加する 

[Azure portal](https://portal.azure.com) または [Azure CLI](/cli/azure/what-is-azure-cli) を使用して、Azure Digital Twins インスタンス用のプライベート エンドポイントを含む Private Link を有効にすることができます。 

インスタンスの初期セットアップの一部として Private Link を設定する場合は、Azure portal を使用する必要があります。 作成後のインスタンスで Private Link を有効にする場合は、Azure portal または Azure CLI を使用できます。 どちらの作成方法でも、構成オプションと、インスタンスに対する結果は同じです。

以下のセクションのタブを使用して、好みのエクスペリエンスの手順を選択してください。

>[!TIP]
> また、Azure Digital Twins インスタンスを使用するのではなく、Private Link サービスを使用して Private Link エンドポイントを設定することもできます。 その場合も、構成オプションと最終的な結果は同じです。
>
> Private Link リソースの設定の詳細については、[Azure portal](../private-link/create-private-endpoint-portal.md)、[Azure CLI](../private-link/create-private-endpoint-cli.md)、[Azure Resource Manager](../private-link/create-private-endpoint-template.md)、または [PowerShell](../private-link/create-private-endpoint-powershell.md) の Private Link に関するドキュメントを参照してください。

### <a name="add-a-private-endpoint-during-instance-creation"></a>インスタンスの作成時にプライベート エンドポイントを追加する

このセクションでは、Azure Digital Twins のインスタンスの初期セットアップの一部として、Private Link を使用するプライベート エンドポイントを作成します。 この作業は、Azure portal でのみ行うことができます。

# <a name="portal"></a>[ポータル](#tab/portal)

このセクションでは、Azure portal で Azure Digital Twins のインスタンスを設定する間に、Private Link を有効にする方法について説明します。 

Private Link のオプションは、インスタンスのセットアップの **[ネットワーク]** タブにあります。

1. Azure portal で Azure Digital Twins のインスタンスのセットアップを始めます。 手順については、[インスタンスと認証の設定](how-to-set-up-instance-portal.md)に関する記事を参照してください。
1. インスタンスのセットアップで **[ネットワーク]** タブまで来たら、 **[接続方法]** の **[プライベート エンドポイント]** オプションを選択することにより、プライベート エンドポイントを有効にできます。

    これによって追加される **[プライベート エンドポイント接続]** というセクションで、プライベート エンドポイントの詳細を構成できます。 **[+ 追加]** ボタンをクリックして続けます。
    
    :::image type="content" source="media/how-to-enable-private-link/create-instance-networking-1.png" alt-text="新しい Azure Digital Twins インスタンスの [ネットワーク] タブを示す Azure portal のスクリーンショット。プライベート エンドポイントを作成する方法が強調表示されています。[追加] ボタンが強調表示されています。" lightbox="media/how-to-enable-private-link/create-instance-networking-1.png":::

1. **[プライベート エンドポイントの作成]** ページが表示されたら、新しいプライベート エンドポイントの詳細を入力します。

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-full.png" alt-text="[プライベート エンドポイントの作成] ページが表示されている Azure portal のスクリーンショット。以下で説明するフィールドが含まれます。":::

    1. **[サブスクリプション]** と **[リソース グループ]** の選択を入力します。 **[場所]** を、使用する VNet と同じ場所に設定します。 エンドポイントの **[名前]** を選択し、 **[Target sub-resources]\(ターゲット サブリソース\)** で *[API]* を選択します。

    1. 次に、エンドポイントのデプロイに使用する **[仮想ネットワーク]** と **[サブネット]** を選択します。

    1. 最後に、 **[プライベート DNS ゾーンと統合する]** かどうかを選択します。 既定の **[はい]** をそのまま使用できます。または、このオプションの詳細については、ポータルのリンクに従って、[プライベート DNS 統合の詳細を確認](../private-link/private-endpoint-overview.md#dns-configuration)してください。

    1. 構成オプションを設定した後、 **[OK]** を選択して完了します。

1. これにより、Azure Digital Twins のインスタンスのセットアップの **[ネットワーク]** タブに戻ります。 新しいエンドポイントが **[プライベート エンドポイントの接続]** に表示されていることを確認します。
    
    :::image type="content" source="media/how-to-enable-private-link/create-instance-networking-2.png" alt-text="新しく作成されたプライベート エンドポイントがある Azure Digital Twins の [ネットワーク] タブが表示されている Azure portal のスクリーンショット。" lightbox="media/how-to-enable-private-link/create-instance-networking-2.png":::

1. 下部にあるナビゲーション ボタンを使用して、インスタンスの残りのセットアップを続けることができます。

# <a name="cli"></a>[CLI](#tab/cli)

Azure CLI を使用したインスタンスの作成では、Private Link エンドポイントを追加することはできません。 

インスタンス作成の間に Azure portal に切り替えてエンドポイントを追加するか、インスタンスの作成後に次のセクションに進んで CLI を使用してプライベート エンドポイントを追加することができます。

--- 

### <a name="add-a-private-endpoint-to-an-existing-instance"></a>既存のインスタンスにプライベート エンドポイントを追加する

このセクションでは、既に存在する Azure Digital Twins インスタンスでプライベート エンドポイントを備えた Private Link を有効にします。

# <a name="portal"></a>[ポータル](#tab/portal)

1. まず、ブラウザーで [Azure portal](https://portal.azure.com) に移動します。 ポータルの検索バーで名前を検索して、Azure Digital Twins のインスタンスを表示します。

1. 左側のメニューで、 **[Networking (preview)]\(ネットワーク (プレビュー)\)** を選択します。

1. **[プライベート エンドポイント接続]** タブに切り替えます。

1. **[+ プライベート エンドポイント]** を選択して、 **[プライベート エンドポイントの作成]** の設定を開きます。

    :::image type="content" source="media/how-to-enable-private-link/add-endpoint-digital-twins.png" alt-text="既存の Azure Digital Twins インスタンスの [ネットワーク] ページが表示されている Azure portal のスクリーンショット。プライベート エンドポイントの作成方法が強調表示されています。" lightbox="media/how-to-enable-private-link/add-endpoint-digital-twins.png":::

1.  **[基本]**  タブで、プロジェクトの **[サブスクリプション]** と **[リソース グループ]** 、およびエンドポイントの **[名前]** と **[リージョン]** を入力または選択します。 リージョンは、使用している VNet のリージョンと同じにする必要があります。

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-1.png" alt-text="[プライベート エンドポイントの作成] ダイアログの最初の ([基本]) タブが表示されている Azure portal のスクリーンショット。上で説明したフィールドが含まれます。":::

    終わったら、 **[次: リソース >]** ボタンを選択して、次のタブに進みます。

1. **[リソース]** タブで、次の情報を入力または選択します。 
    * **[接続方法]** : **[マイ ディレクトリ内の Azure リソースに接続します]** を選択して、お使いの Azure Digital Twins インスタンスを検索します。
    * **サブスクリプション**:お使いのサブスクリプションを入力します。
    * **[リソースの種類]** :**Microsoft.DigitalTwins/digitalTwinsInstances** を選択します
    * **リソース**:お使いの Azure Digital Twins インスタンスの名前を選択します。
    * **[Target sub-resources]\(ターゲット サブリソース\)** : **[API]** を選択します。

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-2.png" alt-text="[プライベート エンドポイントの作成] ダイアログの 2 番目の ([リソース]) タブが表示されている Azure portal のスクリーンショット。上で説明したフィールドが含まれます。":::

    終わったら、 **[次: 構成 >]** ボタンを選択して、次のタブに進みます。    

1.  **[構成]** タブで、次の情報を入力または選択します。
    * **仮想ネットワーク**:仮想ネットワークを選択します。
    * **サブネット**:仮想ネットワークからサブネットを選択します。
    * **プライベート DNS ゾーンと統合する**: **[プライベート DNS ゾーンと統合する]** かどうかを選択します。 既定の **[はい]** をそのまま使用できます。または、このオプションの詳細については、ポータルのリンクに従って、[プライベート DNS 統合の詳細を確認](../private-link/private-endpoint-overview.md#dns-configuration)してください。
    **[はい]** を選択した場合は、既定の構成情報をそのまま使用できます。

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-3.png" alt-text="[プライベート エンドポイントの作成] ダイアログの 3 番目の ([構成]) タブが表示されている Azure portal のスクリーンショット。上で説明したフィールドが含まれます。":::

    終わったら、 **[確認および作成]** ボタンを選択して、セットアップを完了します。 

1. **[確認および作成]** タブで選択内容を確認し、 **[作成]** ボタンを選択します。 

エンドポイントのデプロイが完了すると、Azure Digital Twins インスタンス用のプライベート エンドポイント接続に表示されます。

# <a name="cli"></a>[CLI](#tab/cli)

プライベート エンドポイントを作成し、Azure CLI を使用してそれを Azure Digital Twins のインスタンスにリンクするには、[az network private-endpoint create](/cli/azure/network/private-endpoint?view=azure-cli-latest&preserve-view=true#az_network_private_endpoint_create) コマンドを使用します。 `--private-connection-resource-id` パラメーターで、 Azure Digital Twins インスタンスを、その完全修飾 ID を使用して識別します。

次の例では、必要なパラメーターのみ指定したコマンドを使用して、プライベート エンドポイントを作成しています。

```azurecli-interactive
az network private-endpoint create --connection-name <private-link-service-connection> --name <name-for-private-endpoint> --resource-group <resource-group> --subnet <subnet-ID> --private-connection-resource-id "/subscriptions/<subscription-ID>/resourceGroups/<resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<Azure-Digital-Twins-instance-name>" 
```

必須と省略可能な各パラメーターの完全な一覧、およびプライベート エンドポイント作成の追加の例については、[az network private-endpoint create のリファレンス ドキュメント](/cli/azure/network/private-endpoint?view=azure-cli-latest&preserve-view=true#az_network_private_endpoint_create)を参照してください。

--- 

## <a name="manage-private-endpoint-connections"></a>プライベート エンドポイント接続を管理する

このセクションでは、作成した後でプライベート エンドポイントを表示、編集、削除する方法について説明します。

# <a name="portal"></a>[ポータル](#tab/portal)

Azure Digital Twins インスタンス用のプライベート エンドポイントを作成したら、Azure Digital Twins インスタンスの **[ネットワーク (プレビュー)]** タブでそれを表示できます。 このページには、そのインスタンスに関連付けられているすべてのプライベート エンドポイント接続が表示されます。

:::image type="content" source="media/how-to-enable-private-link/view-endpoint-digital-twins.png" alt-text="プライベート エンドポイントが 1 つある既存の Azure Digital Twins インスタンスの [ネットワーク] ページが表示されている Azure portal のスクリーンショット。" lightbox="media/how-to-enable-private-link/view-endpoint-digital-twins.png":::


エンドポイントを選択して、詳細情報を表示したり、構成設定を変更したり、接続を削除したりします。

>[!TIP]
> エンドポイントは、Azure portal の Private Link センターから表示することもできます。

# <a name="cli"></a>[CLI](#tab/cli)

Azure Digital Twins インスタンスに対してプライベート エンドポイントを作成した後、[az dt network private-endpoint connection](/cli/azure/dt/network/private-endpoint/connection?view=azure-cli-latest&preserve-view=true) コマンドを使用して、インスタンスに対するプライベート エンドポイント **接続** を引き続き管理できます。 操作には以下が含まれます。
* プライベート エンドポイント接続を表示する
* プライベート エンドポイント接続の状態を設定する
* プライベート エンドポイント接続を削除する
* インスタンスのすべてのプライベート エンドポイント接続の一覧を表示する

詳細と例については、[az dt network private-endpoint のリファレンス ドキュメント](/cli/azure/dt/network/private-endpoint?view=azure-cli-latest&preserve-view=true)を参照してください。

### <a name="get-additional-private-link-information"></a>Private Link に関するその他の情報を取得する

[az dt network private-link](/cli/azure/dt/network/private-link?view=azure-cli-latest&preserve-view=true) コマンドを使用して、インスタンスの Private Link の状態に関する追加情報を取得できます。 操作には以下が含まれます。
* Azure Digital Twins インスタンスに関連付けられている Private Link を一覧表示する
* インスタンスに関連付けられている Private Link を表示する

詳細と例については、[az dt network private-link のリファレンス ドキュメント](/cli/azure/dt/network/private-link?view=azure-cli-latest&preserve-view=true)を参照してください。

--- 

## <a name="disable--enable-public-network-access-flags"></a>パブリック ネットワーク アクセス フラグを無効または有効にする

Azure Digital Twins インスタンスの構成によって、すべてのパブリック接続を拒否し、プライベート エンドポイント経由の接続のみを許可するようにして、ネットワークのセキュリティを強化できます。 この操作は、**パブリック ネットワーク アクセス フラグ** で行います。 

このポリシーを使用すると、API アクセスを Private Link 接続のみに制限することができます。 パブリック ネットワーク アクセス フラグが "*無効*" に設定されていると、パブリック クラウドから Azure Digital Twins インスタンスのデータ プレーンへのすべての REST API 呼び出しで、`403, Unauthorized` が返されます。 または、ポリシーが "*無効*" に設定されていて、要求がプライベート エンドポイントを通して行われた場合は、API 呼び出しは成功します。

ネットワーク フラグの値は、[Azure portal](https://portal.azure.com)、[Azure CLI](/cli/azure/)、または [ARMClient コマンド ツール](https://github.com/projectkudu/ARMClient)を使用して更新できます。

# <a name="portal"></a>[ポータル](#tab/portal-2)

[Azure portal](https://portal.azure.com) でパブリック ネットワーク アクセスを無効または有効にするには、ポータルを開き、お使いの Azure Digital Twins インスタンスに移動します。

1. 左側のメニューで、 **[Networking (preview)]\(ネットワーク (プレビュー)\)** を選択します。

1. **[パブリック アクセス]** タブの **[Allow public network access to]\(パブリック ネットワーク アクセスを許可する\)** を、 **[無効]** または **[すべてのネットワーク]** に設定します。

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-enable-private-link/network-flag-portal.png" alt-text="Azure Digital Twins インスタンスの [ネットワーク] ページが表示されている Azure portal のスクリーンショット。パブリック アクセスの切り替え方法が強調表示されています。" lightbox="media/how-to-enable-private-link/network-flag-portal.png":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

    **[保存]** を選択します。

# <a name="cli"></a>[CLI](#tab/cli-2)

Azure CLI で、`--public-network-access` パラメーターを `az dt create` コマンドに追加することで、パブリック ネットワーク アクセスを無効または有効にできます。 このコマンドは、新しいインスタンスを作成する場合にも使用できますが、既存のインスタンスの名前を指定することで、既存のインスタンスのプロパティを編集する場合にも使用できます。 (このコマンドの詳細については、その[リファレンス ドキュメント](/cli/azure/dt?view=azure-cli-latest&preserve-view=true#az_dt_create)または [Azure Digital Twins インスタンスを設定するための一般的な手順](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance)に関する記事を参照してください)。

Azure Digital Twins インスタンスのパブリック ネットワーク アクセスを **無効にする** には、このような `--public-network-access` パラメーターを使用します。

```azurecli-interactive
az dt create --dt-name <name-of-existing-instance> --resource-group <resource-group> --public-network-access Disabled
```

パブリック ネットワーク アクセスが現在無効になっているインスタンスでそれを **有効にする** には、次のようなコマンドを使用します。

```azurecli-interactive
az dt create --dt-name <name-of-existing-instance> --resource-group <resource-group> --public-network-access Enabled
```

# <a name="armclient"></a>[ARMClient](#tab/arm-client-2)

[ARMClient コマンド ツール](https://github.com/projectkudu/ARMClient)では、次のコマンドを使用してパブリック ネットワーク アクセスを有効または無効にします。 

パブリック ネットワーク アクセスを **無効にする** には:
  
```cmd/sh
armclient login 

armclient PATCH /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance>?api-version=2020-12-01 "{ 'properties': { 'publicNetworkAccess': 'disabled' } }"  
```

パブリック ネットワーク アクセスを **有効にする** には:  
  
```cmd/sh
armclient login 

armclient PATCH /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance>?api-version=2020-12-01 "{ 'properties': { 'publicNetworkAccess': 'enabled' } }"  
``` 

---


## <a name="next-steps"></a>次のステップ

Azure 用 Private Link の詳細を確認します。 
* [Azure Private Link サービスとは](../private-link/private-link-service-overview.md)