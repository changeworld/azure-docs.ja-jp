---
title: テンプレートを使用してゲスト構成割り当てを作成する方法
description: Azure Resource Manager から直接マシンに構成をデプロイする方法について説明します。
ms.date: 08/09/2021
ms.topic: how-to
ms.openlocfilehash: aa7938a9421a9e7680af34af475585c4dc1c818a
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2021
ms.locfileid: "122868431"
---
# <a name="how-to-create-a-guest-configuration-assignment-using-templates"></a>テンプレートを使用してゲスト構成割り当てを作成する方法

複数のマシンに[ゲスト構成パッケージを割り当てる](../concepts/guest-configuration-assignments.md)最善の方法は、[Azure Policy](./guest-configuration-create-definition.md) を使用することです。 ゲスト構成パッケージを 1 台のマシンに割り当てることもできます。

## <a name="built-in-and-custom-configurations"></a>組み込み構成とカスタム構成

ゲスト構成パッケージを 1 台のマシンに割り当てるには、次の例を変更します。 2 つのシナリオがあります。

- [公開した](./guest-configuration-create-publish.md)パッケージへのリンクを使用して、マシンにカスタム構成を適用します。
- マシンに Azure ベースラインなどの[組み込み](../samples/built-in-packages.md)構成を適用します。

## <a name="extending-other-resource-types-such-as-arc-enabled-servers"></a>Arc 対応サーバーなど、他の種類のリソースを拡張する

以下の各セクションの例には、名前が `Microsoft.Compute/virtualMachines` で始まる **type** プロパティが含まれています。 ゲスト構成リソース プロバイダー `Microsoft.GuestConfiguration` は、親の種類を参照する必要のある[拡張リソース](../../../azure-resource-manager/management/extension-resource-types.md)です。

例を [Arc 対応サーバー](../../../azure-arc/servers/overview.md)など、他のリソースの種類に変更するには、親の種類をリソース プロバイダーの名前に変更します。
Arc 対応サーバーの場合、リソース プロバイダーは `Microsoft.HybridCompute/machines` です。

次の "<>" フィールドを、お使いの環境に固有の値に置き換えます。

- **<vm_name>** : 構成が適用されるマシン リソースの名前
- **<configuration_name>** : 適用する構成の名前
- **<vm_location>** : ゲスト構成割り当てが作成される Azure リージョン
- **<Url_to_Package.zip>** : カスタム コンテンツ パッケージの場合、.zip ファイルへの HTTPS リンク
- **<SHA256_hash_of_package.zip>** : カスタム コンテンツ パッケージの場合、.zip ファイルの SHA256 ハッシュ

## <a name="assign-a-configuration-using-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを使用して構成を割り当てる

ゲスト構成割り当てリソースを含む、[Azure Resource Manager テンプレート](../../../azure-resource-manager/templates/deployment-tutorial-local-template.md?tabs=azure-powershell)をデプロイできます。

カスタム構成を割り当てる例を次に示します。

```json
{
            "apiVersion": "2020-06-25",
            "type": "Microsoft.Compute/virtualMachines/providers/guestConfigurationAssignments",
            "name": "<vm_name>/Microsoft.GuestConfiguration/<configuration_name>",
            "location": "<vm_location>",
            "dependsOn": [
                "Microsoft.Compute/virtualMachines/<vm_name>"
            ],
            "properties": {
                "guestConfiguration": {
                    "name": "<configuration_name>",
                    "contentUri": "<Url_to_Package.zip>",
                    "contentHash": "<SHA256_hash_of_package.zip>",
                    "assignmentType": "ApplyAndMonitor"
                }
            }
        }
```

`AzureWindowBaseline` 組み込み構成を割り当てる例を次に示します。

```json
{
            "apiVersion": "2020-06-25",
            "type": "Microsoft.Compute/virtualMachines/providers/guestConfigurationAssignments",
            "name": "<vm_name>/Microsoft.GuestConfiguration/<configuration_name>",
            "location": "<vm_location>",
            "dependsOn": [
                "Microsoft.Compute/virtualMachines/<vm_name>"
            ],
            "properties": {
                "guestConfiguration": {
                    "name": "AzureWindowsBaseline",
                    "version": "1.*",
                    "assignmentType": "ApplyAndMonitor",
                    "configurationParameter": [
                    {
                        "name": "Minimum Password Length;ExpectedValue",
                        "value": "16"
                    },
                    {
                        "name": "Minimum Password Length;RemediateValue",
                        "value": "16"
                    },
                    {
                        "name": "Maximum Password Age;ExpectedValue",
                        "value": "75"
                    },
                    {
                        "name": "Maximum Password Age;RemediateValue",
                        "value": "75"
                    }
                ]
                }
            }
        }
```

## <a name="assign-a-configuration-using-bicep"></a>Bicep を使用して構成を割り当てる

