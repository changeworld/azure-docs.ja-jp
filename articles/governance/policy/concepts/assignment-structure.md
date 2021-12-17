---
title: ポリシー割り当て構造の詳細
description: ポリシーの定義とパラメーターを評価のためにリソースに関連付けるために Azure Policy によって使用されるポリシー割り当ての定義について説明します。
ms.date: 08/17/2021
ms.topic: conceptual
ms.openlocfilehash: 1b29becc3324c0d174db2102c4ae7ac15206e567
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2021
ms.locfileid: "122323442"
---
# <a name="azure-policy-assignment-structure"></a>Azure Policy の割り当ての構造

ポリシーの割り当ては、どのポリシーまたはイニシアティブにどのリソースを割り当てるか定義するために Azure Policy によって使用されます。 ポリシーの割り当てでは、割り当て時にそのリソース グループのパラメーターの値を決定でき、ポリシー定義を再利用して、同じリソースのプロパティのコンプライアンス ニーズが異なる場合に対応できます。

ポリシーの割り当てを作成するには、JSON を使用します。 ポリシーの割り当てには、次に対する要素が含まれています。

- 表示名
- description
- metadata
- 適用モード
- 除外範囲
- ポリシーの定義
- 非コンプライアンス メッセージ
- parameters

たとえば、次の JSON では、動的パラメーターでの _DoNotEnforce_ モードのポリシー割り当てを示します。

```json
{
    "properties": {
        "displayName": "Enforce resource naming rules",
        "description": "Force resource names to begin with DeptA and end with -LC",
        "metadata": {
            "assignedBy": "Cloud Center of Excellence"
        },
        "enforcementMode": "DoNotEnforce",
        "notScopes": [],
        "policyDefinitionId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming",
        "nonComplianceMessages": [
            {
                "message": "Resource names must start with 'DeptA' and end with '-LC'."
            }
        ],
        "parameters": {
            "prefix": {
                "value": "DeptA"
            },
            "suffix": {
                "value": "-LC"
            }
        }
    }
}
```

Azure Policy のサンプルはすべて「[Azure Policy のサンプル](../samples/index.md)」にあります。

## <a name="display-name-and-description"></a>表示名と説明

**displayName** と **description** は、ポリシーの割り当てを識別し、リソースの特定のセットで使用するためのコンテキストを提供するために使用します。 **displayName** の最大長は _128_ 文字で、**description** の最大長は _512_ 文字です。

## <a name="metadata"></a>Metadata

省略可能な `metadata` プロパティには、ポリシー割り当てに関する情報が格納されています。 お客様は `metadata` で組織にとって有用なすべてのプロパティと値を定義できます。 ただし、Azure Policy で使用される "_一般的_" なプロパティがいくつかあります。 各 `metadata` プロパティには 1024 文字の制限があります。

### <a name="common-metadata-properties"></a>一般的なメタデータのプロパティ

