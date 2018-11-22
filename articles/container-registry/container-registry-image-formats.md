---
title: Azure Container Registry のコンテンツの形式
description: Azure Container Registry 内でサポートされているコンテンツの形式について説明します。
services: container-registry
author: dlepow
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 10/31/2018
ms.author: danlep
ms.openlocfilehash: e7155604339bc634078fd022e05ede5f902bc0d8
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/14/2018
ms.locfileid: "51634846"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Azure Container Registry 内でサポートされているコンテンツの形式

Azure Container Registry 内のプライベート リポジトリを使用して、次のコンテンツ形式の 1 つを管理できます。 

## <a name="docker-compatible-container-images"></a>Docker と互換性のあるコンテナー イメージ

* [Docker Image Manifest V2、Schema 1](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [Docker Image Manifest V2、Schema 2](https://docs.docker.com/registry/spec/manifest-v2-2/) - 1 つの "image:tag" 参照の下にマルチプラットフォーム イメージを格納することをレジストリに許可するマニフェスト一覧が含まれます

* [Open Container Initiative (OCI) のイメージ形式の仕様](https://github.com/opencontainers/image-spec/blob/master/spec.md) 


## <a name="helm-charts"></a>Helm チャート

Azure Container Registry には、Kubernetes 用のアプリケーションをすばやく管理およびデプロイするために使用されるパッケージ形式である [Helm チャート](https://helm.sh/)のリポジトリをホストすることもできます。 [Helm クライアント](https://docs.helm.sh/using_helm/#installing-helm) バージョン 2.11.0 以降がサポートされています。

## <a name="next-steps"></a>次の手順

* Azure Container Registry を使用してイメージを[プッシュおよびプル](container-registry-get-started-docker-cli.md)する方法を確認します。

* [ACR タスク](container-registry-tasks-overview.md)を使用して、コンテナー イメージのビルドとテストを行います。 

* [Moby BuildKit](https://github.com/moby/buildkit) を使用して、OCI 形式のコンテナーを作成およびパッケージ化します。

* Azure Container Registry にホストされる [Helm リポジトリ](container-registry-helm-repos.md)を設定します。 


