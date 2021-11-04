---
title: ジョブのサイズ超過エラー
description: ジョブのサイズまたはテンプレートが大きすぎる場合のエラーをトラブルシューティングする方法について説明します。
ms.topic: troubleshooting
ms.date: 03/23/2021
ms.openlocfilehash: a818cb2f19e929262a124bc094438564f4732e15
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091458"
---
# <a name="resolve-errors-for-job-size-exceeded"></a>ジョブのサイズ超過に関するエラーを解決する

この記事では、**JobSizeExceededException** エラーと **DeploymentJobSizeExceededException** エラーを解決する方法について説明します。

## <a name="symptom"></a>症状

テンプレートをデプロイするときに、デプロイが制限を超えたことを示すエラーが表示されます。

## <a name="cause"></a>原因

このエラーは、許可されている制限の 1 つをデプロイが超えたときに発生します。 通常、このエラーは、テンプレートまたはデプロイを実行するジョブのいずれかが大きすぎる場合に表示されます。

デプロイ ジョブは 1 MB を超えてはなりません。 このジョブには、要求に関するメタデータも含まれます。 大きいテンプレートの場合、メタデータとテンプレートが組み合わされると、ジョブで許容されるサイズを超える可能性があります。

テンプレートは 4 MB を超えてはなりません。 4 MB の制限は、[copy](../templates/copy-resources.md) を使用して多数のインスタンスを作成するリソースの定義で拡張された後のテンプレートの最終的な状態に適用されます。 最後の状態には、変数およびパラメーターの解決済みの値も含まれます。

テンプレートのその他の制限は次のとおりです。

* パラメーター 256 個
* 変数 256 個
* リソース (コピー数を含む) 800 個
* 出力値 64 個
* テンプレート式内で 24,576 文字

コピー ループを使用してリソースをデプロイする場合は、ループ名を依存関係として使用しないでください。

```json
dependsOn: [ "nicLoop" ]
```

代わりに、依存する必要があるループからリソースのインスタンスを使用します。 次に例を示します。

```json
dependsOn: [
    "[resourceId('Microsoft.Network/networkInterfaces', concat('nic-', copyIndex()))]"
]
```

## <a name="solution-1---simplify-template"></a>解決策 1 - テンプレートを簡略化する

最初のオプションは、テンプレートを簡略化することです。 このオプションは、テンプレートでさまざまな種類のリソースをデプロイする場合に使用できます。 テンプレートを[リンクされたテンプレート](../templates/linked-templates.md)に分割することを検討してください。 リソースの種類を論理グループに分割し、リンクされたテンプレートをグループごとに追加します。 たとえば、多数のネットワーク リソースをデプロイする必要がある場合、それらのリソースをリンクされたテンプレートに移動できます。

リンクされたテンプレートに依存するようにその他のリソースを設定し、[リンクされたテンプレートの出力から値を取得する](../templates/linked-templates.md#get-values-from-linked-template)ことができます。

## <a name="solution-2---reduce-name-size"></a>解決策 2 - 名前のサイズを小さくする

[parameters](../templates/parameters.md)、[variables](../templates/variables.md)、[outputs](../templates/outputs.md) に使用する名前の長さを短くしてください。 これらの値がコピー ループを通じて繰り返されると、大きな名前は何度も増殖します。
