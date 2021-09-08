---
title: ゲスト構成割り当てリソースの理解
description: ゲスト構成では、構成をマシンにマップするゲスト構成割り当てという名前の拡張リソースが作成されます。
ms.date: 08/15/2021
ms.topic: conceptual
ms.openlocfilehash: c106ca492166cc604607bfc8da3f9c7d7b3bff2d
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2021
ms.locfileid: "122868569"
---
# <a name="understand-guest-configuration-assignment-resources"></a>ゲスト構成割り当てリソースの理解

Azure Policy が割り当てられている場合で、それが "Guest Configuration" カテゴリに属している場合は、ゲスト割り当てを記述するために含められたメタデータが存在します。

[このドキュメントのビデオ チュートリアルを利用できます](https://youtu.be/DmCphySEB7A)。

ゲスト割り当ては、コンピューターと Azure Policy シナリオとの間のリンクと見なすことができます。 たとえば、次のスニペットでは、Azure Windows ベースライン構成が、最小バージョンを `1.0.0` として、ポリシーのスコープ内のすべてのコンピューターに関連付けられています。

```json
"metadata": {
    "category": "Guest Configuration",
    "guestConfiguration": {
        "name": "AzureWindowsBaseline",
        "version": "1.*"
    }
//additional metadata properties exist
```

## <a name="how-azure-policy-uses-guest-configuration-assignments"></a>Azure Policy でのゲスト構成割り当ての使用方法

このメタデータ情報は、**AuditIfNotExists** か **DeployIfNotExists** のいずれかのポリシー結果を持つ定義の監査リソースを自動的に作成するために、ゲスト構成サービスによって使用されます。 リソースの種類は `Microsoft.GuestConfiguration/guestConfigurationAssignments` です。 Azure Policy は、ゲスト割り当てリソースの **complianceStatus** プロパティを使用して、コンプライアンスの状態を報告します。 詳細については、[コンプライアンス データを取得する](../how-to/get-compliance-data.md)を参照してください。

### <a name="deletion-of-guest-assignments-from-azure-policy"></a>Azure Policy からのゲスト割り当ての削除

Azure Policy 割り当てが削除されると、ゲスト構成割り当てがそのポリシーによって作成されていた場合、そのゲスト構成割り当ても削除されます。

## <a name="manually-creating-guest-configuration-assignments"></a>手動でのゲスト構成割り当ての作成

Azure Resource Manager のゲスト割り当てリソースは、Azure Policy または任意のクライアント SDK で作成できます。

展開テンプレートの例:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "apiVersion": "2021-01-25",
      "type": "Microsoft.Compute/virtualMachines/providers/guestConfigurationAssignments",
      "name": "myMachine/Microsoft.GuestConfiguration/myConfig",
      "location": "westus2",
      "properties": {
        "guestConfiguration": {
          "name": "myConfig",
          "contentUri": "https://mystorageaccount.blob.core.windows.net/mystoragecontainer/myConfig.zip?sv=SASTOKEN",
          "contentHash": "SHA256HASH",
          "version": "1.0.0",
          "assignmentType": "ApplyAndMonitor",
          "configurationParameter": {}
        }
      }
    }    
  ]
}
```

次の表で、ゲスト割り当てリソースの各プロパティについて説明します。

| プロパティ | 説明 |
|-|-|
| name | コンテンツ パッケージ MOF ファイル内の構成の名前。 |
| contentUri | コンテンツ パッケージ (.zip) への HTTPS URI パス。 |
| contentHash | コンテンツ パッケージの SHA256 ハッシュ値。変更されていないことを確認するために使用されます。 |
| version | コンテンツ パッケージのバージョン。 組み込みパッケージにのみ使用され、カスタム コンテンツ パッケージには使用されません。 |
| assignmentType | 割り当ての動作。 使用できる値: `Audit`、`ApplyandMonitor`、および `ApplyandAutoCorrect`。 |
| configurationParameter | コンピューターにダウンロードされた後にオーバーライドされるコンテンツ パッケージ MOF ファイル内の DSC リソースの種類、名前、および値の一覧。 |

### <a name="deletion-of-manually-created-guest-configuration-assignments"></a>手動で作成されたゲスト構成割り当ての削除

手動で作成されたゲスト構成割り当て (Azure Resource Manager テンプレートのデプロイなど) は、手動で削除する必要があります。
親リソース (仮想マシンまたは Arc 対応マシン) を削除すると、ゲスト構成割り当ても削除されます。

ゲスト構成割り当てを手動で削除するには、次の例を使用します。 "\<\>" かっこで示される、サンプルの文字列すべてを置き換えてください。

```PowerShell
# First get details about the guest configuration assignment
$resourceDetails = @{
  ResourceGroupName = '<myResourceGroupName>'
  ResourceType      = 'Microsoft.Compute/virtualMachines/providers/guestConfigurationAssignments/'
  ResourceName      = '<myVMName>/Microsoft.GuestConfiguration'
  ApiVersion        = '2020-06-25'
}
$guestAssignment = Get-AzResource @resourceDetails

# Review details of the guest configuration assignment
$guestAssignment

# After reviewing properties of $guestAssignment to confirm
$guestAssignment | Remove-AzResource
```

## <a name="next-steps"></a>次のステップ

- [ゲスト構成の概要](./guest-configuration.md)に関するページを確認する。
- カスタム ゲスト構成パッケージの[開発環境](../how-to/guest-configuration-create-setup.md)を設定する。
- ゲスト構成用の[パッケージ成果物を作成する](../how-to/guest-configuration-create.md)。
- 開発環境から[パッケージ成果物をテストする](../how-to/guest-configuration-create-test.md)。
- `GuestConfiguration` モジュールを使用して、環境を規模に応じて管理するための [Azure Policy の定義を作成する](../how-to/guest-configuration-create-definition.md)。
- Azure portal を使用して[カスタム ポリシー定義を割り当てる](../assign-policy-portal.md)。
- [ゲスト構成ポリシーの割り当てに関するコンプライアンスの詳細](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration)を見る方法を学習する。
