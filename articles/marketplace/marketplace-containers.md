---
title: Azure Marketplace でのコンテナー オファーの発行ガイド
description: この記事では、Azure Marketplace でコンテナー オファーを発行するための要件を説明します。
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 09/04/2020
ms.openlocfilehash: c52fabcfc2ff22df2de6dd93f2543d625310baef
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2020
ms.locfileid: "89484343"
---
# <a name="publishing-guide-for-container-offers"></a>コンテナー オファーの発行ガイド

コンテナー オファーは、Azure Marketplace にコンテナー イメージを発行するのに役立ちます。 このガイドでは、このオファーの要件について説明します。 

コンテナー オファーは、Azure Marketplace を通じてデプロイおよび課金されるトランザクション オファーです。 ユーザーに表示されるリスト オプションは、"今すぐ入手する" です。

対象となるソリューションが、Kubernetes ベースの Azure コンテナー サービス インスタンスとして設定される Docker コンテナー イメージであるときは、オファーの種類として "*コンテナー*" を使用します。 

> [!NOTE]
> Kubernetes ベースの Azure コンテナー サービス インスタンスの例は、Azure Kubernetes Service や Azure Container Instances です (Kubernetes ベースのコンテナー ランタイムは Azure の顧客が選択)。  

Microsoft は現在、無料およびライセンス持ち込み (BYOL) のライセンス モデルをサポートしています。

## <a name="container-offer-requirements"></a>コンテナー オファーの要件

| 要件 | 詳細 |  
|:--- |:--- |  
| 請求/メータリング | 無料または BYOL のどちらかの課金モデルをサポートします。<br><br> |  
| Dockerfile からビルドされたイメージ | コンテナー イメージは、Docker イメージの仕様に基づき、Dockerfile からビルドされる必要があります。<br> <br>Docker イメージのビルドに関する詳細については、「[Dockerfile reference](https://docs.docker.com/engine/reference/builder/#usage)」(Dockerfile リファレンス) の「Usage」(使用方法) セクションを参照してください。<br><br> |  
| Azure Container Registry リポジトリでのホスティング | コンテナー イメージは、Azure Container Registry リポジトリでホストされる必要があります。<br> <br>Azure Container Registry の操作の詳細については、[クイック スタート: Azure portal を使用したプライベート コンテナー レジストリの作成](../container-registry/container-registry-get-started-portal.md)に関するページを参照してください。<br><br> |  
| イメージのタグ付け | コンテナー イメージには、少なくとも 1 つのタグを含める必要があります (タグの最大数: 16)。<br><br>イメージへのタグ付けに関する詳細については、[Docker ドキュメント](https://docs.docker.com/engine/reference/commandline/tag) サイトの `docker tag` に関するページを参照してください。<br><br> |  

## <a name="next-steps"></a>次のステップ

まだ行っていない場合は、[Azure Marketplace を使用してクラウド ビジネスを拡大する](https://azuremarketplace.microsoft.com/sell)方法について学習します。

パートナー センターで登録して作業を開始するには、次のようにします。

- [パートナー センターにサインイン](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)し、オファーを作成するか完成させます。
- 詳細については、[Azure コンテナー プランを作成する](./partner-center-portal/create-azure-container-offer.md)方法に関するページを参照してください。
