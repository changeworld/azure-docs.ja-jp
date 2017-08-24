---
title: "Azure マネージ アプリケーションの使用 | Microsoft Docs"
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
ms.translationtype: HT
ms.sourcegitcommit: 398efef3efd6b47c76967563251613381ee547e9
ms.openlocfilehash: e1ac025c2769c6d7cffde35c70e049c29a4a006a
ms.contentlocale: ja-jp
ms.lasthandoff: 08/11/2017

---
# <a name="consume-a-service-catalog-managed-application"></a>サービス カタログ マネージ アプリケーションの使用

Azure マネージ アプリケーションには、その利用形態を全体として見た場合に 2 つのシナリオが存在します。 1 つは、顧客向けにマネージ アプリケーションを作成する発行元 (ISV) のシナリオです。 もう 1 つは、顧客 (そのマネージ アプリケーションを利用するコンシューマー) のシナリオです。 この記事では、2 つ目のシナリオについて取り上げると共に、ISV から提供されたマネージ アプリケーションを顧客が実行する方法について説明します。 詳細については、[マネージ アプリケーションの概要](managed-application-overview.md)に関するページをご覧ください。

マネージ アプリケーションの実行には現在、Azure CLI または Azure Portal を使うことができます。 

## <a name="create-the-managed-application-by-using-azure-cli"></a>Azure CLI を使ったマネージ アプリケーションの作成 

使用するアプライアンスのアプライアンス定義 ID を取得する必要があります。

Azure CLI を使ってマネージ アプリケーションを作成する方法は 2 つあります。

* 通常のテンプレート デプロイ コマンドを使用する。 
* この目的で用意された新しい作成コマンドを使用する。

### <a name="use-the-template-deployment-command"></a>テンプレート デプロイ コマンドを使用する

ベンダーによって作成された applianceMainTemplate.json ファイルをデプロイします。

次に、リソース グループを 2 つ作成します。 1 つ目は、アプライアンス リソース (Microsoft.Solutions/appliances) の作成先となるリソース グループです。 2 つ目は、mainTemplate.json で定義されているすべてのリソースを含むことになるリソース グループです。 このリソース グループは ISV が管理します。

```azurecli
az group create --name mainResourceGroup --location westcentralus    
az group create --name managedResourceGroup --location westcentralus
```

> [!NOTE]
> リソース グループの場所には `westcentralus` を使用します。
>


applianceMainTemplate.json を mainResourceGroup にデプロイするには、次のコマンドを使用します。

```azurecli
az group deployment create --name managedAppDeployment --resourceGroup mainResourceGroup --templateUri  
```

前述のテンプレートの実行後、テンプレートに定義されているパラメーターの値を入力するよう求められます。 テンプレート内のリソースをプロビジョニングするうえで必要なパラメーターのほか、次に示す 2 つの重要なパラメーターの値が必要です。

- **managedResourceGroupId**: applianceMainTemplate.json に定義されているリソースの作成先となるリソース グループの ID。 この ID は `/subscriptions/{subscriptionId}/resourceGroups/{resoureGroupName}` という形式です。 先ほどの例では、`managedResourceGroup` の ID が該当します。
- **applianceDefinitionId**: マネージ アプリケーション定義リソースの ID。 この値は、ISV から提供されます。 

> [!NOTE] 
> ISV は、アプライアンス定義リソースの作成先となるリソース グループへのアクセスを許可する必要があります。 アプライアンス定義リソースは、ISV のサブスクリプションに作成されます。 したがって顧客のテナント内のユーザー、ユーザー グループ、アプリケーションには、このリソースへの読み取りアクセス権が必要となります。 

デプロイが正常に完了すると、アプライアンス リソースが mainResourceGroup に作成されていることを確認できます。 storageAccount リソースは、managedResourceGroup に作成されます。

### <a name="use-the-create-command"></a>作成コマンドを使用する

マネージ アプリケーションは、`az managedapp create` コマンドを使ってマネージ アプリケーション定義から作成することができます。 

