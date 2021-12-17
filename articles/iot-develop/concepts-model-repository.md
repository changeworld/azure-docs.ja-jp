---
title: デバイス モデル リポジトリの概念の理解 | Microsoft Docs
description: ソリューション開発者や IT プロフェッショナル向けに、デバイス モデル リポジトリの基本的な概念について説明します。
author: rido-min
ms.author: rmpablos
ms.date: 11/12/2021
ms.topic: conceptual
ms.service: iot-develop
services: iot-develop
ms.openlocfilehash: 7b32983707f2c23ef6385fc974f4f1d50ac48e50
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132399298"
---
# <a name="device-models-repository"></a>デバイス モデル リポジトリ

デバイス モデル リポジトリ (DMR) を使用すると、デバイス ビルダーは IoT プラグ アンド プレイ デバイス モデルを管理および共有できます。 デバイス モデルは、[Digital Twins モデリング言語 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) を使用して定義された JSON LD ドキュメントです。

DMR によって、デバイス ツイン モデル識別子 (DTMI) に基づいて DTDL インターフェイスをフォルダー構造に格納するパターンが定義されます。 DTMI を相対パスに変換することで、DMR 内のインターフェイスを特定できます。 たとえば、 `dtmi:com:example:Thermostat;1` DTMI は `/dtmi/com/example/thermostat-1.json` に変換され、URL [https://devicemodels.azure.com/dtmi/com/example/thermostat-1.json](https://devicemodels.azure.com/dtmi/com/example/thermostat-1.json) のパブリック ベース URL `devicemodels.azure.com` から取得でき ます。

## <a name="index-expanded-and-metadata"></a>インデックス、展開済み、メタデータ

DMR の規約には、ホストされるモデルの使用を簡略化するための追加のアーティファクトが含まれています。 これらの機能は、カスタムリポジトリまたはプライベートリポジトリでは _オプション_ となります。

- _Index_。 使用可能なすべての DTMI は、一連の json ファイルによって構成される次のような *インデックス* を介して公開されます: [https://devicemodels.azure.com/index.page.2.json](https://devicemodels.azure.com/index.page.2.json)
- _拡張済み_。 すべての依存関係を持つファイルは、各インターフェイスで使用できます。たとえば: [https://devicemodels.azure.com/dtmi/com/example/temperaturecontroller-1.expanded.json](https://devicemodels.azure.com/dtmi/com/example/temperaturecontroller-1.expanded.json)
- _Metadata_。 このファイルは、リポジトリのキー属性を公開し、最新の公開済みモデル スナップショットで定期的に更新されます。 これには、モデル インデックスまたは拡張モデル ファイルが使用可能かどうかなど、リポジトリが実装する機能が含まれます。 [https://devicemodels.azure.com/metadata.json](https://devicemodels.azure.com/metadata.json) で DMR メタデータにアクセスできます。

## <a name="public-device-models-repository"></a>パブリック デバイス モデル リポジトリ

Microsoft によって、これらの特性を持つパブリック DMR がホストされます:

- キュレーション モデル。 Microsoft によって、GitHub のプル要求 (PR) 検証ワークフローを使用して、使用可能なすべてのインターフェイスがレビューおよび承認されます。
- 不変性。  公開後、インターフェイスを更新することはできません。
- ハイパースケール。 Microsoft は、デバイス モデルを公開して使用できる、安全でスケーラブルなエンドポイントを作成するために必要なインフラストラクチャを提供しています。

## <a name="custom-device-models-repository"></a>カスタム デバイス モデル リポジトリ

ローカル ファイル システムやカスタム HTTP Web サーバーなどの任意のストレージ メディア内で同じ DMR パターンを使用して、カスタム DMR を作成します。 DMR にアクセスするために使用するベース URL を変更して、パブリック DMR と同じ方法でカスタム DMR からデバイス モデルを取得できます。

> [!NOTE]
> Microsoft は、パブリック DMR のデバイス モデルを検証するためのツールを提供しています。 これらのツールはカスタム リポジトリで再利用できます。

## <a name="public-models"></a>パブリック モデル

モデル リポジトリに格納されているパブリック デバイス モデルは、すべてのユーザーがアプリケーション内で使用および統合することができます。 パブリック デバイス モデルを使用すると、オープンなエコシステムでデバイス ビルダーおよびソリューション開発者が、IoT プラグ アンド プレイ デバイス モデルを共有したり、再利用したりすることができます。

モデル リポジトリにモデルを公開してパブリックにする方法については、「[モデルを公開する](#publish-a-model)」セクションを参照してください。

ユーザーは、公式の [GitHub リポジトリ](https://github.com/Azure/iot-plugandplay-models)からパブリック インターフェイスを参照、検索、表示できます。

`dtmi` フォルダー内のすべてのインターフェイスは、パブリック エンドポイント [https://devicemodels.azure.com](https://devicemodels.azure.com) からも使用できます

### <a name="resolve-models"></a>モデルを解決する

これらのインターフェイスにプログラムでアクセスするには、NuGet パッケージ [Azure.IoT.ModelsRepository](https://www.nuget.org/packages/Azure.IoT.ModelsRepository) で入手可能な `ModelsRepositoryClient` を使用できます。 既定では、このクライアントは [devicemodels.azure.com](https://devicemodels.azure.com/) で入手可能なパブリック DMR に対してクエリを実行するように構成されており、任意のカスタム リポジトリに対して構成できます。

クライアントは `DTMI` を入力として受け取り、必要なすべてのインターフェイスを含むディクショナリを返します。

```cs
using Azure.IoT.ModelsRepository;

var client = new ModelsRepositoryClient();
IDictionary<string, string> models = client.GetModels("dtmi:com:example:TemperatureController;1");
models.Keys.ToList().ForEach(k => Console.WriteLine(k));
```

予想される出力には、依存関係チェーンで見つかった 3 つのインターフェイスの `DTMI` が表示されます。

```txt
dtmi:com:example:TemperatureController;1
dtmi:com:example:Thermostat;1
dtmi:azure:DeviceManagement:DeviceInformation;1
```

`ModelsRepositoryClient` は、カスタム モデル リポジトリ (http(s) を介して利用可能) に対してクエリを実行するように構成できます。また、利用可能な `ModelDependencyResolution` のいずれかを使用して依存関係の解決を指定します。

- 無効。 指定されたインターフェイスだけを返します。依存関係はありません。
- 有効にします。 依存関係チェーン内のすべてのインターフェイスを返します。

> [!Tip] 
> カスタム リポジトリで `.expanded.json` ファイルが公開されない場合があります。使用できない場合、クライアントはフォールバックして各依存関係をローカルに処理します。

次のサンプル コードは、カスタム リポジトリのベース URL を使用して `ModelsRepositoryClient` を初期化する方法を示しています。この例では、`expanded` フォームを使用せずに (`raw` エンドポイントで利用できないため)、GitHub API からの `raw` URL を使用しています。 `AzureEventSourceListener` は、クライアントによって実行される HTTP 要求を検査するために初期化されます。

```cs
using AzureEventSourceListener listener = AzureEventSourceListener.CreateConsoleLogger();

var client = new ModelsRepositoryClient(
    new Uri("https://raw.githubusercontent.com/Azure/iot-plugandplay-models/main"));

ModelResult model = await client.GetModelAsync(
    "dtmi:com:example:TemperatureController;1", 
    dependencyResolution: ModelDependencyResolution.Enabled);

model.Content.Keys.ToList().ForEach(k => Console.WriteLine(k));
```

Azure SDK GitHub リポジトリ ([Azure.Iot.ModelsRepository/samples](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/modelsrepository/Azure.IoT.ModelsRepository/samples)) のソース コード内で、さらに多くのサンプルを入手できます。

## <a name="publish-a-model"></a>モデルを公開する

> [!Important]
> パブリック DMR にモデルを送信できるようにするには、GitHub アカウントが必要です。

1. パブリック GitHub リポジトリ ([https://github.com/Azure/iot-plugandplay-models](https://github.com/Azure/iot-plugandplay-models)) をフォークします。
1. フォークされたリポジトリをクローンします。 必要に応じて、新しいブランチを作成して、変更を `main` ブランチから分離します。
1. フォルダーまたはファイル名の規則を使用して、新しいインターフェイスを `dtmi` フォルダーに追加します。 詳細については、「[`dtmi/` フォルダーへのモデルのインポート](#import-a-model-to-the-dtmi-folder)」を参照してください。
1. `dmr-client` ツールを使用して、ローカルでモデルを検証します。 詳細については、「[モデルを検証する](#validate-models)」を参照してください。
1. 変更をローカルでコミットし、フォークにプッシュします。
1. フォークから、`main` ブランチを対象とするプル要求を作成します。 「[Issue もしくはプル リクエストの作成](https://docs.github.com/free-pro-team@latest/desktop/contributing-and-collaborating-using-github-desktop/creating-an-issue-or-pull-request)」のドキュメントを参照してください。
1. [プル要求の要件](https://github.com/Azure/iot-plugandplay-models/blob/main/pr-reqs.md)を確認します。

送信されたインターフェイスを検証する一連の GitHub アクションがプル要求によってトリガーされ、プル要求がすべての要件を満たしていることが確認されます。

Microsoft によって、3 営業日以内にすべてのチェックが行われ、プル要求への応答が行われます。

### <a name="dmr-client-tools"></a>`dmr-client` のツール

PR チェック中にモデルの検証に使用されるツールは、DTDL インターフェイスをローカルに追加して検証する際にも使用できます。

> [!NOTE]
> このツールには、[.NET SDK](https://dotnet.microsoft.com/download) バージョン 3.1 以上が必要です。

### <a name="install-dmr-client"></a>`dmr-client` のインストール

```bash
dotnet tool install --global Microsoft.IoT.ModelsRepository.CommandLine --version 1.0.0-beta.5
```

### <a name="import-a-model-to-the-dtmi-folder"></a>`dtmi/` フォルダーへのモデルのインポート

モデルが既に json ファイルに格納されている場合は、`dmr-client import` コマンドを使用して、`dtmi/` フォルダーに正しいファイル名で追加できます。

```bash
# from the local repo root folder
dmr-client import --model-file "MyThermostat.json"
```

> [!TIP]
> `--local-repo` 引数を使用して、ローカル リポジトリのルート フォルダーを指定できます。

### <a name="validate-models"></a>モデルを検証する

以下の `dmr-client validate` コマンドを使用して、モデルを検証できます。

```bash
dmr-client validate --model-file ./my/model/file.json
```

> [!NOTE]
> 検証では、すべてのインターフェイスが DTDL 言語仕様と互換性があることを確認するために、最新の DTDL パーサー バージョンが使用されます。

外部の依存関係を検証するには、それらがローカル リポジトリに存在している必要があります。 モデルを検証するには、`--repo` オプションを使用して `local` フォルダーまたは `remote` フォルダーを指定し、依存関係を解決します。

```bash
# from the repo root folder
dmr-client validate --model-file ./my/model/file.json --repo .
```

### <a name="strict-validation"></a>厳密な検証

DMR には追加の[要件](https://github.com/Azure/iot-plugandplay-models/blob/main/pr-reqs.md)が含まれています。その要件に対してモデルを検証するには、`stict` フラグを使用します。

```bash
dmr-client validate --model-file ./my/model/file.json --repo . --strict true
```

コンソール出力にエラー メッセージが表示されていないかを確認します。

### <a name="export-models"></a>モデルをエクスポートする

JSON 配列を使用することで、モデルを特定リポジトリ (ローカルまたはリモート) から 1 つのファイルにエクスポートできます。

```bash
dmr-client export --dtmi "dtmi:com:example:TemperatureController;1" -o TemperatureController.expanded.json
```

### <a name="create-the-repository-index"></a>リポジトリを作成する `index`

DMR には、公開時に使用可能なすべての DTMI のリストを持つ *インデックス* を含めることができます。 [DMR Tools Wiki](https://github.com/Azure/iot-plugandplay-models-tools/wiki/Model-Index) で説明されているように、このファイルは複数のファイルに分割できます。

カスタム DMR またはプライベート DMR でインデックスを生成するには、index コマンドを使います:

```bash
dmr-client index -r . -o index.json
```

> [!NOTE]
> パブリック DMR は、 https://devicemodels.azure.com/index.json で使用可能となる、更新されたインデックスを提供できるように構成されます:

### <a name="create-expanded-files"></a>*拡張済み* ファイルを作る

拡張済みファイルは、次のコマンドを使用して生成できます:

```bash
dmr-client expand -r .
```

## <a name="next-steps"></a>次のステップ

次の手順では、「[IoT プラグ アンド プレイのアーキテクチャ](concepts-architecture.md)」を確認することをお勧めします。
