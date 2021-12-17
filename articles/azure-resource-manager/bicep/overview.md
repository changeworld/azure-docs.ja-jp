---
title: Azure リソースをデプロイするための Bicep 言語
description: Azure にインフラストラクチャをデプロイするための Bicep 言語について説明します。 JSON を使用してテンプレートを開発するよりも優れた作成エクスペリエンスが提供されます。
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: 565f849edafaa6c083a1e7d21bb67f8b3ab8453e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131443492"
---
# <a name="what-is-bicep"></a>Bicep とは

Bicep は、宣言型の構文を使用して Azure リソースをデプロイするドメイン固有言語 (DSL) です。 簡潔な構文、信頼性の高いタイプ セーフ、およびコードの再利用のサポートが提供されます。 Microsoft では、Bicep によって Azure のコード ソリューションとしてのインフラストラクチャに最適な作成エクスペリエンスが提供されると信じています。

JSON の代わりに Bicep を使用して、Azure Resource Manager テンプレート (ARM テンプレート) を開発できます。 ARM テンプレートを作成するための JSON 構文は冗長になることがあり、複雑な式を必要とします。 Bicep 構文では、その複雑さが軽減され、開発エクスペリエンスが向上します。 Bicep は ARM テンプレート JSON に対する透過的な抽象化であり、JSON テンプレートの機能は一切失われません。 デプロイ中、Bicep CLI により、Bicep ファイルが ARM テンプレート JSON に変換されます。

Bicep は、アプリケーションを作成するための一般的なプログラミング言語としては意図されていません。 Bicep ファイルでは、リソースを作成するための一連のプログラミング コマンドを記述せずに、Azure リソースとリソース プロパティを宣言します。

ARM テンプレートで有効なリソースの種類、API バージョン、およびプロパティは、Bicep ファイルで有効です。

