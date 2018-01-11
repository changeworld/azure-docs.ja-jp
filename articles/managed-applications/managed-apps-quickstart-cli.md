---
title: "Azure CLI を使用した Azure マネージ アプリケーションの作成 | Microsoft Docs"
description: "組織のメンバーを対象とする Azure マネージ アプリケーションを作成する方法について説明します。"
services: azure-resource-manager
author: tfitzmac
manager: timlt
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.date: 12/15/2017
ms.author: tomfitz
ms.openlocfilehash: 35059603096279f7d58da1c1b40dd2ab3f1b5c38
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/18/2017
---
# <a name="create-and-deploy-an-azure-managed-application-with-azure-cli"></a>Azure CLI を使用した Azure マネージ アプリケーションの作成とデプロイ

この記事では、マネージ アプリケーションの操作の概要を説明します。 組織のユーザー向けの内部カタログにマネージ アプリケーション定義を追加します。 次に、そのマネージ アプリケーションをサブスクリプションにデプロイします。 導入を簡素化するために、マネージ アプリケーション用のファイルが既に作成されています。 1 つ目のファイルには、ソリューションのインフラストラクチャが定義されます。 2 つ目のファイルには、ポータルを使用してマネージ アプリケーションをデプロイするためのユーザー インターフェイスが定義されます。 これらのファイルは GitHub で入手できます。 これらのファイルを作成する方法については、[サービス カタログ アプリケーションの作成](publish-service-catalog-app.md)に関するチュートリアルをご覧ください。

作業が完了すると、マネージ アプリケーションのさまざまな部分を含む 3 つのリソース グループが作成されます。

| リソース グループ | 指定値を含む | 説明 |
| -------------- | -------- | ----------- |
| appDefinitionGroup | マネージ アプリケーション定義。 | 発行元が、このリソース グループとマネージ アプリケーション定義を作成します。 マネージ アプリケーション定義へのアクセス権を持つすべてのユーザーがデプロイできます。 |
| applicationGroup | マネージ アプリケーション インスタンス。 | コンシューマーが、このリソース グループとマネージ アプリケーション インスタンスを作成します。 コンシューマーは、このインスタンスを使用してマネージ アプリケーションを更新できます。 |
| infrastructureGroup | マネージ アプリケーションのリソース。 | マネージ アプリケーションを作成すると、このリソース グループが自動的に作成されます。 発行元は、アプリケーションを管理するためにこのリソース グループにアクセスできます。 コンシューマーは、このリソース グループへのアクセスが制限されます。 |

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group-for-definition"></a>定義用のリソース グループの作成

マネージ アプリケーション定義はリソース グループに存在します。 リソース グループは、Azure リソースをまとめてデプロイして管理するための論理上のコレクションです。

リソース グループを作成するには、次のコマンドを使用します。

```azurecli-interactive
az group create --name appDefinitionGroup --location westcentralus
```

## <a name="create-the-managed-application-definition"></a>マネージ アプリケーション定義の作成

マネージ アプリケーションを定義するときは、コンシューマーに代わってリソースを管理するユーザー、グループ、またはアプリケーションを選択します。 この ID には、割り当てられているロールに従って、マネージ リソース グループに対するアクセス許可が付与されています。 通常は、リソースを管理する Azure Active Directory グループを作成します。 ただし、この記事では独自の ID を使用します。

ID のオブジェクト ID を取得するには、次のコマンドでユーザー プリンシパル名を指定します。

```azurecli-interactive
userid=$(az ad user show --upn-or-object-id example@contoso.org --query objectId --output tsv)
```

次に、ユーザーにアクセス権を付与する RBAC の組み込みロールのロール定義 ID が必要となります。 次のコマンドは、所有者ロールのロール定義 ID を取得する方法を示しています。

```azurecli-interactive
roleid=$(az role definition list --name Owner --query [].name --output tsv)
```

次に、マネージ アプリケーション定義のリソースを作成します。 マネージ アプリケーションには、ストレージ アカウントだけが含まれます。

```azurecli-interactive
az managedapp definition create \
  --name "ManagedStorage" \
  --location "westcentralus" \
  --resource-group appDefinitionGroup \
  --lock-level ReadOnly \
  --display-name "Managed Storage Account" \
  --description "Managed Azure Storage Account" \
  --authorizations "$userid:$roleid" \
  --package-file-uri "https://raw.githubusercontent.com/Azure/azure-managedapp-samples/master/samples/201-managed-storage-account/managedstorage.zip"
```

