---
title: "Azure マネージ アプリケーションの実行 | Microsoft Docs"
description: "公開されているファイルから Azure マネージ アプリケーションを作成する方法について、顧客の視点で説明します。"
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/17/2017
ms.author: gauravbh; tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: 041254b07584a52ae92e603f60a439050b747af1
ms.contentlocale: ja-jp
ms.lasthandoff: 05/18/2017


---
# <a name="consume-an-azure-managed-application"></a>Azure マネージ アプリケーションの実行

[マネージ アプリケーションの概要](managed-application-overview.md)に関する記事で説明したように、マネージ アプリケーションには、その利用形態を全体として見た場合に 2 つのシナリオが存在します。 1 つは、顧客向けにマネージ アプリケーションを作成する発行元 (ISV) のシナリオです。 もう 1 つは、そのマネージ アプリケーションのエンド カスタマー (コンシューマー) のシナリオです。 この記事では、2 つ目のシナリオについて取り上げると共に、ISV から提供されたマネージ アプリケーションをエンド カスタマーが実行する方法について説明します。

マネージ アプリケーションの実行には現在、Azure CLI または Azure Portal を使うことができます。 

## <a name="create-the-managed-application-using-cli"></a>CLI を使ったマネージ アプリケーションの作成 

コンシューマーは、実行するアプライアンスのアプライアンス定義 ID を取得する必要があります。

Azure CLI を使ってマネージ アプリケーションを作成するには、2 とおりの方法があります。 1 つは通常のテンプレート デプロイ コマンドを使う方法で、もう 1 つはその目的に特化した新しいコマンドを使う方法です。

### <a name="create-using-template-deployment-command"></a>テンプレート デプロイ コマンドでの作成

ベンダーによって作成された applianceMainTemplate.json ファイルをデプロイします。

まず、リソース グループを 2 つ作成します。 1 つ目は、アプライアンス リソース (Microsoft.Solutions/appliances) の作成先となるリソース グループです。 2 つ目は、mainTemplate.json に定義されているすべてのリソースを含むことになるリソース グループです。 このリソース グループは ISV が管理します。

```azurecli
az group create --name mainResourceGroup --location westcentralus    
az group create --name managedResourceGroup --location westcentralus
```

> [!NOTE]
> リソース グループの場所には `westcentralus` を使用します。
>


次に、以下のコマンドを使って、mainResourceGroup に applianceMainTemplate.json をデプロイします。

```azurecli
az group deployment create --name managedAppDeployment --resourceGroup mainResourceGroup --templateUri  
```

前述のテンプレートを実行すると、テンプレートに定義されているパラメーターの値を入力するよう求められます。 テンプレート内のリソースをプロビジョニングするうえで必要なパラメーターに加え、次に示す 2 つの重要なパラメーターの値が必要となります。

- managedResourceGroupId - applianceMainTemplate.json に定義されているリソースの作成先となるリソース グループの ID。 この ID は `/subscriptions/{subscriptionId}/resourceGroups/{resoureGroupName}` という形式になっています。 先ほどの例では、`managedResourceGroup` の ID が該当します。
- applianceDefinitionId - マネージ アプリケーション定義リソースの ID。 この値は、ISV から提供されます。 

> [!NOTE] 
> ISV は、アプライアンス定義リソースの作成先となるリソース グループへのアクセスを許可する必要があります。 アプライアンス定義リソースは、ISV のサブスクリプションに作成されます。 したがって顧客のテナント内のユーザー、ユーザー グループ、アプリケーションには、このリソースへの読み取りアクセス権が必要となります。 

デプロイが正常に完了すると、アプライアンス リソースが **mainResourceGroup** に作成されていることを確認できます。 storageAccount リソースは、**managedResourceGroup** に作成されます。

### <a name="create-the-managed-application-using-create-command"></a>create コマンドを使ったマネージ アプリケーションの作成

マネージ アプリケーションは、`az managedapp create` コマンドを使ってマネージ アプリケーション定義から作成することができます。 

```azurecli
az managedapp create --name ravtestappliance401 --location "westcentralus" 
    --kind "Servicecatalog" --resource-group "ravApplianceCustRG401" 
       --managedapp-definition-id "/subscriptions/{guid}/resourceGroups/ravApplianceDefRG401/providers/Microsoft.Solutions/applianceDefinitions/ravtestAppDef401" 
       --managed-rg-id "/subscriptions/{guid}/resourceGroups/ravApplianceCustManagedRG401" 
       --parameters "{\"storageAccountName\": {\"value\": \"ravappliancedemostore1\"}}" 
       --debug
```

**appliance-definition-Id** - 前の手順で作成したアプライアンス定義のリソース ID。 この ID を取得するには、次のコマンドを実行します。

```azurecli
az appliance definition show -n ravtestAppDef1 -g ravApplianceRG2
```

このコマンドを実行するとアプライアンス定義が返されます。 必要になるのは、**Id** プロパティの値です。

**managed-rg-id** - applianceMainTemplate.json に定義されているすべてのリソースの作成先となるリソース グループの名前。 このリソース グループはマネージ リソース グループであり、発行元が管理します。 存在しない場合は、自動的に作成されます。

**resource-group** - アプライアンス リソースの作成先となるリソース グループ。 Microsoft.Solutions/appliance リソースは、このリソース グループに存在します。 

**parameters** - applianceMainTemplate.json に定義されているリソースに必要なパラメーター。

## <a name="create-the-managed-application-using-portal"></a>ポータルを使ったマネージ アプリケーションの作成

ISV によって発行されたマネージ アプリケーションは、ポータルから実行することもできます。 以下の手順に従ってください。

Azure Portal の [作成] ブレードで [サービス カタログの管理されているアプリケーション] を選択します。

![](./media/managed-application-consumption/create-service-catalog-managed-application.png)

すると、さまざまな ISV/パートナーから提供されているプランが一覧表示されます。 作成するアプリケーションを選択し、[作成] をクリックします。

![](./media/managed-application-consumption/select-offer.png)

[作成] をクリックするとブレードが表示されるので、リソースのプロビジョニングに必要なパラメーターを入力します。 

![](./media/managed-application-consumption/input-parameters.png)

必要な値を入力したら [OK] をクリックします。 入力内容に照らしてテンプレートが検証されます。 検証にパスした場合は、テンプレートのデプロイが開始されます。 デプロイが完了すると、テンプレートに定義されている適切なリソースが、指定したマネージ リソース グループにプロビジョニングされます。

## <a name="known-issues"></a>既知の問題

このプレビュー リリースには、次の問題が確認されています。

* アプライアンスの作成中に 500 内部サーバー エラーが発生した場合、断続的な問題であることが強く疑われます。 この問題に遭遇した場合は操作を再試行してください。
* マネージ リソース グループには新しいリソース グループを使う必要があります。 既存のリソース グループを使うとデプロイに失敗します。
* Microsoft.Solutions/appliances リソースを含んだリソース グループの作成場所は **westcentralus** とする必要があります。

## <a name="next-steps"></a>次のステップ

* マネージ アプリケーションの概要については、「[Azure マネージ アプリケーションの概要](managed-application-overview.md)」を参照してください。
* ベンダーから見た利用形態については、「[Azure マネージ アプリケーションの作成と発行](managed-application-publishing.md)」を参照してください。