Bicep の作業状態を確認するには、[Bicep プロジェクトのリポジトリ](https://github.com/Azure/bicep)を参照してください。

Bicep の詳細については、次のビデオをご覧ください。

> [!VIDEO https://www.youtube.com/embed/sc1kJfcRQgY]

## <a name="get-started"></a>作業開始

Bicep を使用して作業を開始するには:

1. **ツールをインストールします**。 [Bicep の開発環境とデプロイ環境の設定](./install.md)に関するページを参照してください。 または、[VS Code の Devcontainer または Codespaces のリポジトリ](https://github.com/Azure/vscode-remote-try-bicep)を使用して、構成済みの作成環境を取得することもできます。
2. **[クイックスタート](./quickstart-create-bicep-use-visual-studio-code.md)と [Microsoft Learn の Bicep モジュール](./learn-bicep.md)** を完了します。

既存の ARM テンプレートを Bicep に逆コンパイルするには、[ARM テンプレートを Bicep に逆コンパイルする](./decompile.md)方法に関するページを参照してください。 [Bicep Playground](https://bicepdemo.z22.web.core.windows.net/) を使用すると、Bicep と同等の JSON を並べて表示できます。

Bicep ファイルで使用可能なリソースの詳細については、[Bicep リソースのリファレンス](/azure/templates/)に関するページを参照してください

Bicep の例については、[Bicep GitHub リポジトリ](https://github.com/Azure/bicep/tree/main/docs/examples)を参照してください。

## <a name="benefits-of-bicep-versus-other-tools"></a>他のツールと比較した Bicep の利点

Bicep には、他のオプションに比べて次の利点があります。

- **すべてのリソースの種類と API バージョンのサポート**: Bicep では、Azure サービスのすべてのプレビュー版と GA バージョンがすぐにサポートされます。 リソース プロバイダーが新しいリソースの種類と API バージョンを導入するとすぐに、Bicep ファイルでそれらを使用できます。 新しいサービスは、ツールが更新されるまで待たなくても使用することができます。
- **単純な構文**: 同等の JSON テンプレートと比較すると、Bicep ファイルはより簡潔で読みやすくなっています。 Bicep は、プログラミング言語に関する知識を必要としません。 Bicep 構文は宣言型であり、デプロイするリソースとリソース プロパティを指定します。
- **作成エクスペリエンス**: VS Code を使用して Bicep ファイルを作成すると、ファーストクラスの作成エクスペリエンスが得られます。 エディターには、豊富なタイプセーフ、IntelliSense、構文検証が用意されています。
- **モジュール性**: [モジュール](./modules.md)を使用して、Bicep コードを管理しやすいパーツに分割できます。 モジュールによって、関連するリソースのセットがデプロイされます。 モジュールを使用すると、コードを再利用し、開発を簡素化することができます。 これらのリソースをデプロイする必要がある場合はいつでも、モジュールを Bicep ファイルに追加します。
- **Azure サービスとの統合**: Bicep は、Azure Policy、Template Specs、Blueprints などの Azure サービスと統合されています。
- **管理する状態または状態ファイルがない**: すべての状態が Azure に格納されます。 ユーザーは共同作業を行うことができ、更新プログラムが想定どおりに処理されることを確信できます。 テンプレートをデプロイする前に変更をプレビューするには、[what-if 操作](./deploy-what-if.md)を使用します。
- **コストがかからないオープン ソース**: Bicep は完全に無料です。 Premium 機能に対して料金を支払う必要はありません。 また、Microsoft サポートによってサポートされています。

## <a name="bicep-improvements"></a>Bicep の機能強化

Bicep では、同等の JSON と比較して、より簡単で簡潔な構文が提供されます。 ブラケット表現 `[...]` は使用しません。 代わりに、関数を直接呼び出したり、パラメーターと変数から値を取得したりします。 展開された各リソースにシンボリック名を与えます。それにより、テンプレートでそのリソースを参照しやすくなります。

たとえば、次の JSON からは、リソース プロパティからの出力値が返されます。

```json
"outputs": {
  "hostname": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))).dnsSettings.fqdn]"
    },
}
```

Bicep では、同等の出力式をもっと簡単に記述できます。 次の例では、テンプレート内で定義されるリソースに対して、シンボリック名 **publicIP** を利用して同じプロパティが返されます。

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

構文の完全な比較については、「[テンプレートにおける JSON と Bicep の比較](compare-template-syntax.md)」参照してください。

Bicep では、リソース間の依存関係が自動的に管理されます。 リソースのシンボル名が別のリソース宣言で使用されている場合に `dependsOn` を設定しないようにすることができます。

Bicep ファイルの構造は、JSON テンプレートよりも柔軟です。 ファイル内の任意の場所で、パラメーター、変数、および出力を宣言できます。 JSON では、テンプレートの対応するセクション内で、すべてのパラメーター、変数、および出力を宣言する必要があります。

## <a name="faq"></a>よく寄せられる質問

**既存の言語を使用するのではなく、新しいものを作成するのはなぜですか?**

Bicep は、新しい言語ではなく、既存の ARM テンプレート言語のリビジョンと考えることができます。 構文は変更されましたが、コアとなる機能とランタイムは変わりません。

Bicep を開発する前に、既存のプログラミング言語を使用することを検討したことがありました。 対象ユーザーにとっては、別の言語を開始するよりも、Bicep を学習する方が容易であると判断しました。

**Terraform や他のサードパーティ製のコードとしてのインフラストラクチャ オファリングにエネルギーを集中させないのはなぜですか?**

構成言語とツールの好みはユーザーごとに異なります。 これらすべてのツールで、Azure での優れたエクスペリエンスが提供されるようにしたいと考えています。 Bicep は、その取り組みの一環です。

Terraform を使い慣れている場合は、切り替える理由はありません。 Microsoft は、Azure の Terraform を最高のものにするために尽力しています。

ARM テンプレートを選択したお客様の場合、Bicep によって作成エクスペリエンスが向上するものと確信しています。 また、Bicep は、コードとしてのインフラストラクチャを採用していないお客様の移行にも役立ちます。

**これは運用環境で使用する準備ができていますか?**

はい。 バージョン 0.3 以降、Bicep は Microsoft サポート プランでサポートされています。 Bicep は、ARM テンプレートで実現できるものと同等の機能を備えています。 現在計画されている破壊的変更はありませんが、将来的に破壊的変更を作成することが必要になる可能性があります。

**Bicep は Azure 専用ですか?**

現時点では、Azure を超えて Bicep を拡張する予定はありません。 Microsoft では、Azure を完全にサポートし、デプロイ エクスペリエンスを最適化したいと考えています。

この目標を達成するには、Azure の外部にあるいくつかの API を使用する必要があります。 それらのシナリオのための機能拡張ポイントをご提供する予定です。

**既存の ARM テンプレートはどうなりますか?**

引き続き、今までと全く同様に機能します。 変更を加える必要はありません。 基になる ARM テンプレートの JSON 言語は引き続きサポートされます。 Bicep ファイルは JSON にコンパイルされ、その JSON はデプロイのために Azure に送信されます。

準備ができたら、[JSON ファイルを Bicep に逆コンパイルする](./decompile.md)ことができます。

**Bicep を使用して Azure Stack Hub にデプロイすることはできますか?**

はい。Azure Stack Hub のデプロイには Bicep を使用できます。ただし Bicep では、まだ Azure Stack Hub では利用できない型が表示されることがあるので注意してください。 [Azure Stack Hub クイックスタート テンプレートの GitHub リポジトリ](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/Bicep)で一連の例をご覧いただけます。 

## <a name="known-limitations"></a>既知の制限事項

- Bicep では改行が識別されます。 次に例を示します。

    ```bicep
    resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = if (newOrExisting == 'new') {
      ...
    }
    ```

    これを次のように記述することはできません。

    ```bicep
    resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' =
        if (newOrExisting == 'new') {
          ...
        }
    ```

- 単一の apiProfile をリソースの種類ごとの apiVersion のセットにマップするために使用される apiProfile の概念はサポートされていません。
- ユーザー定義関数はサポートされていません。

## <a name="next-steps"></a>次の手順

[クイックスタート](./quickstart-create-bicep-use-visual-studio-code.md)を開始します。
