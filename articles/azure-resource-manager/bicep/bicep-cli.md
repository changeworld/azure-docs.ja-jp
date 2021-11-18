---
title: Bicep CLI コマンドと概要
description: Bicep CLI で使用できるコマンドについて説明します。 これらのコマンドには、Bicep からの Azure Resource Manager テンプレートの作成が含まれます。
ms.topic: conceptual
ms.date: 10/18/2021
ms.openlocfilehash: bbb9bbb0f960cac27cba794da410842b80356fb6
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132551940"
---
# <a name="bicep-cli-commands"></a>Bicep CLI コマンド

この記事では、Bicep CLI で使用できるコマンドについて説明します。 コマンドを実行するには、[Bicep CLI がインストール](./install.md)されている必要があります。

この記事では、Azure CLI でコマンドを実行する方法を示します。 Azure CLI を使用しない場合は、コマンドの先頭に `az` を指定せずにコマンドを実行します。 たとえば、`az bicep version` が ``bicep version`` になります。

## <a name="build"></a>build

`build` コマンドは、Bicep ファイルを Azure Resource Manager テンプレート (ARM テンプレート) に変換します。 通常、このコマンドを実行する必要はありません。Bicep ファイルのデプロイ時に自動的に実行されます。 Bicep ファイルから作成される ARM テンプレート JSON を表示する場合は、このコマンドを手動で実行してください。

次の例では、_main.bicep_ という名前の Bicep ファイルを、_main.json_ という名前の ARM テンプレートに変換します。 新しいファイルは、Bicep ファイルと同じディレクトリに作成されます。

```azurecli
az bicep build --file main.bicep
```

次の例では、_main.json_ を別のディレクトリに保存します。

```azurecli
az bicep build --file main.bicep --outdir c:\jsontemplates
```

次の例では、作成するファイルの名前と場所を指定します。

```azurecli
az bicep build --file main.bicep --outfile c:\jsontemplates\azuredeploy.json
```

ファイルを `stdout` に出力するには、次のコマンドを使用します。

```azurecli
az bicep build --file main.bicep --stdout
```