コマンドが完了すると、リソース グループにマネージ アプリケーション定義が作成されます。 

前の例で使用されているパラメーターは次のとおりです。

* **resource-group**: マネージ アプリケーション定義が作成されるリソース グループの名前。
* **lock-level**: マネージ リソース グループに対して設定されるロックの種類。 これによって、このリソース グループに対して問題となるような操作を顧客が実行できないようにします。 現在サポートされているロック レベルは ReadOnly だけです。 ReadOnly が指定されている場合、マネージ リソース グループに存在するリソースの読み取りしか顧客は実行できません。
* **authorizations**: マネージ リソース グループへのアクセス許可を付与する際に使うプリンシパル ID とロール定義 ID を記述します。 `<principalId>:<roleDefinitionId>` の形式で指定します。 このプロパティには複数の値を指定することができます。 複数の値が必要である場合は、`<principalId1>:<roleDefinitionId1> <principalId2>:<roleDefinitionId2>` という形式で指定してください。 このとき値は、スペースで区切って指定します。
* **package-file-uri**: 必要なファイルが含まれた .zip パッケージの場所。 このパッケージには、少なくとも **mainTemplate.json** ファイルと **createUiDefinition.json** ファイルが含まれています。 **mainTemplate.json** には、マネージ アプリケーションの一部としてプロビジョニングされる Azure リソースが定義されます。 テンプレートは、通常の Resource Manager テンプレートと違いはありません。 **createUiDefinition.json** では、ポータルを使用してマネージ アプリケーションを作成するユーザー向けのユーザー インターフェイスを生成します。

## <a name="create-resource-group-for-managed-application"></a>マネージ アプリケーション用のリソース グループの作成

これで、マネージ アプリケーションをデプロイする準備が整いました。 

デプロイされたマネージ アプリケーションを含めるために、リソース グループが必要です。 場所として **westcentralus** を使用します。

```azurecli-interactive
az group create --name applicationGroup --location westcentralus
```

## <a name="deploy-the-managed-application"></a>マネージ アプリケーションのデプロイ

次のコマンドを使用して、アプリケーションをデプロイします。

```azurecli-interactive
appid=$(az managedapp definition show --name ManagedStorage --resource-group appDefinitionGroup --query id --output tsv)
subid=$(az account show --query id --output tsv)
managedGroupId=/subscriptions/$subid/resourceGroups/infrastructureGroup

az managedapp create \
  --name storageApp \
  --location "westcentralus" \
  --kind "Servicecatalog" \
  --resource-group applicationGroup \
  --managedapp-definition-id $appid \
  --managed-rg-id $managedGroupId \
  --parameters "{\"storageAccountNamePrefix\": {\"value\": \"storage\"}, \"storageAccountType\": {\"value\": \"Standard_LRS\"}}"
```

前の例で使用されているパラメーターは次のとおりです。

* **managedapp-definition-id**: この記事で以前に作成した定義の ID。
* **managed-rg-id**: マネージ アプリケーションに関連付けられているリソースのリソース グループの ID。 このリソース グループはコマンドによって作成されます。 **コマンドを実行する前に存在していない必要があります**。 このリソース グループは発行元が管理します。 
* **resource-group**: マネージ アプリケーション リソースの作成先となるリソース グループ。
* **parameters**: マネージ アプリケーションに関連付けられているリソースに必要なパラメーター。

デプロイが正常に完了すると、applicationGroup にマネージ アプリケーションが作成されます。 storageAccount リソースは、infrastructureGroup に作成されます。

## <a name="next-steps"></a>次のステップ

* マネージ アプリケーションの概要については、[マネージ アプリケーションの概要](overview.md)に関するページをご覧ください。
* サンプル ファイルについては、[マネージ アプリケーションのサンプル](https://github.com/Azure/azure-managedapp-samples/tree/master/samples)に関するページを参照してください。
* マネージ アプリケーションの UI 定義ファイルの作成する方法については、「[CreateUiDefinition の基本概念](create-uidefinition-overview.md)」を参照してください。