```azurecli
az managedapp create --name ravtestappliance401 --location "westcentralus" 
    --kind "Servicecatalog" --resource-group "ravApplianceCustRG401" 
    --managedapp-definition-id "/subscriptions/{guid}/resourceGroups/ravApplianceDefRG401/providers/Microsoft.Solutions/applianceDefinitions/ravtestAppDef401" 
    --managed-rg-id "/subscriptions/{guid}/resourceGroups/ravApplianceCustManagedRG401" 
    --parameters "{\"storageAccountName\": {\"value\": \"ravappliancedemostore1\"}}" 
    --debug
```

* **appliance-definition-Id**: 前の手順で作成したアプライアンス定義のリソース ID。 この ID を取得するには、次のコマンドを実行します。

  ```azurecli
  az appliance definition show -n ravtestAppDef1 -g ravApplianceRG2
  ```

  このコマンドを実行するとアプライアンス定義が返されます。 必要になるのは、ID プロパティの値です。

* **managed-rg-id**: applianceMainTemplate.json に定義されているすべてのリソースの作成先となるリソース グループの名前。 このリソース グループはマネージ リソース グループであり、 発行元が管理します。 存在しない場合は、自動的に作成されます。
* **resource-group**: アプライアンス リソースの作成先となるリソース グループ。 Microsoft.Solutions/appliance リソースは、このリソース グループに存在します。 
* **parameters**: applianceMainTemplate.json に定義されているリソースに必要なパラメーター。

## <a name="create-the-managed-application-by-using-the-portal"></a>ポータルを使ったマネージ アプリケーションの作成

ISV によって発行されたマネージ アプリケーションは、ポータルから使用することもできます。 次の手順に従います。

1. Azure Portal にアクセスします。 **[作成]** ブレードで、**[Service Catalog Managed Application]\(サービス カタログ マネージ アプリケーション\)** を選択します。  

    ![サービス カタログ マネージ アプリケーション](./media/managed-application-consumption/create-service-catalog-managed-application.png)

2. さまざまな ISV/パートナーのプランの一覧から、作成するマネージ アプリケーションを選択します。 **[作成]**を選択します。

    ![マネージ アプリケーションの選択](./media/managed-application-consumption/select-offer.png)

3. 表示されたブレードに、リソースのプロビジョニングに必要なパラメーターを入力します。 

    ![マネージ アプリケーションのパラメーター](./media/managed-application-consumption/input-parameters.png)

4. 値を指定した後、**[OK]** を選択します。 テンプレートによって、入力内容が検証されます。 検証にパスした場合は、テンプレートのデプロイが開始されます。 デプロイが完了すると、テンプレートに定義されている適切なリソースが、指定したマネージ リソース グループにプロビジョニングされます。

## <a name="known-issues"></a>既知の問題

このプレビュー リリースには、次の問題が確認されています。

* アプライアンスの作成中、500 内部サーバー エラーが表示されます。 これは、多くの場合、断続的な問題です。 操作をやり直してください。
* マネージ リソース グループには新しいリソース グループを使う必要があります。 既存のリソース グループを使うと、デプロイに失敗します。
* Microsoft.Solutions/appliances リソースを含むリソース グループは、**westcentralus** に作成する必要があります。

## <a name="next-steps"></a>次のステップ

* マネージ アプリケーションの概要については、[マネージ アプリケーションの概要](managed-application-overview.md)に関するページをご覧ください。
* サービス カタログ マネージ アプリケーションの発行については、「[サービス カタログ マネージ アプリケーションの作成と発行](managed-application-publishing.md)」を参照してください。
* マネージ アプリケーションを Azure Marketplace に発行する方法については、「[Marketplace の Azure マネージ アプリケーション](managed-application-author-marketplace.md)」を参照してください。
* Marketplace からマネージ アプリケーションを使用する方法については、「[Consume Azure managed applications in the Marketplace (Marketplace での Azure マネージ アプリケーションの使用)](managed-application-consume-marketplace.md)」を参照してください。

