---
title: "Azure Container Registry のチュートリアル - 更新されたイメージのリージョン デプロイへのプッシュ"
description: "変更された Docker イメージを Geo レプリケートされた Azure コンテナー レジストリにプッシュした後、それらの変更が複数のリージョンで実行されている Web アプリに自動的にデプロイされたことを確認します。 3 部構成のシリーズの第 3 部。"
services: container-registry
documentationcenter: 
author: mmacy
manager: timlt
editor: mmacy
tags: acr, azure-container-registry, geo-replication
keywords: "Docker、コンテナー、レジストリ、Azure"
ms.service: container-registry
ms.devlang: 
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: marsma
ms.custom: 
ms.openlocfilehash: 05c5149ed6c8502c31539f31bfff046f98dc633d
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2017
---
# <a name="push-an-updated-image-to-regional-deployments"></a>更新されたイメージのリージョン デプロイへのプッシュ

これは 3 部構成のチュートリアル シリーズの第 3 部です。 [前のチュートリアル](container-registry-tutorial-deploy-app.md)では、Geo レプリケーションは 2 つの異なるリージョン Web アプリ デプロイに対して構成されました。 このチュートリアルでは、まずアプリケーションを変更してから、新しいコンテナー イメージを構築し、それを Geo レプリケートされたレジストリにプッシュします。 最後に、両方の Web アプリ インスタンスで、Azure Container Registry Webhook によって自動的にデプロイされた変更を表示します。

このチュートリアルは、シリーズの最後の部分です。

> [!div class="checklist"]
> * Web アプリケーション HTML を変更する
> * Docker イメージを構築してタグ付けする
> * 変更を Azure Container Registry にプッシュする
> * 2 つの異なるリージョン内の更新されたアプリを表示する

2 つの *Web App for Containers* リージョン デプロイをまだ構成していない場合は、シリーズ内の前のチュートリアルに戻り、[Azure Container Registry から Web アプリをデプロイします](container-registry-tutorial-deploy-app.md)。

## <a name="modify-the-web-application"></a>Web アプリケーションを変更する

この手順では、更新されたコンテナー イメージを Azure Container Registry にプッシュしたら明確にわかる変更を Web アプリケーションに加えます。

