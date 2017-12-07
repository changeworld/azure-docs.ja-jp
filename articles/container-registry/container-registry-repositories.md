---
title: "Azure Container Registry リポジトリ"
description: "Docker イメージ用に Azure Container Registry リポジトリを使用する方法"
services: container-registry
author: cristy
manager: timlt
ms.service: container-registry
ms.topic: article
ms.date: 03/24/2017
ms.author: cristyg
ms.openlocfilehash: 3321dd1d8bbd1b8232c26491edd8c374df16b813
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2017
---
# <a name="azure-container-registry-repositories"></a>Azure Container Registry リポジトリ

Azure Container Registry を使用すると、リポジトリにコンテナー イメージを保存できます。 リポジトリにイメージを保存することで、別々の環境にイメージ (またはイメージのバージョン) のグループを配置できます。 これらのリポジトリをは、レジストリにイメージをプッシュするときに指定できます。


## <a name="prerequisites"></a>前提条件
* **Azure コンテナー レジストリ** - コンテナー レジストリは、Azure サブスクリプションに作成します。 たとえば、[Azure Portal](container-registry-get-started-portal.md) または [Azure CLI 2.0](container-registry-get-started-azure-cli.md) を使用します。
* **Docker CLI** - ローカル コンピューターを Docker ホストとして設定し、Docker CLI コマンドにアクセスするには、[Docker エンジン](https://docs.docker.com/engine/installation/)をインストールします。
* **イメージのプル** - イメージを公開 Docker Hub レジストリからプルし、タグ付けして、レジストリにプッシュします。 イメージをプッシュおよびプルする方法のガイダンスについては、[Azure プライベート レジストリへの Docker イメージのプッシュ](container-registry-get-started-docker-cli.md)に関する記事をご覧ください。


## <a name="viewing-repositories-in-the-portal"></a>Portal でのリポジトリの表示

コンテナー レジストリにイメージをプッシュすると、イメージをホストしているリポジトリの一覧を Azure Portal に表示できます。

[Azure プライベート レジストリへの Docker イメージのプッシュ](container-registry-get-started-docker-cli.md)に関する記事の手順に従う場合、ここで Nginx イメージをコンテナー レジストリに配置する必要があります。 この手順の一環として、イメージの名前空間を指定しておく必要があります。 次の例のコマンドは、"samples" リポジトリに Nginx イメージをプッシュします。

```
docker push myregistry.azurecr.io/samples/nginx
```
 Azure Container Registry では、複数レベルのリポジトリ名前空間をサポートしています。 この機能により、特定のアプリに関連するイメージのコレクション (つまり、アプリのコレクション) を特定の開発チームや運用チーム別にグループ化できます。 コンテナー レジストリ内のリポジトリの詳細については、[Azure でのプライベート Docker コンテナー レジストリ](container-registry-intro.md)に関する記事をご覧ください。

コンテナー レジストリ リポジトリを表示するには

1. Azure ポータルにログインする
2. **[Azure Container Registry]** ブレードで、調べるレジストリを選択します。
3. レジストリ ブレードで、**[リポジトリ]** をクリックして、すべてのリポジトリの一覧とそのイメージを表示します。
4. (省略可能) タグを表示するには、特定のイメージを選択します。

![Portal でのリポジトリ](./media/container-registry-repositories/container-registry-repositories.png)


## <a name="next-steps"></a>次のステップ
基本を理解したので、レジストリの使用を開始する準備ができました。 たとえば、[Azure Container Service](https://azure.microsoft.com/documentation/services/container-service/) クラスターへのコンテナー イメージのデプロイを開始できます。
