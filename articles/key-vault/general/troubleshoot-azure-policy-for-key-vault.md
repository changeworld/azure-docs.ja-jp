---
title: Key Vault での Azure Policy の実装に関する問題のトラブルシューティング
description: Key Vault への Azure Policy の実装に関する問題のトラブルシューティング
author: sebansal
ms.author: sebansal
ms.date: 08/17/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 380077339920071fa56d385e6de8f7e7a9e84321
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131072543"
---
# <a name="troubleshoot-issues-with-implementing-azure-policy-on-key-vault"></a>Key Vault での Azure Policy の実装に関する問題のトラブルシューティング

この記事では、[Key Vault 用の Azure Policy](./azure-policy.md) を設定するときに発生する可能性のある一般的なエラーをトラブルシューティングする方法について説明し、それらを解決する方法を提案します。

## <a name="about-azure-policy-for-key-vault"></a>Key Vault 用の Azure Policy について

[Azure Policy](../../governance/policy/index.yml) は、ユーザーが大規模な Azure 環境を監査および管理する機能を提供するガバナンス ツールです。 Azure Policy は、割り当てられたポリシー規則に Azure リソースが確実に準拠するように、Azure リソースにガードレールを配置する機能を提供します。 これによりユーザーは、利用中の Azure 環境の監査、リアルタイムの適用、修復を実行できます。 ポリシーによって実行された監査の結果は、どのリソースとコンポーネントが準拠しているか、していないかのドリルダウンを表示できるコンプライアンス ダッシュボードでユーザーが使用できるようになります。

### <a name="logging"></a>ログ記録

ポリシーの評価がどのように行われているかを監視するには、Key Vault のログを確認できます。 監視を行うには、Azure Key Vault のログ記録を有効にします。これにより、指定した Azure ストレージ アカウントに情報が保存されます。 この設定の詳しい手順については、「[Key Vault のログ記録を有効にする方法](howto-logging.md)」を参照してください。

ログを有効にすると、ポリシー関連のログ情報を収集するために、**AzurePolicyEvaluationDetails** という名前の新しいコンテナーが指定したストレージ アカウントに自動的に作成されます。 

> [!NOTE]
> 監視データ (特にログ ファイル) には機密情報が含まれている可能性があるため、それへのアクセスを厳密に規制する必要があります。 [組み込みの監視 Azure ロール](../../azure-monitor/roles-permissions-security.md)の割り当てとアクセスの制限について確認してください。
> 
> 

個々の BLOB はテキストとして格納されます (JSON BLOB 形式)。 キー ポリシーのログ エントリの例として、"[キーには有効期限が設定されている必要がある](azure-policy.md?tabs=keys#secrets-should-have-expiration-date-set)" を使用します。 このポリシーによって、キー コンテナー内のすべてのキーが評価され、有効期限が設定されていないキーには非準拠というフラグが設定されます。

```json
{
  "ObjectName": "example",
  "ObjectType": "Key",
  "IsComplianceCheck": false,
  "EvaluationDetails": [
    {
      "AssignmentId": "<subscription ID>",
      "AssignmentDisplayName": "[Preview]: Key Vault keys should have an expiration date",
      "DefinitionId": "<definition ID>",
      "DefinitionDisplayName": "[Preview]: Key Vault keys should have an expiration date",
      "Outcome": "NonCompliant",
      "ExpressionEvaluationDetails": [
        {
          "Result": "True",
          "Expression": "type",
          "ExpressionKind": "Field",
          "ExpressionValue": "Microsoft.KeyVault.Data/vaults/keys",
          "TargetValue": "Microsoft.KeyVault.Data/vaults/keys",
          "Operator&quot;: &quot;Equals"
        },
        {
          "Result": "True",
          "Expression": "Microsoft.KeyVault.Data/vaults/keys/attributes.expiresOn",
          "ExpressionKind": "Field",
          "ExpressionValue": "******",
          "TargetValue": "False",
          "Operator&quot;: &quot;Exists"
        }
      ]
    }
  ]
}
```

次の表にフィールド名と説明を示します。

