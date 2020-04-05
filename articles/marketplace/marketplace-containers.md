---
title: Azure Marketplace 用の Containers のオファー発行ガイド
description: この記事では、Marketplace で Containers を発行するための要件を説明します
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: dsindona
ms.openlocfilehash: 882d428d7a8a9b56408aaa68964fa1e36c7cc7c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80277308"
---
# <a name="containers-offer-publishing-guide"></a>Containers のオファー発行ガイド

コンテナーのオファーは、Azure Marketplace にコンテナー イメージを発行するのに役立ちます。 このガイドでは、このオファーの要件について説明します。 

これらは、Marketplace を通じてデプロイされ課金されるトランザクションのオファーです。 ユーザーに表示される行動喚起は、"今すぐ入手する" です。

対象となるソリューションが、Kubernetes ベースの Azure コンテナー サービスとしてプロビジョニングされる Docker コンテナー イメージであるときは、オファーの種類としてコンテナーを使用します。

>[!NOTE]
>Kubernetes ベースの Azure コンテナー サービスとは、たとえば Azure Kubernetes Service や Azure Container Instances が該当します (Kubernetes ベースのコンテナー ランタイムは Azure の顧客が選択)。  

Microsoft は現在、無料およびライセンス持ち込み (BYOL) のライセンス モデルをサポートしています。

## <a name="containers-offer"></a>Containers のオファー

| 要件 | 詳細 |  
|:--- |:--- |  
| 請求/メータリング | 無料または BYOL のどちらかの課金モデルをサポートします。 |  
| Dockerfile からビルドされたイメージ | コンテナー イメージは、Docker イメージの仕様に基づき、Dockerfile からビルドされる必要があります。<ul> <li>Docker イメージの作成の詳細については、[docs.docker.com/engine/reference/builder/#usage](https://docs.docker.com/engine/reference/builder/#usage) にある使用方法のセクションを参照してください。</li> </ul> |  
| ACR でのホスティング | コンテナー イメージは、Azure Container Registry (ACR) リポジトリでホストされる必要があります。<ul> <li>ACR の扱いの詳細については、[docs.microsoft.com/azure/container-registry/container-registry-get-started-portal](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal) にある「クイック スタート: Azure Portal を使用したコンテナー レジストリの作成」を参照してください。</li> </ul> |  
| イメージのタグ付け | コンテナー イメージには、少なくとも 1 つのタグを含める必要があります (タグの最大数: 16)。<ul> <li>画像のタグ付けの詳細については、[docs.docker.com/engine/reference/commandline/tag](https://docs.docker.com/engine/reference/commandline/tag) にある Docker タグのページを参照してください。</li> </ul> |  

## <a name="next-steps"></a>次のステップ

まだ行っていない場合は、 

- マーケットプレースに[登録](https://azuremarketplace.microsoft.com/sell)します。

登録済みかつ新しいオファーを作成しているまたは既存のオファーを操作している場合は、

- [Cloud パートナー ポータルにログイン](https://cloudpartner.azure.com)して、オファーを作成または完成させます。
- 詳しくは、「[Containers](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-containers-offer)」をご覧ください。
