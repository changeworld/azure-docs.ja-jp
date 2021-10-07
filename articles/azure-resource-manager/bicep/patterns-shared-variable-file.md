---
title: 共有変数ファイル パターン
description: 共有変数ファイル パターンについて説明します。
author: johndowns
ms.author: jodowns
ms.topic: conceptual
ms.date: 08/18/2021
ms.openlocfilehash: eb9a24c0c5b6b7bd037fe5da13fe108878f55a15
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128648318"
---
# <a name="shared-variable-file-pattern"></a>共有変数ファイル パターン

Bicep ファイル内の共有値の繰り返しを減少させます。 代わりに、それらの値は Bicep ファイル内の共有 JSON ファイルから読み込みます。 配列を使用している場合は、Bicep コード内で、共有値をデプロイ固有の値と連結します。

## <a name="context-and-problem"></a>コンテキストと問題

Bicep コードを記述するときには、一連の Bicep ファイルにわたって再利用する共通の変数がある場合があります。 リソースを宣言するたびに、Bicep ファイル間で値のコピーと貼り付けを行うなど、それぞれの値を複製することもできます。 ただし、この方法はエラーを招きやすいので、変更を加える必要がある場合は、他のリソース定義との同期を維持するためにそれぞれのリソース定義を更新する必要があります。

さらに、配列として定義されている変数を扱うときには、複数の Bicep ファイルにわたって共通の値のセットが含まれることがあり、デプロイしようとしているリソースのために特定の値を追加する必要もあります。 共有変数をリソース固有の変数と混在させると、変数の 2 つのカテゴリの違いを理解することがより困難になります。

## <a name="solution"></a>解決策

共有する必要がある変数を含む JSON ファイルを作成します。 [`json()` 関数](bicep-functions-object.md#json)と [`loadTextContent()` 関数](bicep-functions-files.md#loadtextcontent)を使用して、ファイルを読み込み、変数にアクセスします。 配列変数の場合は、[`concat()` 関数](bicep-functions-array.md#concat)を使用して、共有値を特定のリソースの任意のカスタム値と組み合わせます。

## <a name="example-1-naming-prefixes"></a>例 1: 名前付けのプレフィックス

リソースを定義する Bicep ファイルが複数あるとします。 すべてのリソースに対して、一貫性のある名前付けのプレフィックスを使用する必要があります。

会社全体に適用する共通の名前付けのプレフィックスが含まれる JSON ファイルを定義します。

::: code language="json" source="~/azure-docs-bicep-samples/samples/patterns-shared-variable-file/shared-prefixes.json" :::

Bicep ファイルで、共有する名前付けのプレフィックスをインポートする変数を宣言します。

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-shared-variable-file/example-1.bicep" range="1" :::

リソース名を定義するときには、文字列補間を使用して、共有する名前のプレフィックスと、一意の名前のサフィックスを連結します。

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-shared-variable-file/example-1.bicep" range="3-4" :::

## <a name="example-2-network-security-group-rules"></a>例 2: ネットワーク セキュリティ グループの規則

独自のネットワーク セキュリティ グループ (NSG) を定義する Bicep ファイルが複数あるとします。 それぞれの NSG に適用する必要がある共通のセキュリティ規則のセットがあれば、追加する必要があるアプリケーション固有の規則があることになります。

会社全体にわたって適用する共通のセキュリティ規則が含まれる JSON ファイルを定義します。

::: code language="json" source="~/azure-docs-bicep-samples/samples/patterns-shared-variable-file/shared-rules.json" :::

Bicep ファイルで、共有するセキュリティ規則をインポートする変数を宣言します。

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-shared-variable-file/example-2.bicep" range="5" :::

この特定の NSG 用のカスタム規則を表す変数配列を作成します。

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-shared-variable-file/example-2.bicep" range="6-21" :::

NSG リソースを定義します。 `concat()` 関数を使用して 2 つの配列を結合し、`securityRules` プロパティを設定します。

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-shared-variable-file/example-2.bicep" range="23-29" highlight="5" :::

## <a name="considerations"></a>注意事項

- この方法を使用すると、JSON ファイルは Bicep によって生成される ARM テンプレート内に含められます。 Bicep によって生成される JSON ARM テンプレートのファイル制限は 4 MB なので、大きな共有変数ファイルを使わないようにすることが重要です。
- 共有変数の配列が、各 Bicep ファイルで指定されている配列の値と競合しないようにします。 たとえば、構成セット パターンを使用してネットワーク セキュリティ グループを定義する場合は、同じ優先順位と方向を定義している規則が複数存在しないようにします。

## <a name="next-steps"></a>次の手順

[構成セット パターンについて学習します。](patterns-configuration-set.md)
