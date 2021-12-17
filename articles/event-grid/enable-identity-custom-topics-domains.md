---
title: Azure Event Grid のカスタム トピックとドメインでマネージド ID を有効にする
description: この記事では、Azure Event Grid のカスタム トピックまたはドメインに対して、マネージド サービス ID を有効にする方法を説明します。
ms.topic: how-to
ms.date: 11/09/2021
ms.openlocfilehash: 1f9cae4c25c37d0fcb670804ae3450871dbd5259
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132156593"
---
# <a name="assign-a-managed-identity-to-an-event-grid-custom-topic-or-domain"></a>マネージド ID を Event Grid のカスタム トピックまたはドメインに割り当てる 
この記事では、システム割り当てまたはユーザー割り当ての[マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) を Event Grid のカスタム トピックまたはドメインに割り当てる方法について説明します。 

## <a name="enable-identity-when-creating-a-topic-or-domain"></a>トピックまたはドメインの作成時に ID を有効にする

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)
**Azure portal** では、トピックまたはドメインを作成するときに、システム割り当て ID または 2 つのユーザーによって割り当てられた ID のいずれかを割り当てることができますが、両方の種類の ID を割り当てることはできません。 トピックまたはドメインが作成されたら、「[既存のトピックまたはドメインに対して ID を有効にする](#enable-identity-for-an-existing-custom-topic-or-domain)」セクションの手順に従って、両方の種類の ID を割り当てることができます。

### <a name="enable-system-assigned-identity"></a>システム割り当て ID を有効にする
トピックまたはドメインの作成ウィザードの **[詳細]** タブで、 **[Enable system assigned identity]\(システム割り当て ID を有効にする\)** を選択します。 

:::image type="content" source="./media/managed-service-identity/create-topic-identity.png" alt-text="[Enable system assigned identity]\(システム割り当て ID を有効にする\) オプションが選択されている画像。":::

### <a name="enable-user-assigned-identity"></a>ユーザー割り当て ID を有効にする
1. トピックまたはドメインの作成ウィザードの **[詳細]** ページで、 **[Enable user-assigned identity]\(ユーザー割り当て ID を有効にする\)** を選択し、 **[Add user assigned identity]\(ユーザー割り当て ID を追加する\)** を選択します。 

    :::image type="content" source="./media/managed-service-identity/create-page-add-user-assigned-identity-link.png" alt-text="[Enable user assigned identity]\(ユーザー割り当て ID を有効にする\) オプションが選択されている画像。":::
1. **[Select user assigned identity]\(ユーザー割り当て ID の選択\)** ウィンドウで、ユーザー割り当て ID を持つサブスクリプションを選択し、**ユーザー割り当て ID** を選択し、 **[選択]** をクリックします。 

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)
また、Azure CLI を使用して、システム割り当て ID を持つカスタム トピックまたはドメインを作成することもできます。 現時点では、Azure CLI は、ユーザー割り当て ID のトピックまたはドメインへの割り当てをサポートしていません。  

`--identity` パラメーターを `systemassigned` に設定して、`az eventgrid topic create` コマンドを使用します。 このパラメーターに値を指定しない場合、既定値 `noidentity` が使用されます。 

```azurecli-interactive
# create a custom topic with a system-assigned identity
az eventgrid topic create -g <RESOURCE GROUP NAME> --name <TOPIC NAME> -l <LOCATION>  --identity systemassigned
```

同様に、`az eventgrid domain create` コマンドを使用してシステム割り当て ID を持つドメインを作成することができます。

---

## <a name="enable-identity-for-an-existing-custom-topic-or-domain"></a>既存のカスタム トピックまたはドメインに対して ID を有効にする
このセクションでは、既存のカスタム トピックまたはドメインに対して、システム割り当て ID またはユーザー割り当て ID を有効にする方法について説明します。 

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)
Azure portal を使用する場合、既存のトピックまたはドメインに、1 つのシステム割り当て ID と最大 2 つのユーザー割り当て ID を割り当てることができます。

次の手順では、カスタム トピックの ID を有効にする方法を示します。 ドメインに対して ID を有効にする手順と似ています。 

1. [Azure ポータル](https://portal.azure.com)にアクセスします。
2. 上部の検索バーで「**event grid topics (イベント グリッド トピック)** 」を検索します。
3. マネージド ID を有効にする **カスタム トピック** を選択します。 
4. 左側のメニューの **[ID]** を選択します。

### <a name="to-assign-a-system-assigned-identity-to-a-topic"></a>システム割り当て ID をトピックに割り当てるには
1. **[システム割り当て済み]** タブで、スイッチを **オン** にして ID を有効にします。 
1. ツール バーの **[保存]** を選択して設定を保存します。 

    :::image type="content" source="./media/managed-service-identity/identity-existing-topic.png" alt-text="カスタム トピックの ID ページ"::: 

### <a name="to-assign-a-user-assigned-identity-to-a-topic"></a>ユーザー割り当て ID をトピックに割り当てるには
1. 「[ユーザー割り当てマネージド ID の管理](../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md)」の記事の手順に従って、ユーザー割り当て ID を作成します。 
1. **[ID]** ページで、右ペインの **[ユーザー割り当て済み]** タブに切り替え、ツール バーの **[+ 追加]** を選択します。

    :::image type="content" source="./media/managed-service-identity/user-assigned-identity-add-button.png" alt-text="[ユーザー割り当て ID] タブを示す画像":::     
1. **[ユーザー マネージド ID の追加]** ウィンドウで、次の手順を実行します。
    1. ユーザー割り当て ID を持つ **Azure サブスクリプション** を選択します。 
    1. **ユーザー割り当て ID** を選択します。 
    1. **[追加]** を選択します。 
1. **[ユーザー割り当て済み]** タブの一覧を更新し、追加されたユーザー割り当て ID を確認します。

同様の手順により、イベント グリッド ドメインに対して ID を有効にできます。

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)
Azure CLI を使用して、システム割り当て ID を既存のカスタム トピックまたはドメインに割り当てることもできます。 現時点では、Azure CLI は、ユーザー割り当て ID のトピックまたはドメインへの割り当てをサポートしていません。

`--identity` に `systemassigned` を設定して `az eventgrid topic update` コマンドを使用し、既存のカスタム トピックに対してシステム割り当て ID を有効にします。 ID を無効にする場合は、値として `noidentity` を指定します。 

```azurecli-interactive
# Update the topic to assign a system-assigned identity. 
az eventgrid topic update -g $rg --name $topicname --identity systemassigned --sku basic 
```

既存のドメインを更新するコマンドも同様です (`az eventgrid domain update`)。

---

## <a name="next-steps"></a>次の手順
配信先 (Service Bus キューなど) の適切なロール (Service Bus データ送信者など) に ID を追加する。 詳細な手順については、「[マネージド ID に Event Grid の配信先へのアクセスを許可する](add-identity-roles.md)」を参照してください。 