| フィールド名 | 説明 |
| --- | --- |
| **ObjectName** |オブジェクトの名前 |
| **ObjectType** |キー コンテナー オブジェクトの種類 (つまり、証明書、シークレット、またはキー) |
| **IsComplianceCheck** |夜間監査中に評価が発生した場合は true、リソースの作成または更新中に評価が発生した場合は false |
| **AssignmentId** | ポリシー割り当ての ID |
| **AssignmentDisplayName** | ポリシー割り当てのフレンドリ名 |
| **DefinitionId** | 割り当てのポリシー定義の ID |
| **DefinitionDisplayName** | 割り当てのポリシー定義のフレンドリ名 |
| **Outcome** | ポリシー評価の結果 |
| **ExpressionEvaluationDetails** | ポリシーの評価中に実行される評価の詳細 |
| **ExpressionValue** | ポリシー評価中の指定されたフィールドの実際の値 |
| **TargetValue** | 指定されたフィールドの予期される値 |


### <a name="frequently-asked-questions"></a>よく寄せられる質問

#### <a name="key-vault-recovery-blocked-by-azure-policy"></a>Azure Policy によって Key Vault の回復がブロックされる

理由の 1 つとして、サブスクリプション (または管理グループ) に、回復をブロックしているポリシーがあることが考えられます。 この問題を解決するには、コンテナーが回復中のときは適用されないようにポリシーを調整します。

**組み込み** ポリシーのために回復に対してエラーの種類 ```RequestDisallowedByPolicy``` が表示される場合は、**最新バージョン** を使用していることを確認してください。 

独自のロジックで **カスタム ポリシー** を作成した場合は、論理的な削除を要求するために使用できるポリシーの部分の例を次に示します。 論理的に削除されたコンテナーの回復では、コンテナーの作成または更新と同じ API を使用します。 ただし、コンテナーのプロパティを指定する代わりに、値が "recover" である 1 つの "createMode" プロパティを使用します。 コンテナーは、削除されたときのプロパティで復元されます。 特定のプロパティが構成されていない場合に要求をブロックするポリシーによっても、論理的に削除されたコンテナーの回復がブロックされます。 この問題を解決するには、"createMode" が "recover" である要求をポリシーが無視するようになる句を追加します。

"createMode" が "recover" ではない場合にのみポリシーが適用されるようになる句があることがわかります。

```

    "policyRule": { 
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.KeyVault/vaults"
          }, 
          {
            "not": {
              "field": "Microsoft.Keyvault/vaults/createMode",
              "equals": "recover"
            }
          },
          {
            "anyOf": [
              {
                "field": "Microsoft.KeyVault/vaults/enableSoftDelete",
                "exists": "false"
              },
              {
                "field": "Microsoft.KeyVault/vaults/enableSoftDelete",
                "equals": "false"
              }
            ]
          }
        ]
      },
      "then": {
        "effect": "[parameters('effect')]"
      }
    }
```

#### <a name="latency-on-azure-policy-assignment-delete-on-key-vault"></a>Key Vault で Azure ポリシーの割り当てを削除するときの待機時間 

Microsoft.KeyVault.Data: 削除されたポリシー割り当ての適用が停止されるまでに最大 24 時間かかることがあります。 

軽減策: ポリシー割り当ての効果を "Disabled" に更新します。


#### <a name="secret-creation-via-arm-template-missing-out-policy-evaluation"></a>ARM テンプレートによるシークレット作成でポリシーが評価されない

シークレットの作成を評価するデータ プレーン ポリシーは、[ARM テンプレートによって作成されたシークレット](../secrets/quick-create-template.md?tabs=CLI)には、シークレットの作成時には適用されません。 24 時間後に、コンプライアンス チェックが自動的に実行されて、コンプライアンスの結果を確認できるようになります。


## <a name="next-steps"></a>次の手順

* [Azure Policy の使用に関するエラーをトラブルシューティングする](../../governance/policy/troubleshoot/general.md)方法を確認する
* [Azure Policy の既知の問題](https://github.com/azure/azure-policy#known-issues)について確認する
