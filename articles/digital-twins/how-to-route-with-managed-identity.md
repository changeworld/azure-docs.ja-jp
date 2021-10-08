---
title: マネージド ID を使用してイベントをルーティングする
titleSuffix: Azure Digital Twins
description: Azure portal または CLI を使用して、Azure Digital Twins のシステム割り当て ID を有効にし、それを使用してイベントを転送する方法を説明します。
author: baanders
ms.author: baanders
ms.date: 6/15/2021
ms.topic: how-to
ms.service: digital-twins
ms.custom: subject-rbac-steps, contperf-fy21q4
ms.openlocfilehash: 4d50c40426d5fb687b28a965b9d921ef6fc4df38
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128651923"
---
# <a name="enable-a-managed-identity-for-routing-azure-digital-twins-events"></a>マネージド ID を有効にして、Azure Digital Twins イベントをルーティングできるようにします

この記事では [Azure Digital Twins のインスタンスでシステム割り当て ID](concepts-security.md#managed-identity-for-accessing-other-resources) を有効にし、その ID を使用して、ルーティングでサポートしている宛先にイベントを転送する方法を説明します。 ルーティングにはマネージド ID の設定は必要ありませんが、[イベント ハブ](../event-hubs/event-hubs-about.md)、[Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)  宛先、[Azure Storage Container](../storage/blobs/storage-blobs-introduction.md) など、他の Azure AD で保護されたリソースにインスタンスが簡単にアクセスするのに役立ちます。

この記事では、次の手順を説明します。 

1. システム割り当て ID を使用して Azure Digital Twins インスタンスを作成するか、既存の Azure Digital Twins インスタンスでシステム割り当て ID を有効にします。 
1. ID に適切なロール (1 つまたは複数) を追加します。 たとえば、エンドポイントが Event Hubs の場合は ID に **Azure Event Hubs データ送信者** ロールを割り当て、エンドポイントが Service Bus の場合は **Azure Service Bus データ送信者ロール** を割り当てます。
1. 認証にシステム割り当て ID を使用できるエンドポイントを Azure Digital Twins に作成します。

## <a name="enable-system-managed-identity-for-the-instance"></a>インスタンスのシステム マネージド ID を有効にする 

Azure Digital Twins インスタンスでシステム割り当て ID を有効にすると、Azure によって [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) にその ID が自動的に作成されます。 その後、その ID を使用して、イベント転送のために Azure Digital Twins エンドポイントに対する認証を行うことができます。

**インスタンスの初期セットアップの一部として** Azure Digital Twins インスタンスのシステム マネージド ID を有効にすることも、**既に存在するインスタンスで後から有効にする** こともできます。

どちらの作成方法でも、インスタンスに提供される構成オプションと結果は同じです。 このセクションでは、両方を行う方法について説明します。

### <a name="add-a-system-managed-identity-during-instance-creation"></a>インスタンスの作成時にシステム マネージド ID を追加する

このセクションでは、Azure Digital Twins インスタンスの作成中に、インスタンスのシステム マネージド ID を有効にする方法について説明します。 インスタンスの作成に [Azure portal](https://portal.azure.com) と [Azure CLI](/cli/azure/what-is-azure-cli) のどちらを使用している場合でも、ID を有効にできます。 以下のタブを使用して、好みのエクスペリエンスの手順を選択してください。

# <a name="portal"></a>[ポータル](#tab/portal) 

ポータルでのインスタンスの作成中にマネージド ID を追加するには、[通常と同様にインスタンスの作成を開始します](how-to-set-up-instance-portal.md)。

システム マネージド ID オプションは、インスタンスのセットアップの **[詳細]** タブにあります。

このタブで、 **[System managed identity]\(システム マネージド ID\)** の **[オン]** オプションを選択して、この機能を有効にします。

:::image type="content" source="media/how-to-route-with-managed-identity/create-instance-advanced.png" alt-text="Azure Digital Twins の [リソースの作成] ダイアログの [詳細] タブが表示されている Azure portal のスクリーンショット。システム マネージド ID はオンになっています。":::

その後、下部にあるナビゲーション ボタンを使用して、インスタンスの残りのセットアップを続けることができます。
   
# <a name="cli"></a>[CLI](#tab/cli)

CLI では、インスタンスの作成に使用する `az dt create` コマンドに `--assign-identity` パラメーターを追加できます。 (このコマンドの詳細については、その[リファレンス ドキュメント](/cli/azure/dt#az_dt_create)または [Azure Digital Twins インスタンスを設定するための一般的な手順](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance)に関する記事を参照してください)。

システム マネージド ID を持つインスタンスを作成するには、このように `--assign-identity` パラメーターを追加します。

```azurecli-interactive
az dt create --dt-name <new-instance-name> --resource-group <resource-group> --assign-identity
```

---

### <a name="add-a-system-managed-identity-to-an-existing-instance"></a>既存のインスタンスにシステム マネージド ID を追加する

このセクションでは、既に存在する Azure Digital Twins インスタンスにシステム マネージド ID を追加します。 以下のタブを使用して、好みのエクスペリエンスの手順を選択してください。

# <a name="portal"></a>[ポータル](#tab/portal) 

まずブラウザーで [Azure portal](https://portal.azure.com) を開きます。

1. ポータルの検索バーでお使いのインスタンスの名前を検索し、それを選択して詳細を表示します。

1. 左側のメニューで **[Identity]\(ID\)** を選択します。

1. このページで、 **[オン]** オプションを選択してこの機能を有効にします。

1. **[保存]** ボタンを選択し、 **[はい]** を選択して確定します。

    :::image type="content" source="media/how-to-route-with-managed-identity/identity-digital-twins.png" alt-text="Azure portal のスクリーンショット。Azure Digital Twins のインスタンスの Identity (ID) ページが表示されている。":::

変更が保存されると、新しい ID の **[オブジェクト ID]** と **[アクセス許可]** のフィールドがこのページに表示されます。

必要に応じてここから **オブジェクト ID** をコピーし、ID に割り当てられている Azure ロールを表示するには **[アクセス許可]** ボタンを使用します。 ロールを設定するには、次のセクションに進みます。

# <a name="cli"></a>[CLI](#tab/cli)

ここでも、`az dt create` コマンドと `--assign-identity` パラメーターを使用して、インスタンスに ID を追加できます。 作成するインスタンスの新しい名前を指定する代わりに、既に存在するインスタンスの名前を指定して、そのインスタンスの `--assign-identity` の値を更新できます。

マネージド ID を **有効にする** コマンドは、システム マネージド ID を持つインスタンスを作成するコマンドと同じです。 変更されるのは、インスタンス名パラメーターの値だけです。

```azurecli-interactive
az dt create --dt-name <name-of-existing-instance> --resource-group <resource-group> --assign-identity
```

マネージド ID が現在有効になっているインスタンスでそれを **無効にする** には、次のようなコマンドを使用して `--assign-identity` を `false` に設定します。

```azurecli-interactive
az dt create --dt-name <name-of-existing-instance> --resource-group <resource-group> --assign-identity false
```

---

## <a name="assign-azure-roles-to-the-identity"></a>Azure ロールを ID に割り当てる 

Azure Digital Twins インスタンス用のシステム割り当て ID が作成されたら、サポートされている送信先にイベントを転送するためにさまざまな種類の[エンドポイント](concepts-route-events.md)で認証を行うため、それに適切なロールを割り当てる必要があります。 このセクションでは、ロールのオプションと、それらをシステム割り当て ID に割り当てる方法について説明します。

>[!NOTE]
> これは重要なステップです。行わないと、ID はエンドポイントにアクセスできず、イベントは配信されません。

### <a name="supported-destinations-and-azure-roles"></a>サポートされている配信先と Azure ロール 

送信先の種類に応じて、ID がエンドポイントにアクセスするために必要な最小限のロールを次に示します。 さらに高いアクセス許可を持つロール (データ所有者ロールなどの) も機能します。

| 到着地 | Azure ロール |
| --- | --- |
| Azure Event Hubs | Azure Event Hubs データ送信者 |
| Azure Service Bus | Azure Service Bus データ送信者 |
| [Azure ストレージ コンテナー] | ストレージ BLOB データ共同作成者 |

Azure Digital Twins でのルーティングでサポートされているエンドポイント、ルート、送信先の種類の詳細については、[イベント ルート](concepts-route-events.md)に関するページを参照してください。

### <a name="assign-the-role"></a>ロールを割り当てる

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

以下のタブを使用して、好みのエクスペリエンスの手順を選択してください。

# <a name="portal"></a>[ポータル](#tab/portal) 

ID にロールを割り当てるには、まずブラウザーで [Azure portal](https://portal.azure.com) を開きます。

1. ポータルの検索バーで名前を検索して、お使いのエンドポイント リソース (イベント ハブ、Service Bus トピック、またはストレージ コンテナー) に移動します。 

1. **[アクセス制御 (IAM)]** を選択します。

1. **[追加]**  >  **[ロールの割り当ての追加]** を選択して、[ロールの割り当ての追加] ページを開きます。

1. 以下の情報を使用して、目的のロールを Azure Digital Twins インスタンスのマネージド ID に割り当てます。 詳細な手順については、「[Azure portal を使用して Azure ロールを割り当てる](../role-based-access-control/role-assignments-portal.md)」を参照してください。
    
    | 設定 | 値 |
    | --- | --- |
    | Role | ドロップダウン メニューから目的のロールを選択します。 |
    | アクセスの割り当て先 | **[システム割り当てマネージド ID]** で、 **[Digital Twins]** を選択します。 |
    | メンバー | ロールを割り当てる Azure Digital Twins インスタンスのマネージド ID を選択します。 マネージ ID の名前はインスタンスの名前と一致するため、Azure Digital Twins インスタンスの名前を選択します。 |
    
    ![[ロールの割り当ての追加] ページ](../../includes/role-based-access-control/media/add-role-assignment-page.png)

# <a name="cli"></a>[CLI](#tab/cli)

指定したロールを持つ 1 つ以上のスコープに ID を割り当てるために、`--scopes` パラメーターを `az dt create` コマンドに追加できます。 このパラメーターを付加したコマンドは、最初にインスタンスを作成するときに使用することもできますし、既存のインスタンスの名前を渡すことで後から使用することもできます。

次の例では、システム マネージド ID を持つインスタンスを作成し、その ID にイベント ハブの `MyCustomRole` という名前のカスタム ロールを割り当てています。

```azurecli-interactive
az dt create --dt-name <instance-name> --resource-group <resource-group> --assign-identity --scopes "/subscriptions/<subscription ID>/resourceGroups/<resource-group>/providers/Microsoft.EventHub/namespaces/<Event-Hubs-namespace>/eventhubs/<event-hub-name>" --role MyCustomRole
```

このコマンドを使用したロールの割り当てのその他の例については、[az dt create のリファレンス ドキュメント](/cli/azure/dt#az_dt_create)を参照してください。

また、ロールを作成および管理するために、[az role assignment](/cli/azure/role/assignment?view=azure-cli-latest&preserve-view=true) コマンド グループも使用できます。 このコマンドは、create コマンドでロールの割り当てをグループ化したくない場合の別のシナリオをサポートするために使用できます。

---

## <a name="create-an-endpoint-with-identity-based-authentication"></a>ID ベースの認証を使用してエンドポイントを作成する

Azure Digital Twins インスタンスのシステム マネージド ID を設定し、それに適切なロールを割り当てた後は、認証にその ID を使用できる Azure Digital Twins [エンドポイント](how-to-manage-routes.md#create-an-endpoint-for-azure-digital-twins)を作成できます。 このオプションは、種類が Event Hubs と Service Bus のエンドポイントでのみ使用できます (Event Grid ではサポートされていません)。

>[!NOTE]
> キー ベースの ID で既に作成されているエンドポイントを編集して、ID ベースの認証に変更することはできません。 最初にエンドポイントを作成するときに、認証の種類を選択する必要があります。

以下のタブを使用して、好みのエクスペリエンスの手順を選択してください。

# <a name="portal"></a>[ポータル](#tab/portal) 

まず、こちらの [Azure Digital Twins のエンドポイントを作成する手順](how-to-manage-routes.md#create-an-endpoint-for-azure-digital-twins)に従います。

エンドポイントの種類に必要な詳細を設定するステップになったら、[認証の種類] で **[Identity-based]\(ID ベース\)** を必ず選択します。

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes/create-endpoint-event-hub-authentication.png" alt-text="種類が Event Hub のエンドポイントを作成するスクリーンショット。" lightbox="media/how-to-manage-routes/create-endpoint-event-hub-authentication.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

エンドポイントの設定を終了し、 **[保存]** を選択します。

# <a name="cli"></a>[CLI](#tab/cli)

CLI でのエンドポイントの作成は、エンドポイントの作成に使用する `az dt endpoint create` コマンドに `--auth-type` パラメーターを追加することで実行できます。 (このコマンドの詳細については、その[リファレンス ドキュメント](/cli/azure/dt/endpoint/create?view=azure-cli-latest&preserve-view=true)または [Azure Digital Twins エンドポイントを設定するための一般的な手順](how-to-manage-routes.md#create-the-endpoint)に関する記事を参照してください)。

ID ベースの認証を使用するエンドポイントを作成するには、`--auth-type` パラメーターを使用して `IdentityBased` 認証の種類を指定します。 下の例は、Event Hubs エンドポイントに対するこの機能を示しています。

```azurecli-interactive
az dt endpoint create eventhub --endpoint-name <endpoint-name> --eventhub-resource-group <eventhub-resource-group> --eventhub-namespace <eventhub-namespace> --eventhub <eventhub-name> --auth-type IdentityBased --dt-name <instance-name>
```

---

## <a name="considerations-for-disabling-system-managed-identities"></a>システム マネージド ID を無効にする場合の考慮事項

ID は、それを使用するエンドポイントとは別に管理されているため、ID またはそのロールを変更したときの Azure Digital Twins インスタンスのエンドポイントに対する影響について考慮することが重要です。 ID を無効にした場合、またはエンドポイントに必要なロールを削除した場合、エンドポイントにアクセスできなくなり、イベントのフローが中断される可能性があります。

設定されていたマネージド ID を無効にした後もエンドポイントを引き続き使用するには、エンドポイントを削除し、異なる認証の種類で[再作成する](how-to-manage-routes.md#create-an-endpoint-for-azure-digital-twins)必要があります。 この変更を行った後、エンドポイントへのイベントの配信が再開されるまで、最大で 1 時間かかることがあります。

## <a name="next-steps"></a>次のステップ

Azure AD でのマネージド ID の詳細について確認します。 
* [Azure リソースのマネージド ID](../active-directory/managed-identities-azure-resources/overview.md)