---
title: "Azure Container Registry のベスト プラクティス"
description: "ベスト プラクティスに従って Azure Container Registry を効果的に使う方法を説明します。"
services: container-registry
author: mmacy
manager: timlt
ms.service: container-registry
ms.topic: quickstart
ms.date: 11/05/2017
ms.author: marsma
ms.openlocfilehash: 5ccbb3022dc38f13eed9b5aa24beb14dfdb3b5b6
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2017
---
# <a name="best-practices-for-azure-container-registry"></a>Azure Container Registry のベスト プラクティス

以下のベスト プラクティスに従うことにより、Azure のプライベート Docker レジストリのパフォーマンスを最大限に引き出し、運用のコスト効率を高めることができます。

## <a name="network-close-deployment"></a>ネットワーク的に近接する場所へのデプロイ

コンテナー レジストリは、コンテナーのデプロイ先と同じ Azure リージョンに作成してください。 ご利用のコンテナー ホストとネットワーク的に近接するリージョンにレジストリを配置することで、待ち時間とコストの両方を低減することができます。

ネットワーク的に近接する場所へのデプロイは、プライベート コンテナー レジストリを使用する大きな理由の 1 つです。 Docker イメージは、段階的なデプロイに対応した優れた[レイヤー構造](https://docs.docker.com/engine/userguide/storagedriver/imagesandcontainers/)になっています。 ところが、新しいノードは、特定のイメージに必要なすべてのレイヤーをプルする必要があります。 この初回 `docker pull` で、あっという間に数ギガバイトにまで達することがあります。 プライベート レジストリとデプロイとの距離が短ければ、ネットワーク待ち時間が最小限で済みます。
また、Azure を含むすべてのパブリック クラウドには、ネットワーク送信データ転送料金が導入されています。 異なるデータセンターからイメージを取得すると、待ち時間に加えて、ネットワーク送信データ転送料金が別途発生します。

## <a name="geo-replicate-multi-region-deployments"></a>Geo レプリケーション転送を使用した複数リージョンへのデプロイ

複数のリージョンにコンテナーをデプロイする場合は、Azure Container Registry の [Geo レプリケーション](container-registry-geo-replication.md)機能をご使用ください。 ローカル データ センターから世界各国の顧客にサービスを提供する場合であれ、開発チームの拠点が分散している場合であれ、レジストリを Geo レプリケーション転送することにより、レジストリの管理を省力化し、待ち時間を最小限に抑えることができます。 この機能は現在プレビュー段階です。[Premium](container-registry-skus.md#premium) レジストリでご利用いただけます。

Geo レプリケーションの使用方法については、[Azure Container Registry の Geo レプリケーション](container-registry-tutorial-prepare-registry.md)に関する 3 部構成のチュートリアルを参照してください。

## <a name="repository-namespaces"></a>リポジトリの名前空間

リポジトリの名前空間を有効活用することで、1 つのレジストリを組織内の複数のグループで共有することができます。 デプロイやチームの境界を越えてレジストリを共有することができます。 Azure Container Registry では、名前空間を入れ子にしてグループ分けすることができます。

たとえば、以下のようなコンテナー イメージ タグを考えてみましょう。 ルート名前空間には全社的に使用されるイメージ (`aspnetcore` など) を置き、Production グループと Marketing グループが所有するコンテナー イメージには、それぞれ固有の名前空間を使用します。

```
contoso.azurecr.io/aspnetcore:2.0
contoso.azurecr.io/products/widget/web:1
contoso.azurecr.io/products/bettermousetrap/refundapi:12.3
contoso.azurecr.io/marketing/2017-fall/concertpromotions/campaign:218.42
```

## <a name="dedicated-resource-group"></a>専用のリソース グループ

コンテナー レジストリは、複数のコンテナー ホスト間で使用されるリソースであるため、専用のリソース グループに置くことをお勧めします。

特定のホスト タイプ (Azure Container Instances など) を試験的に使用することもあるかと思いますが、作業後はコンテナー インスタンスを削除するのが普通です。 しかし、Azure Container Registry にプッシュした一連のイメージを保存しておきたいこともあるでしょう。 レジストリを専用のリソース グループに置いておけば、コンテナー インスタンス リソース グループを削除するときに、レジストリ内の一連のイメージを不注意で削除するリスクを極力抑えることができます。

## <a name="authentication"></a>認証

Azure Container Registry に対して認証を行うときのシナリオは、個人の認証とサービス ("ヘッドレス") 認証の 2 つに大別されます。 次の表は、それらのシナリオの概要とそれぞれに推奨される認証方法をまとめたものです。

| 型 | サンプル シナリオ | 推奨される方法 |
|---|---|---|
| 個人 ID | 開発者が、その開発マシンにイメージをプルしたり、開発マシンからイメージをプッシュしたりする。 | [az acr login](/cli/azure/acr?view=azure-cli-latest#az_acr_login) |
| ヘッドレス/サービス ID | ユーザーの直接介入を伴わないビルドとデプロイのパイプライン。 | [サービス プリンシパル](container-registry-authentication.md#service-principal) |

Azure Container Registry の認証について詳しくは、「[Azure コンテナー レジストリによる認証](container-registry-authentication.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ

Azure Container Registry には、いくつかのレベル (SKU) があり、その機能はレベルごとに異なります。 SKU のバリエーションについて詳しくは、「[Azure Container Registry SKU](container-registry-skus.md)」をご覧ください。