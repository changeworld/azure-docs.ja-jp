---
title: ジョブのサイズ超過エラー
description: ジョブのサイズまたはテンプレートが大きすぎる場合のエラーをトラブルシューティングする方法について説明します。
ms.topic: troubleshooting
ms.date: 10/07/2020
ms.openlocfilehash: 638bdef246fc908ab997bfb99e7526febdb3792e
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2020
ms.locfileid: "91822152"
---
# <a name="resolve-errors-for-job-size-exceeded"></a>ジョブのサイズ超過に関するエラーを解決する

この記事では、**JobSizeExceededException** エラーと **DeploymentSizeExceededException** エラーを解決する方法について説明します。

## <a name="symptom"></a>症状

テンプレートをデプロイするときに、デプロイが制限を超えたことを示すエラーが表示されます。

## <a name="cause"></a>原因

テンプレートのサイズが 4 MB を超えると、このエラーが発生することがあります。 4 MB の制限は、[copy](copy-resources.md) を使用して多数のインスタンスを作成するリソースの定義で拡張された後のテンプレートの最終的な状態に適用されます。 最後の状態には、変数およびパラメーターの解決済みの値も含まれます。

デプロイ ジョブには、要求に関するメタデータも含まれます。 大きいテンプレートの場合、メタデータとテンプレートが組み合わされると、ジョブで許容されるサイズを超える可能性があります。

テンプレートのその他の制限は次のとおりです。

* パラメーター 256 個
* 変数 256 個
* リソース (コピー数を含む) 800 個
* 出力値 64 個
* テンプレート式内で 24,576 文字

## <a name="solution-1---simplify-template"></a>解決策 1 - テンプレートを簡略化する

最初のオプションは、テンプレートを簡略化することです。 このオプションは、テンプレートでさまざまな種類のリソースをデプロイする場合に使用できます。 テンプレートを[リンクされたテンプレート](linked-templates.md)に分割することを検討してください。 リソースの種類を論理グループに分割し、リンクされたテンプレートをグループごとに追加します。 たとえば、多数のネットワーク リソースをデプロイする必要がある場合、それらのリソースをリンクされたテンプレートに移動できます。

リンクされたテンプレートに依存するようにその他のリソースを設定し、[リンクされたテンプレートの出力から値を取得する](linked-templates.md#get-values-from-linked-template)ことができます。

## <a name="solution-2---reduce-name-size"></a>解決策 2 - 名前のサイズを小さくする

[parameters](template-parameters.md)、[variables](template-variables.md)、[outputs](template-outputs.md) に使用する名前の長さを短くしてください。 これらの値がコピー ループを通じて繰り返されると、大きな名前は何度も増殖します。

## <a name="solution-3---use-serial-copy"></a>解決策 3 - シリアル コピーを使用する

2 つ目のオプションは、コピー ループを[並列処理からシリアル処理に変更する](copy-resources.md#serial-or-parallel)方法です。 このオプションは、コピーによって多数のリソースをデプロイしたことが原因でエラーが発生したと思われる場合にのみ使用してください。 この変更により、リソースが並行してデプロイされないため、デプロイ時間が大幅に長くなる可能性があります。
