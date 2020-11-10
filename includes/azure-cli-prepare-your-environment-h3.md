---
ms.topic: include
ms.date: 09/10/2020
author: dbradish-microsoft
ms.author: dbradish
manager: barbkess
ms.technology: azure-cli
ms.service: azure-cli
ms.devlang: azurecli
ms.custom: devx-track-azurecli
ms.openlocfilehash: 923eee619fdff7b128d9429f38cf713803cc6a13
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2020
ms.locfileid: "93347396"
---
### <a name="prepare-your-environment-for-the-azure-cli"></a>Azure CLI の環境を準備する

- Bash 環境で [Azure Cloud Shell](/azure/cloud-shell/quickstart) を使用します。

   [![埋め込みの起動](https://shell.azure.com/images/launchcloudshell.png "Azure Cloud Shell を起動する")](https://shell.azure.com)   
- 必要に応じて、Azure CLI を[インストール](/cli/azure/install-azure-cli)して、CLI リファレンス コマンドを実行します。
   - ローカル インストールを使用する場合は、[az login](/cli/azure/reference-index#az-login) コマンドを使用して Azure CLI でサインインします。  認証プロセスを完了するには、ターミナルに表示される手順に従います。  追加のサインイン オプションについては、「[Azure CLI を使用してサインインする](/cli/azure/authenticate-azure-cli)」を参照してください。
  - 初回使用時にインストールを求められたら、Azure CLI 拡張機能をインストールします。  拡張機能の詳細については、「[Azure CLI で拡張機能を使用する](/cli/azure/azure-cli-extensions-overview)」を参照してください。
  - [az version](/cli/azure/reference-index?#az_version) を実行し、インストールされているバージョンおよび依存ライブラリを検索します。 最新バージョンにアップグレードするには、[az upgrade](/cli/azure/reference-index?#az_upgrade) を実行します。

