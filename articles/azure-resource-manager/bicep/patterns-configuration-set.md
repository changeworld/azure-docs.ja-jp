---
title: 構成セット パターン
description: 構成セット パターンについて説明します。
author: johndowns
ms.author: jodowns
ms.topic: conceptual
ms.date: 08/18/2021
ms.openlocfilehash: b2ccac6f646304d7d530e616a57c8490e17aba22
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446666"
---
# <a name="configuration-set-pattern"></a>構成セット パターン

個別のパラメーターを多数定義するのではなく、定義済みの値セットを作成します。 デプロイ中に、使用する値セットを選択します。

## <a name="context-and-problem"></a>コンテキストと問題

多くの場合、1 つの Bicep ファイルで多くのリソースが定義されます。 各リソースには、デプロイ先の環境に応じてそれぞれ異なる構成を使用することが必要な場合があります。 たとえば、App Service プランおよびアプリと、ストレージ アカウントをデプロイする Bicep ファイルを作成するとします。 これらの各リソースには、そのコスト、可用性、および回復性に影響する複数のオプションが用意されています。 運用環境には、高可用性と回復性を優先する構成セットの使用を必要とします。 非運用環境には、コスト削減を優先する別の構成セットの使用を必要とします。

構成設定ごとにパラメーターを作成することもできますが、これにはいくつかの欠点があります。

- この手法では、各リソースに使用する値および各パラメーターを設定することの影響をテンプレート ユーザーに理解してもらう必要があるため、彼らに負担をかけることになります。
- 新しいリソースを定義するたびに、テンプレート内のパラメーターの数が増加します。
- テストされていない、または正しく機能しないパラメーター値の組み合わせを、ユーザーが選択することも考えられます。

## <a name="solution"></a>解決策

環境の種類を指定するために、1 つのパラメーターを作成します。 そのパラメーターの値に基づいてリソースごとに構成が自動的に選択されるように、変数を使用します。

> [!NOTE]
> この手法は、_t-shirt sizing_ と呼ばれることもあります。 T シャツを購入する場合、その長さ、幅、袖などについての選択肢は多くありません。 あなたは小、中、大のサイズの中から選択するだけです。そのサイズに基づいて必要な寸法が T シャツのデザイナーによって事前に定義されています。

## <a name="example"></a>例

非運用と運用の 2 種類の環境にデプロイできるテンプレートがあるとします。 環境の種類によって、必要となる構成は異なります。

| プロパティ | 非運用環境 | 運用環境 |
|-|-|-|
| **App Service プラン** |
| SKU 名 | S2 | P2V3 |
| 容量 (インスタンス数) | 1 | 3 |
| **App Service アプリ** |
| 常時接続 | 無効 | Enabled |
| **[ストレージ アカウント]** |
| SKU 名 | Standard_LRS | Standard_ZRS |

このテンプレートには、構成セット パターンを使用できます。

運用や非運用などの環境の種類を示す 1 つのパラメーターを受け入れます。 `@allowedValues` パラメーター デコレーターを使用することで、あなたが期待する値のみがテンプレートのユーザーによって確実に指定されるようにします。

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-configuration-set/main.bicep" range="5-9" :::

次に、"_マップ変数_" を作成します。これは、環境の種類に応じて特定の構成を定義するためのオブジェクトです。 変数には、`Production` と `NonProduction` という名前の 2 つのオブジェクトがあることにご注意ください。 これらの名前は、前の例のパラメーターに許容されている値と一致します。

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-configuration-set/main.bicep" range="16-49" :::

リソースを定義するときは、構成マップを使用してリソースのプロパティを定義します。

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-configuration-set/main.bicep" range="51-74" highlight="4, 14, 23" :::

## <a name="considerations"></a>注意事項

- マップ変数では、プロパティをリソース別にグループ化して、それらの定義を簡略化することを検討してください。
- マップ変数では、個々のプロパティ値 (例に含まれる `alwaysOn` プロパティなど) を定義することも、オブジェクト プロパティ (例に含まれる SKU プロパティなど) を設定するオブジェクト変数を定義することもできます。
- [リソース条件](conditional-resource-deployment.md)に対して構成セットを使用することを検討してください。 これにより、Bicep コードで、特定のリソースを特定の環境にのみデプロイすることができます。

## <a name="next-steps"></a>次の手順

[共有変数ファイル パターンについて説明します。](patterns-shared-variable-file.md)
