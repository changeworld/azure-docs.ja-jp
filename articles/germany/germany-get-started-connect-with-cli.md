---
title: Azure CLI を使用して Azure Germany に接続する | Microsoft Docs
description: Azure CLI を使用して Azure Germany でサブスクリプションを管理する方法
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfdocs, devx-track-azurecli
ms.openlocfilehash: b5fb2f9b55fbe949e2c171ee7bea989c1741ab5d
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/21/2020
ms.locfileid: "117029116"
---
# <a name="connect-to-azure-germany-by-using-azure-cli"></a>Azure CLI を使用して Azure Germany に接続する

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

Azure コマンド ライン インターフェイス (Azure CLI) を使用するには、グローバル Azure ではなく Azure Germany に接続する必要があります。 Azure CLI を使用すると、スクリプトで大規模なサブスクリプションを管理したり、現在 Azure portal にはない機能を利用したりできます。 グローバル Azure で Azure CLI を使用した経験をお持ちであれば、操作の大部分は同じです。  

## <a name="azure-cli"></a>Azure CLI
[Azure CLI をインストール](/cli/azure/install-az-cli2)する方法はいくつかあります。  

Azure Germany に接続するには、クラウドを設定します。

```azurecli
az cloud set --name AzureGermanCloud
```

クラウドの設定後、ログインできます。

```azurecli
az login --username your-user-name@your-tenant.onmicrosoft.de
```

クラウドが AzureGermanCloud に正しく設定されていることを確認するには、次のコマンドのいずれかを実行し、AzureGermanCloud 項目の `isActive` フラグが `true` に設定されていることを確認します。

```azurecli
az cloud list
```

```azurecli
az cloud list --output table
```

## <a name="azure-classic-cli"></a>Azure クラシック CLI
[Azure クラシック CLI をインストールする](/cli/azure/install-azure-cli)方法は複数あります。 既に Node がインストールされている場合は、npm パッケージをインストールするのが最も簡単です。

CLI を npm パッケージからインストールするには、[最新の Node.js と npm](https://nodejs.org/en/download/package-manager/) をダウンロードし、インストールしていることを確認してください。 次に、**npm install** を実行して、**azure-cli** パッケージをインストールします。

```bash
npm install -g azure-cli
```

Linux ディストリビューションの場合、**npm** コマンドを正常に実行するには、次のように **sudo** の使用が必要になる場合があります。

```bash
sudo npm install -g azure-cli
```

> [!NOTE]
> Node.js と npm を Linux ディストリビューションまたは OS にインストールまたは更新する必要がある場合は、最新の Node.js LTS バージョン (4.x) をインストールすることをお勧めします。 以前のバージョンを使用すると、インストール エラーが発生する場合があります。


Azure CLI のインストール後、Azure Germany にログインします。

```console
azure login --username your-user-name@your-tenant.onmicrosoft.de  --environment AzureGermanCloud
```

ログインした後は、通常どおりに Azure CLI コマンドを実行できます。

```console
azure webapp list my-resource-group
```

## <a name="next-steps"></a>次のステップ
Azure Germany への接続の詳細については、以下のリソースを参照してください。

* [PowerShell を使用して Azure Germany に接続する](./germany-get-started-connect-with-ps.md)
* [Visual Studio を使用して Azure Germany に接続する](./germany-get-started-connect-with-vs.md)
* [Azure portal を使用して Azure Germany に接続する](./germany-get-started-connect-with-portal.md)