Bicep ファイルに外部レジストリを参照するモジュールが含まれている場合、build コマンドによって [restore](#restore) が自動的に呼び出されます。 restore コマンドは、レジストリからファイルを取得し、ローカル キャッシュに格納します。

restore を自動的に呼び出さないようにするには、`--no-restore` スイッチを使用します。

```azurecli
az bicep build --no-restore <bicep-file>
```

外部モジュールのいずれかがまだキャッシュされていない場合、`--no-restore` スイッチを使用したビルド プロセスは失敗します。

```error
The module with reference "br:exampleregistry.azurecr.io/bicep/modules/storage:v1" has not been restored.
```

このエラーが発生した場合は、`--no-restore` スイッチを指定せずに `build` コマンドを実行するか、最初に `bicep restore` を実行します。

`--no-restore` スイッチを使用するには、Bicep CLI バージョン **0.4.1008 以降** が必要です。

## <a name="decompile"></a>decompile

`decompile` コマンドは、ARM テンプレート JSON を Bicep ファイルに変換します。

```azurecli
az bicep decompile --file main.json
```

このコマンドの使用方法の詳細については、「[Bicep への ARM テンプレート JSON の逆コンパイル](decompile.md)」を参照してください。

## <a name="install"></a>インストール

`install` コマンドは、ローカル環境に Bicep CLI を追加します。 詳細については、「[Bicep ツールのインストール](install.md)」を参照してください。

最新バージョンをインストールするには、次のコマンドを実行します。

```azurecli
az bicep install
```

特定のバージョンをインストールするには、以下を実行します。

```azurecli
az bicep install --version v0.3.255
```

## <a name="list-versions"></a>list-versions

`list-versions` コマンドは、使用可能なすべてのバージョンの Bicep CLI を返します。 新しいバージョンに[アップグレード](#upgrade)するか新しいバージョンを[インストール](#install)する場合は、このコマンドを使用します。

```azurecli
az bicep list-versions
```

次のコマンドは、使用可能なバージョンの配列を返します。

```azurecli
[
  "v0.4.1",
  "v0.3.539",
  "v0.3.255",
  "v0.3.126",
  "v0.3.1",
  "v0.2.328",
  "v0.2.317",
  "v0.2.212",
  "v0.2.59",
  "v0.2.14",
  "v0.2.3",
  "v0.1.226-alpha",
  "v0.1.223-alpha",
  "v0.1.37-alpha",
  "v0.1.1-alpha"
]
```

## <a name="publish"></a>[発行]

`publish` コマンドは、レジストリにモジュールを追加します。 Azure コンテナー レジストリが存在しており、レジストリへの発行を行うアカウントが適切なアクセス許可を所持している必要があります。 モジュール レジストリの設定の詳細については、[Bicep モジュール用のプライベート レジストリの使用](private-module-registry.md)に関する記事を参照してください。

ファイルをレジストリに発行すると、[それをモジュール内で参照](modules.md#file-in-registry)できます。

publish コマンドを使用するには、Bicep CLI バージョン **0.4.1008 以降** が必要です。

モジュールをレジストリに発行するには、次のコマンドを使用します。

```azurecli
az bicep publish <bicep-file> --target br:<registry-name>.azurecr.io/<module-path>:<tag>
```

次に例を示します。

```azurecli
az bicep publish storage.bicep --target br:exampleregistry.azurecr.io/bicep/modules/storage:v1
```

`publish` コマンドは、[bicepconfig.json](bicep-config-modules.md) ファイルに定義されたエイリアスを認識しません。 モジュールの完全なパスを指定してください。

> [!WARNING]
> 同じターゲットに発行すると、古いモジュールが上書きされます。 更新するときは、バージョンを増やすことをお勧めします。

## <a name="restore"></a>復元

レジストリに発行されたモジュールを Bicep ファイルで使用する場合、`restore` コマンドにより、必要なすべてのモジュールのコピーがレジストリから取得されます。 これらのコピーはローカル キャッシュに格納されます。 Bicep ファイルは、外部ファイルがローカル キャッシュで使用可能な場合にのみビルドできます。 通常は、`restore` を実行する必要はありません。`build` によって自動的に呼び出されます。

restore コマンドを使用するには、Bicep CLI バージョン **0.4.1008 以降** が必要です。

ファイルの外部モジュールを手動で復元するには、次のコマンドを使用します。

```azurecli
az bicep restore <bicep-file>
```

指定する Bicep ファイルはデプロイするファイルです。 これには、レジストリにリンクするモジュールが含まれている必要があります。 たとえば、次のファイルを復元できます。

```bicep
module stgModule 'br:exampleregistry.azurecr.io/bicep/modules/storage:v1' = {
  name: 'storageDeploy'
  params: {
    storagePrefix: 'examplestg1'
  }
}
```

ローカル キャッシュは次の場所にあります。

```path
%USERPROFILE%\.bicep\br\<registry-name>.azurecr.io\<module-path\<tag>
```

## <a name="upgrade"></a>upgrade

`upgrade` コマンドは、インストールされているバージョンを最新バージョンで更新します。

```azurecli
az bicep upgrade
```

## <a name="version"></a>version

`version` コマンドは、インストールされているバージョンを返します。

```azurecli
az bicep version
```

次のコマンドは、バージョン番号を表示します。

```azurecli
Bicep CLI version 0.4.1 (e2387595c9)
```

Bicep CLI がインストールされていない場合は、Bicep CLI が見つからなかったことを示すエラーが表示されます。

## <a name="next-steps"></a>次のステップ

Bicep ファイルのデプロイの詳細については、以下の記事を参照してください。

* [Azure CLI](deploy-cli.md)
* [Cloud Shell](deploy-cloud-shell.md)
* [PowerShell](deploy-powershell.md)
