---
title: "クイックスタート - Azure Portal を使用した Azure のプライベート Docker レジストリの作成"
description: "Azure Portal を使用してプライベート Docker コンテナー レジストリを作成する方法を簡単に説明します。"
services: container-registry
author: mmacy
manager: timlt
ms.service: container-registry
ms.topic: quickstart
ms.date: 12/06/2017
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: eaf935c1060e53673351936111083d8bb44f05e7
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2018
---
# <a name="create-a-container-registry-using-the-azure-portal"></a>Azure Portal を使用したコンテナー レジストリの作成

Azure Container Registry は、プライベート Docker コンテナー イメージを保存および管理する Azure のプライベート Docker レジストリです。 このクイックスタートでは、Azure Portal を使用してコンテナー レジストリを作成します。

このクイック スタートを完了するには、Docker をローカルにインストールする必要があります。 Docker では、[Mac][docker-mac]、[Windows][docker-windows]、または [Linux][docker-linux] システムで Docker を簡単に構成できるパッケージが提供されています。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal (https://portal.azure.com) にサインインします。

## <a name="create-a-container-registry"></a>コンテナー レジストリの作成

**[新規作成]** > **[コンテナー]** > **[Azure Container Registry]** を選択します。

![Azure Portal でコンテナー レジストリを作成する][qs-portal-01]

**[レジストリ名]** と **[リソース グループ]** に値を入力します。 レジストリの名前は Azure 内で一意にする必要があります。また、5 ～ 50 文字の英数字を含める必要があります。 `myResourceGroup` というリソース グループを作成し、**[SKU]** に [Basic] を選択します。 **[作成]** を選択して ACR インスタンスをデプロイします。

![Azure Portal でコンテナー レジストリを作成する][qs-portal-03]

このクイックスタートでは、*基本*のレジストリを作成します。 次の表で簡単に説明されているように、Azure Container Registry はいくつかの SKU で使用できます。 それぞれの詳細については、「[Azure Container Registry SKU][container-registry-skus]」を参照してください。

[!INCLUDE [container-registry-sku-matrix](../../includes/container-registry-sku-matrix.md)]

**"デプロイメントに成功しました"** というメッセージが表示されたら、ポータルでコンテナー レジストリを選択し、**[アクセス キー]** を選択します。

![Azure Portal でコンテナー レジストリを作成する][qs-portal-05]

**[管理者ユーザー]** の **[有効にする]** を選択します。 次の値をメモします。

* ログイン サーバー
* ユーザー名
* password

Docker CLI を使用してレジストリを使用する場合は、次の手順で以下の値を使用します。

![Azure Portal でコンテナー レジストリを作成する][qs-portal-06]

## <a name="log-in-to-acr"></a>ACR にログインする

コンテナー イメージをプッシュしたりプルしたりするには、あらかじめ ACR インスタンスにログインしておく必要があります。 ログインするには、[docker login][docker-login] コマンドを使用します。 *username*、*password*、*login server* の値を前述の手順でメモした値で置き換えます。

```bash
docker login --username <username> --password <password> <login server>
```

このコマンドは、完了すると `Login Succeeded` を返します。 `--password-stdin` パラメーターの使用を推奨するセキュリティ警告が表示されることもあります。 このパラメーターの使用について、ここでは説明していませんが、このベスト プラクティスに従うことをお勧めします。 詳細については、[docker login][docker-login] コマンドのリファレンスを参照してください。

## <a name="push-image-to-acr"></a>ACR にイメージをプッシュする

Azure Container Registry にイメージをプッシュするには、まずイメージを用意する必要があります。 必要な場合は、次のコマンドを実行して、既存のイメージを Docker Hub からプルします。

```bash
docker pull microsoft/aci-helloworld
```

イメージをレジストリにプッシュする前に、イメージに ACR ログイン サーバー名を使用してタグを付ける必要があります。 [docker tag][docker-tag] コマンドを使用してイメージにタグ付けします。 *login server* を前述の手順でメモしたログイン サーバー名で置き換えます。

```
docker tag microsoft/aci-helloworld <login server>/aci-helloworld:v1
```

最後に、[docker push][docker-push] を使用して、ACR インスタンスにイメージをプッシュします。 *login server* を ACR インスタンスのログイン サーバー名で置き換えます。

```
docker push <login server>/aci-helloworld:v1
```

`docker push` コマンドが成功すると、次のように出力されます。

```
The push refers to a repository [uniqueregistryname.azurecr.io/aci-helloworld]
7c701b1aeecd: Pushed
c4332f071aa2: Pushed
0607e25cc175: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:f2867748615cc327d31c68b1172cc03c0544432717c4d2ba2c1c2d34b18c62ba size: 1577
```

## <a name="list-container-images"></a>コンテナー イメージの一覧表示

ACR インスタンスのイメージ一覧を表示するには、ポータルでレジストリに移動し、**[リポジトリ]** を選択してから、`docker push` で作成したリポジトリを選択します。

この例では、**aci-helloworld** リポジトリを選択したので、**[タグ]** に `v1` タグが付いたイメージが表示されます。

![Azure Portal でコンテナー レジストリを作成する][qs-portal-09]

## <a name="clean-up-resources"></a>リソースのクリーンアップ

不要になったら、**myResourceGroup** リソース グループを削除します。 リソース グループを削除すると、リソース グループだけでなく、ACR インスタンスとすべてのコンテナー イメージも削除されます。

![Azure Portal でコンテナー レジストリを作成する][qs-portal-08]

## <a name="next-steps"></a>次の手順

このクイック スタートでは、Azure Portal を使用して Azure Container Registry を作成しました。 Azure Container Instances と一緒に Azure Container Registry を使用する場合は、Azure Container Instances のチュートリアルに進みます。

> [!div class="nextstepaction"]
> [Azure Container Instances のチュートリアル][container-instances-tutorial-prepare-app]

<!-- IMAGES -->
[qs-portal-01]: ./media/container-registry-get-started-portal/qs-portal-01.png
[qs-portal-02]: ./media/container-registry-get-started-portal/qs-portal-02.png
[qs-portal-03]: ./media/container-registry-get-started-portal/qs-portal-03.png
[qs-portal-04]: ./media/container-registry-get-started-portal/qs-portal-04.png
[qs-portal-05]: ./media/container-registry-get-started-portal/qs-portal-05.png
[qs-portal-06]: ./media/container-registry-get-started-portal/qs-portal-06.png
[qs-portal-07]: ./media/container-registry-get-started-portal/qs-portal-07.png
[qs-portal-08]: ./media/container-registry-get-started-portal/qs-portal-08.png
[qs-portal-09]: ./media/container-registry-get-started-portal/qs-portal-09.png

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[container-instances-tutorial-prepare-app]: ../container-instances/container-instances-tutorial-prepare-app.md
[container-registry-skus]: container-registry-skus.md
