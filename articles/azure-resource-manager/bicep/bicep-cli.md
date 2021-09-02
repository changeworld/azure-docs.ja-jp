---
title: Bicep CLI コマンドと概要
description: Bicep CLI で使用できるコマンドについて説明します。 これらのコマンドには、Bicep からの Azure Resource Manager テンプレートの作成が含まれます。
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: dd1f292d4ce60353d2f8cecaaa83e38b26bdfaa3
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/06/2021
ms.locfileid: "111540803"
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
