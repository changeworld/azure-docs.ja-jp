---
title: "Azure コンテナー レジストリ内の Docker イメージ | Microsoft Docs"
description: "Docker CLI を使用した Azure コンテナー レジストリに対する Docker イメージのプッシュとプル"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: dlepow
tags: 
keywords: 
ms.assetid: 64fbe43f-fdde-4c17-a39a-d04f2d6d90a1
ms.service: container-registry
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2016
ms.author: stevelas
translationtype: Human Translation
ms.sourcegitcommit: 2a381431acb6436ddd8e13c69b05423a33cd4fa6
ms.openlocfilehash: 1e70f41dd15b0243fb8edd137710ac555821434e
ms.lasthandoff: 02/22/2017

---
# <a name="push-your-first-image-to-a-container-registry-using-the-docker-cli"></a>Docker CLI を使用したコンテナー レジストリへの最初のイメージのプッシュ
[Docker Hub](https://hub.docker.com/) で公開 Docker イメージを格納するように、Azure コンテナー レジストリではプライベート [Docker](http://hub.docker.com) コンテナー イメージを格納および管理します。 コンテナー レジストリに対する[ログイン](https://docs.docker.com/engine/reference/commandline/login/)、[プッシュ](https://docs.docker.com/engine/reference/commandline/push/)、[プル](https://docs.docker.com/engine/reference/commandline/pull/)などの操作には、[Docker コマンド ライン インターフェイス](https://docs.docker.com/engine/reference/commandline/cli/) (Docker CLI) を使用します。 

背景情報と概念については、「[Azure Container Registry とは](container-registry-intro.md)」を参照してください。


> [!NOTE]
> Container Registry は現在プレビュー段階です。
> 
> 

## <a name="prerequisites"></a>前提条件
* **Azure コンテナー レジストリ** - コンテナー レジストリは、Azure サブスクリプションに作成します。 たとえば、[Azure Portal](container-registry-get-started-portal.md) または [Azure CLI 2.0](container-registry-get-started-azure-cli.md) を使用します。
* **Docker CLI** - ローカル コンピューターを Docker ホストとして設定し、Docker CLI コマンドにアクセスするには、[Docker エンジン](https://docs.docker.com/engine/installation/)をインストールします。

## <a name="log-in-to-a-registry"></a>レジストリへのログイン
[レジストリの資格情報](container-registry-authentication.md)を使用してコンテナー レジストリにログインするには、`docker login` を実行します。

次の例では、Azure Active Directory [サービス プリンシパル](../active-directory/active-directory-application-objects.md)の ID とパスワードを渡します。 たとえば、自動化シナリオのために、レジストリにサービス プリンシパルを割り当てることができます。 

```
docker login myregistry-contoso.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

> [!TIP]
> 必ずレジストリの完全修飾名 (すべて小文字) を指定してください。 この例では `myregistry-contoso.azurecr.io` です。

## <a name="steps-to-pull-and-push-an-image"></a>イメージをプルおよびプッシュするための手順
次の例では、Nginx イメージを公開 Docker Hub レジストリからダウンロードし、プライベート Azure コンテナー レジストリにタグ付けして、レジストリにプッシュし、もう一度プルします。

**1.Docker の公式 Nginx イメージをプルする**

まず、ローカル コンピューターに公開 Nginx イメージをプルします。

```
docker pull nginx
```
**2.Nginx コンテナーを起動する**

次のコマンドでは、ローカルの Nginx コンテナーを対話形式で起動し (そのため、Nginx からの出力を確認できます)、ポート 8080 でリッスンします。 実行中のコンテナーは、停止すると削除されます。

```
docker run -it --rm -p 8080:80 nginx
```

ブラウザーで [http://localhost:8080](http://localhost:8080) に移動して、実行中のコンテナーを表示します。 次のような画面が表示されます。

![ローカル コンピューター上の Nginx](./media/container-registry-get-started-docker-cli/nginx.png)

実行中のコンテナーを停止するには、Ctrl + C キーを押します。

**3.レジストリ内のイメージのエイリアスを作成する**

次のコマンドでは、レジストリへの完全修飾パスを使用して、イメージのエイリアスを作成します。 この例では、レジストリのルートが煩雑にならないように、`samples` 名前空間を指定しています。

```
docker tag nginx myregistry-exp.azurecr.io/samples/nginx
```  

**4.レジストリにイメージをプッシュする**

```
docker push myregistry-contoso.azurecr.io/samples/nginx
``` 

**5.レジストリからイメージをプルする**

```
docker pull myregistry-contoso.azurecr.io/samples/nginx
``` 

**6.レジストリから Nginx コンテナーを起動する**

```
docker run -it --rm -p 8080:80 myregistry-exp.azurecr.io/samples/nginx
```

ブラウザーで [http://localhost:8080](http://localhost:8080) に移動して、実行中のコンテナーを表示します。

実行中のコンテナーを停止するには、Ctrl + C キーを押します。

**6.イメージを削除する**

```
docker rmi myregistry-contoso.azurecr.io/samples/nginx
```



## <a name="next-steps"></a>次のステップ
基本を理解したので、レジストリの使用を開始する準備ができました。 たとえば、[Azure Container Service](https://azure.microsoft.com/documentation/services/container-service/) クラスターへのコンテナー イメージのデプロイを開始できます。




