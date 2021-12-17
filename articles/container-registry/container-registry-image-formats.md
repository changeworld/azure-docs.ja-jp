---
title: サポートされているコンテンツの形式
description: Docker と互換性のあるコンテナー イメージ、Helm チャート、OCI イメージ、OCI 成果物など、Azure Container Registry でサポートされているコンテンツ形式について説明します。
ms.topic: article
ms.date: 03/02/2021
ms.openlocfilehash: 1203bc8cbebfd59ef28f03d19e939b73d707a3f3
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114438884"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Azure Container Registry 内でサポートされているコンテンツの形式

Azure Container Registry 内のプライベート リポジトリを使用して、次のコンテンツ形式の 1 つを管理できます。 

## <a name="docker-compatible-container-images"></a>Docker と互換性のあるコンテナー イメージ

以下の Docker コンテナー イメージの形式がサポートされています。

* [Docker Image Manifest V2、Schema 1](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [Docker Image Manifest V2、Schema 2](https://docs.docker.com/registry/spec/manifest-v2-2/) - 1 つの `image:tag` 参照の下に[マルチアーキテクチャ イメージ](push-multi-architecture-images.md)を格納することをレジストリに許可するマニフェスト一覧が含まれます

## <a name="oci-images"></a>OCI イメージ

Azure Container Registry では、[Open Container Initiative (OCI) イメージ形式の仕様](https://github.com/opencontainers/image-spec/blob/master/spec.md) (省略可能な[イメージ インデックス](https://github.com/opencontainers/image-spec/blob/master/image-index.md)仕様を含む) に準拠するイメージをサポートしています。 パッケージ形式には、[Singularity Image Format (SIF)](https://github.com/sylabs/sif) が含まれています。

## <a name="oci-artifacts"></a>OCI 成果物

Azure Container Registry では、コンテナー イメージおよびその他のコンテンツ タイプ (成果物) の格納、共有、セキュリティ保護およびデプロイに、ベンダー中立かつクラウドを問わない仕様である、[OCI Distribution Specification](https://github.com/opencontainers/distribution-spec) をサポートしています。 この仕様では、コンテナー イメージに加え、広範な成果物をレジストリに格納できます。 成果物のプッシュおよびプルには、その成果物に適したツールを使用します。 例については、参照してください。

* [Azure コンテナー レジストリを使用して OCI 成果物をプッシュおよびプルする](container-registry-oci-artifacts.md)
* [Azure コンテナー レジストリに対する Helm グラフのプッシュおよびプル](container-registry-helm-repos.md)

OCI 成果物の詳細については、GitHub 上の[ストレージとしての OCI レジストリ (ORAS)](https://github.com/deislabs/oras) リポジトリおよび [OCI 成果物](https://github.com/opencontainers/artifacts)リポジトリの記事を参照してください。

## <a name="helm-charts"></a>Helm チャート

Azure Container Registry では、Kubernetes 用のアプリケーションをすばやく管理およびデプロイするために使用されるパッケージ形式である [Helm チャート](https://helm.sh/)のリポジトリをホストすることができます。 [Helm クライアント](https://docs.helm.sh/using_helm/#installing-helm)のバージョン 3 をお勧めします。 「[Azure コンテナー レジストリに対する Helm グラフのプッシュおよびプル](container-registry-helm-repos.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ

* Azure Container Registry を使用してイメージを[プッシュおよびプル](container-registry-get-started-docker-cli.md)する方法を確認します。

* [ACR タスク](container-registry-tasks-overview.md)を使用して、コンテナー イメージのビルドとテストを行います。 

* [Moby BuildKit](https://github.com/moby/buildkit) を使用して、OCI 形式のコンテナーを作成およびパッケージ化します。

* Azure Container Registry にホストされる [Helm リポジトリ](container-registry-helm-repos.md)を設定します。 


