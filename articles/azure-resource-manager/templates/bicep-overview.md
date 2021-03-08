---
title: Azure Resource Manager テンプレート用の Bicep 言語
description: Azure Resource Manager テンプレートを使用して Azure にインフラストラクチャをデプロイするための Bicep 言語について説明します。
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 6a2750dc99e82c9cf8c9b8b97d156d3a9fe30f31
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744035"
---
# <a name="what-is-bicep-preview"></a>Bicep とは (プレビュー)

Bicep は、Azure リソースを宣言によってデプロイするための言語です。 簡潔な構文が提供され、モジュール化とコードの再利用に対するサポートが強化されることで、作成エクスペリエンスが簡略化されます。 Bicep は、ドメイン固有言語 (DSL) です。これは、特定のシナリオまたはドメイン向けに設計されていることを意味します。 Bicep は、アプリケーションを作成するための一般的なプログラミング言語としては意図されていません。

Bicep は Azure Resource Manager テンプレート (ARM テンプレート) に対する透過的な抽象化です。 各 Bicep ファイルは、標準の ARM テンプレートにコンパイルされます。 ARM テンプレートで有効なリソースの種類、API バージョン、およびプロパティは、Bicep ファイルで有効です。

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="get-started"></a>作業開始

Bicep の使用を開始するには、[ツールをインストール](https://github.com/Azure/bicep/blob/main/docs/installing.md)します。

ツールをインストールしたら、[Bicep チュートリアル](./bicep-tutorial-create-first-bicep.md)を試してください。 このチュートリアル シリーズでは、Bicep の構造と機能について説明します。 Bicep ファイルをデプロイし、ARM テンプレートを同等の Bicep ファイルに変換します。

同等の JSON および Bicep のファイルを並べて表示するには、[Bicep プレイグラウンド](https://aka.ms/bicepdemo)を参照してください。

Bicep に変換したい既存の ARM テンプレートがある場合は、「[JSON から Bicep に逆コンパイルする](compare-template-syntax.md#decompile-json-to-bicep)」を参照してください。

## <a name="bicep-improvements"></a>Bicep の機能強化

Bicep では、同等の JSON と比較して、より簡単で簡潔な構文が提供されます。 `[...]` 式は使用しません。 代わりに、関数を直接呼び出したり、パラメーターと変数から値を取得したり、リソースを参照したりします。 構文の完全な比較については、「[テンプレートにおける JSON と Bicep の比較](compare-template-syntax.md)」参照してください。

Bicep では、リソース間の依存関係が自動的に管理されます。 リソースのシンボル名が別のリソース宣言で使用されている場合に `dependsOn` を設定しないようにすることができます。

Bicep を使用すると、プロジェクトを複数のモジュールに分割できます。

Bicep ファイルの構造は、JSON テンプレートよりも柔軟です。 ファイル内の任意の場所で、パラメーター、変数、および出力を宣言できます。 JSON では、テンプレートの対応するセクション内で、すべてのパラメーター、変数、および出力を宣言する必要があります。

Bicep の VS Code 拡張機能では、より高度な検証と IntelliSense が提供されます。 たとえば、この拡張機能には、リソースのプロパティを取得するための IntelliSense が用意されています。

## <a name="faq"></a>よく寄せられる質問

**既存の言語を使用するのではなく、新しいものを作成するのはなぜですか?**

Bicep は、新しい言語ではなく、既存の ARM テンプレート言語のリビジョンと考えることができます。 構文は変更されましたが、コアとなる機能とランタイムは変わりません。

Bicep を開発する前に、既存のプログラミング言語を使用することを検討したことがありました。 対象ユーザーにとっては、別の言語を開始するよりも、Bicep を学習する方が容易であると判断しました。

**Terraform や他のサードパーティ製のコードとしてのインフラストラクチャ オファリングにエネルギーを集中させないのはなぜですか?**

構成言語とツールの好みはユーザーごとに異なります。 これらすべてのツールで、Azure での優れたエクスペリエンスが提供されるようにしたいと考えています。 Bicep は、その取り組みの一環です。

Terraform を使い慣れている場合は、切り替える理由はありません。 Microsoft は、Azure の Terraform を最高のものにするために尽力しています。

ARM テンプレートを選択したお客様の場合、Bicep によって作成エクスペリエンスが向上するものと確信しています。 また、Bicep は、コードとしてのインフラストラクチャを採用していないお客様の移行にも役立ちます。

**Bicep は Azure 専用ですか?**

Bicep は、完全なソリューションを Azure にデプロイすることに重点を置いた DSL です。 この目標を達成するには、Azure の外部にあるいくつかの API を使用する必要があります。 それらのシナリオのための機能拡張ポイントをご提供する予定です。

**既存の ARM テンプレートはどうなりますか?**

引き続き、今までと全く同様に機能します。 変更を加える必要はありません。 基になる ARM テンプレートの JSON 言語は引き続きサポートされます。 Bicep ファイルは JSON にコンパイルされ、その JSON はデプロイのために Azure に送信されます。

準備ができたら、[JSON ファイルを Bicep に変換](compare-template-syntax.md#decompile-json-to-bicep)できます。

## <a name="next-steps"></a>次のステップ

[Bicep チュートリアル](./bicep-tutorial-create-first-bicep.md)を開始します。
