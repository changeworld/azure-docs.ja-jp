---
author: sabbour
ms.author: asabbour
ms.date: 4/5/2020
ms.openlocfilehash: 1fded0ad08af4b1e5d915e32e09087c1a78bd318
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520559"
---
### <a name="create-the-cluster"></a>クラスターを作成する

チュートリアルに従って、[Azure Red Hat OpenShift クラスターを作成](../tutorial-create-cluster.md)します。 CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.6.0 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

### <a name="connect-to-the-cluster"></a>クラスターに接続する

Azure Red Hat OpenShift クラスターを管理するには、[oc](https://docs.openshift.com/container-platform/4.7/cli_reference/openshift_cli/getting-started-cli.html) (OpenShift コマンドライン クライアント) を使用します。

> [!NOTE]
> [OpenShift コマンド ライン](../tutorial-connect-cluster.md)を[Azure Cloud Shell](https://shell.azure.com/) にインストールし、以下のすべてのコマンドライン操作に使用することをお勧めします。 shell.azure.com から、または次のリンクをクリックして、シェルを起動します。
>
> [![埋め込みの起動](https://docs.microsoft.com/azure/includes/media/cloud-shell-try-it/hdi-launch-cloud-shell.png "Azure Cloud Shell を起動する")](https://shell.azure.com/bash)

チュートリアルに従って CLI をインストールし、クラスターの資格情報を取得して、Web コンソールと OpenShift CLI を使用して[クラスターに接続](../tutorial-connect-cluster.md)します。

ログインすると、`default` プロジェクトを使用しているというメッセージが表示されます。

```output
Login successful.

You have access to 61 projects, the list has been suppressed. You can list all projects with 'oc projects'

Using project "default".
```