前のチュートリアルで [GitHub から複製した](container-registry-tutorial-prepare-registry.md#get-application-code)アプリケーション ソース内の `AcrHelloworld/Views/Home/Index.cshtml` ファイルを見つけ、それをお気に入りのテキスト エディターで開きます。 `<img>` の行の上に次の行を追加します。

```html
<h1>MODIFIED</h1>
```

変更された `Index.cshtml` は次のようになります。

```html
@{
    ViewData["Title"] = "Azure Container Registry :: Geo-replication";
}
<h1>MODIFIED</h1>
<img width="700" src="~/images/@ViewData["MAPIMAGE"]" />
<ul>
<li>Registry URL: @ViewData["REGISTRYURL"]</li>
<li>Registry IP: @ViewData["REGISTRYIP"]</li>
<li>HostEntry: @ViewData["HOSTENTRY"]</li>
<li>Region: @ViewData["REGION"]</li>
<li>Map: @ViewData["MAPIMAGE"]</li>
</ul>
```

## <a name="rebuild-the-image"></a>イメージを再構築する

これで Web アプリケーションが更新されたので、そのコンテナー イメージを再構築します。 前と同様に、タグには完全修飾イメージ名 (ログイン サーバー URL を含む) を使用します。

```bash
docker build . -f ./AcrHelloworld/Dockerfile -t <acrName>.azurecr.io/acr-helloworld:v1
```

## <a name="run-the-container-locally"></a>コンテナーをローカルで実行する

Azure Container Registry にデプロイする前に、そのイメージをローカルに実行して構築が成功したことを確認します。

```bash
docker run -d -p 8080:80 <acrName>.azurecr.io/acr-helloworld:v1
```

Web ブラウザーで http://localhost:8080 に移動して、コンテナーが起動して実行中であり、変更が表示されることを確認します。

![ローカルのコンテナー イメージ][local-container-01]

## <a name="push-image-to-azure-container-registry"></a>Azure Container Registry へのイメージのプッシュ

次に、更新された *acr-helloworld* コンテナー イメージを Geo レプリケートされたレジストリにプッシュします。 ここでは、更新されたイメージを*米国西部*と*米国東部*の両方のリージョンにあるレジストリ レプリカにデプロイするための 1 つの `docker push` コマンドを実行します。

```bash
docker push <acrName>.azurecr.io/acr-helloworld:v1
```

出力は次のようになります。

```bash
The push refers to a repository [uniqueregistryname.azurecr.io/acr-helloworld]
c003ed6fc8b8: Pushed
02b11afef3fd: Layer already exists
cf17b6f921be: Layer already exists
c93ae914d31e: Layer already exists
2eea44510cee: Layer already exists
670f809bd6d5: Layer already exists
a75caa09eb1f: Layer already exists
v1: digest: sha256:e44c0956a21c91e1f5f7bc83f23f1de710c798246df1e0e508c0c88025449646 size: 1792
```

## <a name="view-the-webhook-logs"></a>Webhook ログを表示する

イメージがレプリケートされている間、Azure Container Registry Webhook がトリガーされていることを確認できます。

前のチュートリアルでコンテナーを *Web Apps for Containers* にデプロイしたときに作成されたリージョン Webhook を表示するには、Azure Portal のコンテナー レジストリに移動し、**[SERVICES] (サービス)** の **[Webhook]** を選択します。

![Azure Portal のコンテナー レジストリ Webhook][tutorial-portal-01]

各 Webhook を選択すると、その呼び出しと応答の履歴が表示されます。 両方の Webhook のログに**プッシュ** アクションの行が表示されます。 ここで、*米国西部*リージョンにある Webhook のログは、前の手順で `docker push` によってトリガーされた**プッシュ** アクションを示しています。

![Azure Portal のコンテナー レジストリ Webhook のログ (米国西部)][tutorial-portal-02]

## <a name="view-the-updated-web-app"></a>更新された Web アプリを表示する

Webhook は、Web アプリに新しいイメージがレジストリにプッシュされたことを通知します。これにより、更新されたコンテナーが 2 つのリージョン Web アプリに自動的にデプロイされます。

Web ブラウザーで両方のリージョン Web アプリ デプロイに移動することによって、アプリケーションが両方のデプロイで更新されたことを確認します。 各 [App Service の概要] タブの右上で、デプロイされた Web アプリの URL を見つけることができます。

![Azure Portal の [App Service の概要]][tutorial-portal-03]

更新されたアプリケーションを表示するには、[App Service の概要] のリンクを選択します。 *米国西部*で実行されているアプリのビューの例を次に示します。

![米国西部リージョンで実行されている変更された Web アプリのブラウザー ビュー][deployed-app-westus-modified]

ブラウザーで*米国東部*デプロイを表示することによって、更新されたコンテナー イメージがここにもデプロイされたことを確認します。

![米国東部リージョンで実行されている変更された Web アプリのブラウザー ビュー][deployed-app-eastus-modified]

1 つの `docker push` で、両方のリージョン Web アプリ デプロイを更新し、Azure Container Registry はネットワーク上の近い場所にあるリポジトリからコンテナー イメージを提供しました。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Web アプリケーション コンテナーの新しいバージョンを更新し、それを Geo レプリケートされたレジストリにプッシュしました。 Azure Container Registry の Webhook が Web Apps for Containers に更新を通知し、それがレジストリ レプリカからのローカル プルをトリガーしました。

このシリーズの最後のチュートリアルでは、次のことを行いました。

> [!div class="checklist"]
> * Web アプリケーション HTML を更新しました
> * Docker イメージを構築してタグ付けしました
> * 変更を Azure Container Registry にプッシュしました
> * 2 つの異なるリージョン内の更新されたアプリを表示しました

<!-- IMAGES -->
[deployed-app-eastus-modified]: ./media/container-registry-tutorial-deploy-update/deployed-app-eastus-modified.png
[deployed-app-westus-modified]: ./media/container-registry-tutorial-deploy-update/deployed-app-westus-modified.png
[local-container-01]: ./media/container-registry-tutorial-deploy-update/local-container-01.png
[tutorial-portal-01]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-01.png
[tutorial-portal-02]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-02.png
[tutorial-portal-03]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-03.png