- `assignedBy` (文字列): 割り当てを作成したセキュリティ プリンシパルのフレンドリ名。
- `createdBy` (文字列): 割り当てを作成したセキュリティ プリンシパルの GUID。
- `createdOn` (文字列): ユニバーサル ISO 8601 日時形式の割り当て作成時刻。
- `parameterScopes` (オブジェクト): キーと値のペアのコレクション。ここで、キーは [strongType](./definition-structure.md#strongtype) 構成済みパラメーター名と一致し、値は、_strongType_ を照合して使用可能なリソースの一覧を提供するためにポータルで使用されるリソース スコープを定義します。 スコープが割り当てスコープと異なる場合、ポータルによりこの値が設定されます。 設定されると、ポータルでのポリシー割り当ての編集により、パラメーターのスコープがこの値に自動的に設定されます。 ただし、スコープはその値にロックされず、別のスコープに変更できます。

  次の `parameterScopes` の例は、**backupPolicyId** という名前の _strongType_ パラメーターについてです。これは、ポータルで割り当てが編集されたときにリソース選択のスコープを設定します。

  ```json
  "metadata": {
      "parameterScopes": {
          "backupPolicyId": "/subscriptions/{SubscriptionID}/resourcegroups/{ResourceGroupName}"
      }
  }
  ```

- `updatedBy` (文字列): 割り当てを更新したセキュリティ プリンシパルのフレンドリ名 (存在する場合)。
- `updatedOn` (文字列): ユニバーサル ISO 8601 日時形式の割り当て更新時刻 (存在する場合)。

## <a name="enforcement-mode"></a>適用モード

**enforcementMode** プロパティを使用すると、ユーザーは、ポリシーの適用を開始したり、[Azure Activity ログ](../../../azure-monitor/essentials/platform-logs-overview.md)のエントリをトリガーしたりすることなく、既存のリソースに対するポリシーの結果をテスできます。 このシナリオは、一般に "What If" と呼ばれ、安全な展開のプラクティスに沿っています。 **enforcementMode** は、[無効](./effects.md#disabled)の効果とは異なります。なぜなら、その効果は、リソースの評価がまったく行われないようにするからです。

このプロパティの値は次のとおりです。

|モード |JSON 値 |Type |手動で修復 |アクティビティ ログ エントリ |説明 |
|-|-|-|-|-|-|
|Enabled |Default |string |はい |はい |ポリシーの効果は、リソースの作成時または更新時に適用されます。 |
|無効 |DoNotEnforce |string |はい |いいえ | ポリシーの効果は、リソースの作成時または更新時に適用されません。 |

ポリシーまたはイニシアティブの定義で **enforcementMode** を指定しないと、値 _Default_ が使用されます。 **enforcementMode** が _DoNotEnforce_ に設定されている場合でも、[deployIfNotExists](./effects.md#deployifnotexists) のポリシーに対して [修復タスク](../how-to/remediate-resources.md)を開始できます。

## <a name="excluded-scopes"></a>除外範囲

割り当ての **範囲** には、子リソース コンテナーと子リソースがすべて含まれます。 子リソース コンテナーまたは子リソースに定義を適用しない場合、**notScopes** を設定することでそれぞれを評価から "_除外_" できます。 このプロパティは、1 つまたは複数のリソース コンテナーまたはリソースを評価から除外することを可能にする配列です。 **notScopes** は、初回割り当ての作成後、追加または更新できます。

> [!NOTE]
> "_除外された_" リソースは、"_適用除外された_" リソースとは異なります。 詳細については、「[Azure Policy でのスコープについて](./scope.md)」を参照してください。

## <a name="policy-definition-id"></a>ポリシー定義 ID

このフィールドでは、ポリシー定義またはイニシアティブ定義の完全なパス名を指定する必要があります。
`policyDefinitionId` は文字列であり、配列ではありません。 複数のポリシーを一緒に割り当てることが多い場合は、代わりに[イニシアティブ](./initiative-definition-structure.md)を使用することをお勧めします。

## <a name="non-compliance-messages"></a>非コンプライアンス メッセージ

リソースがポリシーまたはイニシアチブ定義に準拠していない理由を説明するカスタム メッセージを設定するには、割り当て定義で `nonComplianceMessages` を設定します。 このノードは `message` エントリの配列です。 このカスタム メッセージは、非コンプライアンスの既定のエラー メッセージというだけでなく、オプションでもあります。

> [!IMPORTANT]
> コンプライアンス非対応向けのカスタム メッセージは、[Resource Manager モード](./definition-structure.md#resource-manager-modes)定義を使用した定義またはイニシアチブでのみサポートされます。

```json
"nonComplianceMessages": [
    {
        "message": "Default message"
    }
]
```

割り当てがイニシアチブ用の場合は、イニシアチブのポリシー定義ごとに異なるメッセージを構成できます。 メッセージでは、イニシアチブ定義で構成されている `policyDefinitionReferenceId` 値を使用します。 詳細については、[ポリシー定義のプロパティ](./initiative-definition-structure.md#policy-definition-properties)に関するページを参照してください。

```json
"nonComplianceMessages": [
    {
        "message": "Default message"
    },
    {
        "message": "Message for just this policy definition by reference ID",
        "policyDefinitionReferenceId": "10420126870854049575"
    }
]
```

## <a name="parameters"></a>パラメーター

ポリシー割り当てのこのセグメントでは、[ポリシー定義またはイニシアティブ定義](./definition-structure.md#parameters)で定義されているパラメーターに対する値を指定します。 この設計により、異なるリソースでポリシーまたはイニシアティブの定義を再利用しながら、異なるビジネス値や結果を確認することができます。

```json
"parameters": {
    "prefix": {
        "value": "DeptA"
    },
    "suffix": {
        "value": "-LC"
    }
}
```

この例では、ポリシー定義で以前に定義されているパラメーターは、`prefix` および `suffix` です。 この特定のポリシー割り当てでは、`prefix` を **DeptA** に、`suffix` を **-LC** に設定します。 同じポリシー定義を異なる部門の異なるパラメーター セットで再利用できるため、柔軟性を提供しながら、ポリシー定義の重複と複雑さを軽減できます。

## <a name="next-steps"></a>次のステップ

- [ポリシー定義の構造](./definition-structure.md)についてさらに学習します。
- [プログラムによってポリシーを作成する](../how-to/programmatically-create.md)方法を理解します。
- [コンプライアンス データを取得する](../how-to/get-compliance-data.md)方法を学習します。
- [準拠していないリソースを修復する](../how-to/remediate-resources.md)方法を学習します。
- 「[Azure 管理グループのリソースを整理する](../../management-groups/overview.md)」で、管理グループとは何かを確認します。
