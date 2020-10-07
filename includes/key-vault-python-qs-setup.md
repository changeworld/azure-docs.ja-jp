---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 09/03/2020
ms.author: msmbaldwin
ms.openlocfilehash: 9440a33858cb70389e86621ae76be9c08ec56d61
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2020
ms.locfileid: "89482134"
---
1. [アクティブなサブスクリプションが含まれる Azure アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)があることを確認します。

1. [Python 2.7 以降または Python 3.5.3 以降](https://www.python.org/downloads)をインストールします。

1. [Azure CLI](/cli/azure/install-azure-cli) をインストールします。

1. [ローカル開発の認証の構成](/azure/developer/python/configure-local-development-environment?tabs=cmd#configure-authentication)に関する記事に記載されている手順に従い、ローカルのサービス プリンシパルを作成し、開発変数を使用して、Python 用の Azure Key Vault クライアントで使用できるようにします。 

    Azure でコードを直接実行する場合に、アプリでマネージド ID が使用されている場合、別のサービス プリンシパルは必要ありません。

1. ターミナルまたはコマンド プロンプトで、適切なプロジェクト フォルダーを作成したら、「[Python 仮想環境を使用する](/azure/developer/python/configure-local-development-environment?tabs=cmd#use-python-virtual-environments)」で説明されているように、Python 仮想環境を作成し、アクティブ化します。

1. Azure Active Directory ID ライブラリをインストールします。

    ```terminal
    pip install azure.identity
    ```
