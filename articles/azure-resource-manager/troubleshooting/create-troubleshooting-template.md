---
title: トラブルシューティング用テンプレートの作成
description: Azure Resource Manager テンプレート (ARM テンプレート) または Bicep ファイルを使用してデプロイされた Azure リソースのトラブルシューティングを行うためにテンプレートを作成する方法について説明します。
tags: top-support-issue
ms.topic: troubleshooting
ms.date: 11/02/2021
ms.openlocfilehash: 0c9b1a6ebd35a6ebe58b568a1a56e44c0395b630
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131477849"
---
# <a name="create-a-troubleshooting-template"></a>トラブルシューティング用テンプレートの作成

テンプレートのトラブルシューティングを行うための最善の方法は、テンプレートの特定の部分を分離してテストすることである場合があります。 エラーの原因と思われるリソースに焦点を当てたトラブルシューティング テンプレートを作成できます。

たとえば、デプロイ テンプレートで既存のリソースを参照している場合にエラーが発生します。 デプロイ テンプレート全体を評価するのではなく、リソースに関するデータを返すトラブルシューティング テンプレートを作成します。 この出力により、正しいパラメーターを渡しているかどうか、テンプレート関数を正しく使用しているかどうか、求めているリソースを取得しているかどうかを確認できます。

## <a name="deploy-a-troubleshooting-template"></a>トラブルシューティング用テンプレートのデプロイ

次の ARM テンプレートと Bicep ファイルでは、既存のストレージ アカウントから情報を取得しています。 Azure PowerShell の [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) または Azure CLI の [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create) を使用してデプロイを実行します。 ストレージ アカウントの名前とリソース グループを指定します。 出力は、ストレージ アカウントのプロパティ名と値を持つオブジェクトです。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageName": {
      "type": "string"
    },
    "storageResourceGroup": {
      "type": "string"
    }
  },
  "variables": {},
  "resources": [],
  "outputs": {
    "exampleOutput": {
      "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageName')), '2021-04-01')]",
      "type": "object"
    }
  }
}
```

Bicep で、`existing` キーワードを使用して、ストレージ アカウントが存在するリソース グループからデプロイを実行します。 別のリソース グループ内のリソースにアクセスするには、`scope` を使用します。 詳細については、「[既存のリソース](../bicep/resource-declaration.md#existing-resources)」を参照してください。

```bicep
param storageName string

resource stg 'Microsoft.Storage/storageAccounts@2021-04-01' existing = {
  name: storageName
}

output exampleOutput object = stg.properties
```

## <a name="alternate-troubleshooting-method"></a>代替のトラブルシューティング方法

依存関係が正しくないことが原因でデプロイ エラーが発生すると思われる場合は、テンプレートを簡略化されたテンプレートに分割してテストを実行できます。 最初に、1 つのリソース (SQL Server など) のみをデプロイするテンプレートを作成します。 リソースのデプロイが正しいと確信している場合は、それに依存するリソースを追加します (SQL Database など)。 これら 2 つのリソースを正しく定義したら、他の依存リソース (監査ポリシーなど) を追加します。 各テスト デプロイの間に、リソース グループを削除して、依存関係を適切にテストします。

## <a name="next-steps"></a>次の手順

- [一般的なデプロイのエラー](common-deployment-errors.md)
- [エラー コードの検索](find-error-code.md)
- [デバッグ ログの有効化](enable-debug-logging.md)
