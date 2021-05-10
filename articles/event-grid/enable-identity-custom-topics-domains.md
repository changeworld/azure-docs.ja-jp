---
title: Azure Event Grid のカスタム トピックとドメインでマネージド ID を有効にする
description: この記事では、Azure Event Grid のカスタム トピックまたはドメインに対して、マネージド サービス ID を有効にする方法を説明します。
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: b93fd44282d9e19e7111dd52c73d8d4c01c67a10
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278220"
---
# <a name="assign-a-system-managed-identity-to-an-event-grid-custom-topic-or-domain"></a>システム マネージド ID を Event Grid のカスタム トピックまたはドメインに割り当てる 
この記事では、Event Grid のカスタム トピックまたはドメインでシステム マネージド ID を有効にする方法を説明します。 マネージド ID については、[Azure リソースのマネージド ID の概要](../active-directory/managed-identities-azure-resources/overview.md)に関するページを参照してください。

## <a name="enable-identity-at-the-time-of-creation"></a>作成時に ID を有効にする

### <a name="using-azure-portal"></a>Azure Portal の使用
Azure portal でカスタム トピックやドメインを作成する際、それに対してシステム割り当て ID を有効にすることができます。 次の図は、カスタム トピックに対してシステム マネージド ID を有効にする方法を示しています。 基本的には、トピック作成ウィザードの **[詳細設定]** ページで、 **[Enable system assigned identity]\(システム割り当て ID を有効にする\)** オプションを選択します。 このオプションは、ドメイン作成ウィザードの **[詳細設定]** ページにもあります。 

![カスタム トピックを作成する際に ID を有効にする](./media/managed-service-identity/create-topic-identity.png)

### <a name="using-azure-cli"></a>Azure CLI の使用
また、Azure CLI を使用して、システム割り当て ID を持つカスタム トピックまたはドメインを作成することもできます。 `--identity` パラメーターを `systemassigned` に設定して、`az eventgrid topic create` コマンドを使用します。 このパラメーターに値を指定しない場合、既定値 `noidentity` が使用されます。 

```azurecli-interactive
# create a custom topic with a system-assigned identity
az eventgrid topic create -g <RESOURCE GROUP NAME> --name <TOPIC NAME> -l <LOCATION>  --identity systemassigned
```

同様に、`az eventgrid domain create` コマンドを使用してシステム マネージド ID を持つドメインを作成することができます。

## <a name="enable-identity-for-an-existing-custom-topic-or-domain"></a>既存のカスタム トピックまたはドメインに対して ID を有効にする
このセクションでは、既存のカスタム トピックまたはドメインに対してシステム マネージド ID を有効にする方法について学びます。 

### <a name="using-azure-portal"></a>Azure Portal の使用
次の手順では、カスタム トピックに対してシステム マネージド ID を有効にする方法を示します。 ドメインに対して ID を有効にする手順と似ています。 

1. [Azure ポータル](https://portal.azure.com)にアクセスします。
2. 上部の検索バーで「**event grid topics (イベント グリッド トピック)** 」を検索します。
3. マネージド ID を有効にする **カスタム トピック** を選択します。 
4. **[ID]** タブに移動します。 
5. スイッチを **オン** にして、ID を有効にします。 
1. ツール バーの **[保存]** を選択して設定を保存します。 

    :::image type="content" source="./media/managed-service-identity/identity-existing-topic.png" alt-text="カスタム トピックの ID ページ"::: 

同様の手順により、イベント グリッド ドメインに対して ID を有効にできます。

### <a name="use-the-azure-cli"></a>Azure CLI の使用
`--identity` に `systemassigned` を設定して `az eventgrid topic update` コマンドを使用し、既存のカスタム トピックに対してシステム割り当て ID を有効にします。 ID を無効にする場合は、値として `noidentity` を指定します。 

```azurecli-interactive
# Update the topic to assign a system-assigned identity. 
az eventgrid topic update -g $rg --name $topicname --identity systemassigned --sku basic 
```

既存のドメインを更新するコマンドも同様です (`az eventgrid domain update`)。


## <a name="next-steps"></a>次の手順
配信先 (Service Bus キューなど) の適切なロール (Service Bus データ送信者など) に ID を追加する。 詳細な手順については、「[マネージド ID に Event Grid の配信先へのアクセスを許可する](add-identity-roles.md)」を参照してください。 