[Azure Bicep](../../../azure-resource-manager/bicep/overview.md) を使用して、ゲスト構成割り当てをデプロイできます。

カスタム構成を割り当てる例を次に示します。

```Bicep
resource myVM 'Microsoft.Compute/virtualMachines@2021-03-01' existing = {
  name: '<vm_name>'
}

resource myConfiguration 'Microsoft.GuestConfiguration/guestConfigurationAssignments@2020-06-25' = {
  name: '<configuration_name>'
  scope: myVM
  location: resourceGroup().location
  properties: {
    guestConfiguration: {
      name: '<configuration_name>'
      contentUri: '<Url_to_Package.zip>'
      contentHash: '<SHA256_hash_of_package.zip>'
      version: '1.*'
      assignmentType: 'ApplyAndMonitor'
    }
  }
}
```

`AzureWindowBaseline` 組み込み構成を割り当てる例を次に示します。

```Bicep
resource myWindowsVM 'Microsoft.Compute/virtualMachines@2021-03-01' existing = {
  name: '<vm_name>'
}

resource AzureWindowsBaseline 'Microsoft.GuestConfiguration/guestConfigurationAssignments@2020-06-25' = {
  name: 'AzureWindowsBaseline'
  scope: myWindowsVM
  location: resourceGroup().location
  properties: {
    guestConfiguration: {
      name: 'AzureWindowsBaseline'
      version: '1.*'
      assignmentType: 'ApplyAndMonitor'
      configurationParameter: [
        {
          name: 'Minimum Password Length;ExpectedValue'
          value: '16'
        }
        {
          name: 'Minimum Password Length;RemediateValue'
          value: '16'
        }
        {
          name: 'Maximum Password Age;ExpectedValue'
          value: '75'
        }
        {
          name: 'Maximum Password Age;RemediateValue'
          value: '75'
        }
      ]
    }
  }
}
```

## <a name="assign-a-configuration-using-terraform"></a>Terraform を使用して構成を割り当てる

[Terraform](https://www.terraform.io/) を使用して、ゲスト構成割り当てを[デプロイ](/azure/developer/terraform/get-started-windows-powershell)できます。

> [!IMPORTANT]
> Terraform プロバイダー [azurerm_policy_virtual_machine_configuration_assignment](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/virtual_machine_configuration_policy_assignment) は、`assignmentType` プロパティをサポートするよう更新されていないので、監査を実行する構成だけがサポートされます。

カスタム構成を割り当てる例を次に示します。

```Terraform
resource "azurerm_virtual_machine_configuration_policy_assignment" "<configuration_name>" {
  name               = "<configuration_name>"
  location           = azurerm_windows_virtual_machine.example.location
  virtual_machine_id = azurerm_windows_virtual_machine.example.id
  configuration {
    name            = "<configuration_name>"
    contentUri      =  '<Url_to_Package.zip>'
    contentHash     =  '<SHA256_hash_of_package.zip>'
    version         = "1.*"
    assignmentType  = "ApplyAndMonitor
  }
}
```

`AzureWindowBaseline` 組み込み構成を割り当てる例を次に示します。

```Terraform
resource "azurerm_virtual_machine_configuration_policy_assignment" "AzureWindowsBaseline" {
  name               = "AzureWindowsBaseline"
  location           = azurerm_windows_virtual_machine.example.location
  virtual_machine_id = azurerm_windows_virtual_machine.example.id
  configuration {
    name    = "AzureWindowsBaseline"
    version = "1.*"
    parameter {
      name  = "Minimum Password Length;ExpectedValue"
      value = "16"
    }
    parameter {
      name  = "Minimum Password Length;RemediateValue"
      value = "16"
    }
    parameter {
      name  = "Minimum Password Age;ExpectedValue"
      value = "75"
    }
    parameter {
      name  = "Minimum Password Age;RemediateValue"
      value = "75"
    }
  }
}
```

## <a name="next-steps"></a>次の手順

- [ゲスト構成の概要](../concepts/guest-configuration.md)に関するページを確認する。
- カスタム ゲスト構成パッケージの[開発環境](../how-to/guest-configuration-create-setup.md)を設定する。
- ゲスト構成用の[パッケージ成果物を作成する](../how-to/guest-configuration-create.md)。
- 開発環境から[パッケージ成果物をテストする](../how-to/guest-configuration-create-test.md)。
- マシンからアクセス可能になるように、[パッケージ成果物を発行](./guest-configuration-create-publish.md)する。
- `GuestConfiguration` モジュールを使用して、環境を大規模に管理するための [Azure Policy の定義を作成する](../how-to/guest-configuration-create-definition.md)。
- Azure portal を使用して[カスタム ポリシー定義を割り当てる](../assign-policy-portal.md)。
- [ゲスト構成ポリシーの割り当てに関するコンプライアンスの詳細](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration)を見る方法を学習する。
