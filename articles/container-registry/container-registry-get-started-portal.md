---
title: クイック スタート - Azure でのプライベート Docker レジストリの作成 - Azure portal
description: Azure Portal を使用してプライベート Docker コンテナー レジストリを作成する方法を簡単に説明します。
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: quickstart
ms.date: 11/06/2018
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 865c53fdda60f6a0384157ec68042b4b8b243a7a
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/11/2018
ms.locfileid: "53255365"
---
# <a name="quickstart-create-a-private-container-registry-using-the-azure-portal"></a>クイック スタート:Azure portal を使用したプライベート コンテナー レジストリの作成

Azure Container Registry は、プライベート Docker コンテナー イメージを保存および管理する Azure のプライベート Docker レジストリです。 このクイック スタートでは、Azure Portal を使ってコンテナー レジストリを作成し、そのレジストリにコンテナー イメージをプッシュして、最後に Azure Container Instances (ACI) に対してレジストリからコンテナーをデプロイします。

このクイック スタートを完了するには、Docker をローカルにインストールする必要があります。 Docker では、[Mac][docker-mac]、[Windows][docker-windows]、または [Linux][docker-linux] システムで Docker を簡単に構成できるパッケージが提供されています。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ( https://portal.azure.com ) にサインインします。

## <a name="create-a-container-registry"></a>コンテナー レジストリの作成

**[リソースの作成]** > **[コンテナー]** > **[コンテナー レジストリ]** の順に選択します。

![Azure Portal でコンテナー レジストリを作成する][qs-portal-01]

**[レジストリ名]** と **[リソース グループ]** に値を入力します。 レジストリの名前は Azure 内で一意にする必要があります。また、5 ～ 50 文字の英数字を含める必要があります。 このクイック スタートでは、`myResourceGroup` という名前の新しいリソース グループを `West US` に作成し、**[SKU]** には [Basic] を選択します。 **[作成]** を選択して ACR インスタンスをデプロイします。

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

イメージをレジストリにプッシュする前に、イメージに ACR ログイン サーバー名を使用してタグを付ける必要があります。 [docker tag][docker-tag] コマンドを使用してイメージにタグ付けします。 *login server* を前述の手順でメモしたログイン サーバー名で置き換えます。 *repository name* には、リポジトリ内のイメージの配置先 (**`myrepo`** など) を追加します。

```bash
docker tag microsoft/aci-helloworld <login server>/<repository name>/aci-helloworld:v1
```

最後に、[docker push][docker-push] を使用して、ACR インスタンスにイメージをプッシュします。 *login server* は、ACR インスタンスのログイン サーバー名に、また、*repository name* は、前のコマンドで使用したリポジトリの名前に置き換えます。

```bash
docker push <login server>/<repository name>/aci-helloworld:v1
```

`docker push` コマンドが成功すると、次のように出力されます。

```
The push refers to repository [specificregistryname.azurecr.io/myrepo/aci-helloworld]
31ba1ebd9cf5: Pushed
cd07853fe8be: Pushed
73f25249687f: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:565dba8ce20ca1a311c2d9485089d7ddc935dd50140510050345a1b0ea4ffa6e size: 1576
```

## <a name="list-container-images"></a>コンテナー イメージの一覧表示

ACR インスタンスのイメージ一覧を表示するには、ポータルでレジストリに移動し、**[リポジトリ]** を選択してから、`docker push` で作成したリポジトリを選択します。

この例では、**aci-helloworld** リポジトリを選択したので、**[タグ]** に `v1` タグが付いたイメージが表示されます。

![Azure Portal でコンテナー レジストリを作成する][qs-portal-09]

## <a name="deploy-image-to-aci"></a>ACI へのイメージのデプロイ

レジストリからインスタンスをデプロイするためには、リポジトリ (aci-helloworld) に移動して、v1 の横にある省略記号をクリックする必要があります。

![Azure Container Instances をポータルから起動][qs-portal-10]

コンテキスト メニューが表示されたら **[実行インスタンス]** を選択します。

![ACI を起動するためのコンテキスト メニュー][qs-portal-11]

**[コンテナー名]** を入力し、サブスクリプションが正しく選択されていることを確認して、既存の**リソース グループ**の "myResourceGroup" を選択します。 "パブリック IP アドレス" オプションを **[はい]** に設定して有効にし、**[OK]** をクリックして Azure Container Instances を起動します。

![ACI のデプロイ開始オプション][qs-portal-12]

デプロイが開始すると、デプロイの進捗状況を示すタイルがポータル ダッシュボードに配置されます。 デプロイが完了するとタイルが更新され、新しい **mycontainer** コンテナー グループが表示されます。

![ACI のデプロイ状態][qs-portal-13]

mycontainer コンテナー グループを選択して、コンテナー グループのプロパティを表示します。 コンテナー グループの **[IP address]\(IP アドレス\)** と、コンテナーの **[STATUS]\(状態\)** をメモします。

![ACI コンテナーの詳細][qs-portal-14]

## <a name="view-the-application"></a>アプリケーションを表示する

コンテナーが **[Running]\(稼働中\)** 状態になったら、前のステップでメモした IP アドレスに移動してアプリケーションを表示します。

![ブラウザーでの Hello World アプリ][qs-portal-15]

## <a name="clean-up-resources"></a>リソースのクリーンアップ

リソースをクリーンアップするには、ポータルの **myResourceGroup** リソース グループに移動します。 リソース グループが読み込まれたら、**[リソース グループの削除]** をクリックして、リソース グループ、Azure Container Registry、およびすべての Azure Container Instances を削除します。

![Azure portal でリソース グループを削除する][qs-portal-08]

## <a name="next-steps"></a>次の手順

このクイック スタートでは、Azure CLI を使って Azure Container Registry を作成し、そのインスタンスを Azure Container Instances で起動しました。 Azure Container Instances のチュートリアルに進んで、ACI についての理解を深めましょう。

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
[qs-portal-10]: ./media/container-registry-get-started-portal/qs-portal-10.png
[qs-portal-11]: ./media/container-registry-get-started-portal/qs-portal-11.png
[qs-portal-12]: ./media/container-registry-get-started-portal/qs-portal-12.png
[qs-portal-13]: ./media/container-registry-get-started-portal/qs-portal-13.png
[qs-portal-14]: ./media/container-registry-get-started-portal/qs-portal-14.png
[qs-portal-15]: ./media/container-registry-get-started-portal/qs-portal-15.png

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
