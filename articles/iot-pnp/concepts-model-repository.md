---
title: デバイス モデル リポジトリの概念の理解 | Microsoft Docs
description: ソリューション開発者や IT プロフェッショナル向けに、デバイス モデル リポジトリの基本的な概念について説明します。
author: rido-min
ms.author: rmpablos
ms.date: 09/30/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: cfdf22ac9b97ff7187bc360efe07cfe16249bd6b
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92042883"
---
# <a name="device-model-repository"></a>デバイス モデル リポジトリ

デバイス モデル リポジトリ (DMR) を使用すると、デバイス ビルダーは IoT プラグ アンド プレイ デバイス モデルを管理および共有できます。 デバイス モデルは、[Digital Twins モデリング言語 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) を使用して定義された JSON LD ドキュメントです。

DMR によって、デバイス ツイン モデル識別子 (DTMI) に基づいて DTDL インターフェイスをフォルダー構造に格納するパターンが定義されます。 DTMI を相対パスに変換することで、DMR 内のインターフェイスを特定できます。 たとえば、`dtmi:com:example:Thermostat;1` DTMI は `/dtmi/com/example/thermostat-1.json` に変換されます。

## <a name="public-device-model-repository"></a>パブリック デバイス モデル リポジトリ

Microsoft によって、これらの特性を持つパブリック DMR がホストされます:

- キュレーション モデル。 Microsoft によって、オープンな GitHub PR 検証ワークフローを使用して、使用可能なすべてのインターフェイスがレビューおよび承認されます。
- 不変性。  公開後、インターフェイスを更新することはできません。
- ハイパースケール。 Microsoft によって、セキュリティで保護された拡張性の高いエンドポイントを作成するために必要なすべてのインフラストラクチャが提供されます。

## <a name="custom-device-model-repository"></a>カスタム デバイス モデル リポジトリ

ローカル ファイル システムやカスタム HTTP Web サーバーなどの任意のストレージ メディア内で同じ DMR パターンを使用して、カスタム DMR を作成できます。 DMR にアクセスするために使用するベース URL を変更するだけで、パブリック DRM と同じ方法でカスタム DMR からモデルを取得できます。

> [!NOTE]
> パブリック DMR 内でモデルを検証するために使用されるツールは、カスタム リポジトリ内で再利用できます。

## <a name="public-models"></a>パブリック モデル

モデル リポジトリに格納されているパブリック デジタル ツイン モデルは、すべてのユーザーがアプリケーション内で使用および統合することができます。 パブリック モデルを使用すると、オープンなエコシステムでデバイス ビルダーおよびソリューション開発者が、IoT プラグ アンド プレイ デバイス モデルを共有したり、再利用したりすることができます。

