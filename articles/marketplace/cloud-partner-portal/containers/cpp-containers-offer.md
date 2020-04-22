---
title: Azure Containers イメージ オファー | Azure Marketplace
description: Azure Marketplace でコンテナー オファーを発行するプロセスの概要です。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: dsindona
ms.openlocfilehash: cd9f98d42efcb35dbab4f3c0a06c5a11360e36b6
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2020
ms.locfileid: "81270351"
---
# <a name="containers"></a>Containers

> [!IMPORTANT]
> 2020 年 4 月 13 日以降、Azure Container オファーの管理のパートナー センターへの移行が開始されます。 移行後は、パートナー センターにてオファーを作成・管理することになります。 「[Azure Container オファーを作成する](https://aka.ms/CreateContainerOffer)」の手順に従って、移行されたオファーを管理します。

<table> <tr> <td>このセクションでは、コンテナー イメージを <a href="https://azuremarketplace.microsoft.com">Azure Marketplace</a> に発行する方法を説明します。  
コンテナー オファーの種類では、<a href="https://docs.microsoft.com/azure/aks/index">Azure Kubernetes Service</a> インスタンスまたは <a href="https://docs.microsoft.com/azure/container-instances/container-instances-overview">Azure Container Instances</a> としてプロビジョニングされ、<a href="https://docs.microsoft.com/azure/container-registry">Azure Container Registry</a> リポジトリでホストされている、Docker コンテナー イメージがサポートされています。 </td> <td><img src="./media/container-icon.png"  alt="Azure container icon" /></td> </tr> </table>

## <a name="offer-components"></a>オファーのコンポーネント

このセクションはコンテナーの発行の要素についての概要であり、Azure Marketplace への発行元のためのガイドとして提供されています。 発行は、次の主要な部分に分かれています。

- [前提条件](./cpp-prerequisites.md) - コンテナー オファーを作成または発行する前の、技術要件およびビジネス要件の一覧を示します。
- [オファーを作成する](./cpp-create-offer.md) - Cloud パートナー ポータルを使用して新しいコンテナー オファー エントリを作成するために必要な手順を示します。
- [技術資産を準備する](./cpp-create-technical-assets.md) - Azure Marketplace でのオファーとしてのコンテナー ソリューション用に技術資産を作成する方法です。
- [オファーを発行する](./cpp-publish-offer.md) - Azure Marketplace に発行するためにオファーを送信する方法です。

## <a name="container-publishing-process"></a>コンテナーの発行プロセス

次の図は、VM オファーの発行の概要手順を示したものです。
![オファーを発行する手順](./media/containers-offer-process.png)

コンテナー オファーを発行する手順の概要は次のとおりです。

1. オファーを作成する - オファーに関する詳細情報を指定します。 この情報には、オファーの説明、マーケティング資料、サポート情報、資産の仕様などが含まれます。
2. ビジネス資産と技術資産を作成する - 関連付けられたソリューション (Azure Container Registry でホストされているコンテナー イメージ) に対するビジネス資産 (法務文書とマーケティング資料) と技術資産を作成します。
3. SKU を作成する - オファーに関連付けられた SKU を作成します。 発行を計画しているイメージごとに固有の SKU が必要です。
4. オファーを認定して発行する - オファーと技術資産が完成したら、オファーを送信できます。 この送信によって発行プロセスが開始します。 このプロセスの間に、ソリューションがテストされ、検証され、認定されて、Azure Marketplace での "運用が開始" されます。

## <a name="next-steps"></a>次のステップ

これらの手順を検討する前に、Microsoft Azure Marketplace へのコンテナーの発行に関する[技術要件とビジネス要件](./cpp-prerequisites.md)を満たしている必要があります。