モデル リポジトリにモデルを公開してパブリックにする方法については、「[モデルを公開する](#publish-a-model)」のセクションを参照してください。

ユーザーは、公式の [GitHub リポジトリ](https://github.com/Azure/iot-plugandplay-models)からパブリック インターフェイスを参照、検索、表示できます。

`dtmi` フォルダー内のすべてのインターフェイスは、パブリック エンドポイント [https://devicemodels.azure.com](https://devicemodels.azure.com) からも使用できます

### <a name="resolve-models"></a>モデルを解決する

プログラムを使用してこれらのインターフェイスにアクセスするには、dtmi を、パブリック エンドポイントのクエリに使用できる相対パスに変換する必要があります。 次のコード サンプルは、この方法を示します。

DTMI を絶対パスに変換するには、`IsValidDtmi` と共に `DtmiToPath` 関数を使用します。

```cs
static string DtmiToPath(string dtmi)
{
    if (!IsValidDtmi(dtmi))
    {
        return null;
    }
    // dtmi:com:example:Thermostat;1 -> dtmi/com/example/thermostat-1.json
    return $"/{dtmi.ToLowerInvariant().Replace(":", "/").Replace(";", "-")}.json";
}

static bool IsValidDtmi(string dtmi)
{
    // Regex defined at https://github.com/Azure/digital-twin-model-identifier#validation-regular-expressions
    Regex rx = new Regex(@"^dtmi:[A-Za-z](?:[A-Za-z0-9_]*[A-Za-z0-9])?(?::[A-Za-z](?:[A-Za-z0-9_]*[A-Za-z0-9])?)*;[1-9][0-9]{0,8}$");
    return rx.IsMatch(dtmi);
}
```

結果のパスとリポジトリのベース URL を使用して、インターフェイスを取得できます。

```cs
const string _repositoryEndpoint = "https://devicemodels.azure.com";

string dtmiPath = DtmiToPath(dtmi.ToString());
string fullyQualifiedPath = $"{_repositoryEndpoint}{dtmiPath}";
string modelContent = await _httpClient.GetStringAsync(fullyQualifiedPath);
```

## <a name="publish-a-model"></a>モデルを公開する

> [!Important]
> パブリック DMR にモデルを送信できるようにするには、GitHub アカウントが必要です。

1. パブリック GitHub リポジトリをフォークします: [https://github.com/Azure/iot-plugandplay-models](https://github.com/Azure/iot-plugandplay-models)。
1. フォークされたリポジトリをクローンします。 必要に応じて、新しいブランチを作成して、変更を `main` ブランチから分離します。
1. フォルダーまたはファイル名の規則を使用して、新しいインターフェイスを `dtmi` フォルダーに追加します。 [add-model](#add-model) ツールを参照してください。
1. [変更を検証するためのスクリプト](#validate-files)のセクションを使用して、モデルをローカルで検証します。
1. 変更をローカルでコミットし、フォークにプッシュします。
1. フォークから、`main` ブランチを対象とする PR を作成します。 「[Issue もしくはプル リクエストの作成](https://docs.github.com/free-pro-team@latest/desktop/contributing-and-collaborating-using-github-desktop/creating-an-issue-or-pull-request)」のドキュメントを参照してください。
1. [PR 要件](https://github.com/Azure/iot-plugandplay-models/blob/main/pr-reqs.md)を確認します。

送信された新しいインターフェイスを検証し、PR がすべてのチェックを満たすことを確認する一連の GitHub アクションが PR によってトリガーされます。

Microsoft によって、3 営業日以内にすべてのチェックが行われ、PR への応答が行われます。

### <a name="add-model"></a>add-model

次の手順は、add-model.js スクリプトを使用して新しいインターフェイスを追加する方法を示しています。 このスクリプトでは、Node.js を実行する必要があります。

1. コマンド プロンプトから、ローカル git リポジトリに移動します
1. `npm install` を実行します。
1. `npm run add-model <path-to-file-to-add>` を実行します。

コンソール出力にエラー メッセージが表示されていないかを確認します。

### <a name="local-validation"></a>ローカル検証

PR を送信する前に、同じ検証チェックをローカルで実行して、問題を事前に診断することができます。

#### <a name="validate-files"></a>validate-files

`npm run validate-files <file1.json> <file2.json>` により、ファイル パスが予期されるフォルダーおよびファイル名と一致することが確認されます。

#### <a name="validate-ids"></a>validate-ids

`npm run validate-ids <file1.json> <file2.json>` により、ドキュメント内で定義されているすべての ID がメイン ID と同じルートを使用していることが確認されます。

#### <a name="validate-deps"></a>validate-deps

`npm run validate-deps <file1.json> <file2.json>` により、すべての依存関係が `dtmi` フォルダー内で使用可能であることが確認されます。

#### <a name="validate-models"></a>validate-models

[DTDL 検証サンプル](https://github.com/Azure-Samples/DTDL-Validator)を実行して、モデルをローカルで検証できます。

## <a name="next-steps"></a>次のステップ

次の手順では、「[IoT プラグ アンド プレイのアーキテクチャ](concepts-architecture.md)」を確認することをお勧めします。