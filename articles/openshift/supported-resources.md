---
title: Azure Red Hat OpenShift のサポートされているリソース | Microsoft Docs
description: Microsoft Azure Red Hat OpenShift でサポートされている Azure リージョンと仮想マシンのサイズについて説明します。
services: container-service
author: tylermsft
ms.author: twhitney
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.openlocfilehash: 22cc195b7808ad4a9111aafcf883a68c51f3709f
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2019
ms.locfileid: "65080265"
---
# <a name="azure-red-hat-openshift-resources"></a>Azure Red Hat OpenShift のリソース

このトピックでは、Microsoft Azure Red Hat OpenShift サービスでサポートされている Azure リージョンと仮想マシンのサイズを示します。

## <a name="azure-regions"></a>Azure Azure リージョン

世界中の次の Azure リージョンに、Azure Red Hat OpenShift クラスターをデプロイできます。

|リージョン|CLI コード|
|-|-|
|AU オーストラリア東部|`australiaeast`|
|CA カナダ中部|`canadacentral`|
|CA カナダ東部|`canadaeast`|
|US 米国東部|`eastus`|
|US 米国西部|`westus`|
|EU 西ヨーロッパ|`westeurope`|
|EU 北ヨーロッパ|`northeurope`|

## <a name="virtual-machine-sizes"></a>仮想マシン サイズ

Azure Red Hat OpenShift クラスター内のコンピューティング ノードに対して指定できる、サポートされている仮想マシンのサイズは次のとおりです。

> [!Important]
> それぞれの VM で接続できるドライブ数は異なります。 これは、メモリや CPU サイズほどすぐには明らかにならない場合があります.
> すべてのリージョンですべての VM サイズを使用できるわけではありません。 指定したサイズが API でサポートされていても、指定したリージョンでそのサイズが利用できない場合は、エラーが発生する可能性があります。
> 詳細については、[リージョンごとのサポートされている VM サイズの最新一覧](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)に関するページを参照してください。

## <a name="compute-node-sizes"></a>コンピューティング ノードのサイズ

Azure Red Hat OpenShift の REST API では、次のコンピューティング ノードのサイズがサポートされています。

|Size|vCPU|RAM|
|-|-|-|
|Standard D4s v3|4|16 GB|
|Standard D8s v3|8|32 GB|
|Standard D16s v3|16|64 GB|
|Standard D32s v3|32|128 GB|
|-|-|-|
|Standard E4s v3|4|32 GB|
|Standard E8s v3|8|64 GB|
|Standard E16s v3|16|128 GB|
|Standard E32s v3|32|256 GB|
|-|-|-|
|Standard F8s v2|8|16 GB|
|Standard F16s v2|16|32 GB|
|Standard F32s v2|32|64 GB|

## <a name="master-node-sizes"></a>マスター ノードのサイズ

Azure Red Hat OpenShift の REST API では、次のマスター/インフラストラクチャ ノードのサイズがサポートされています。

|Size|vCPU|RAM|
|-|-|-|
|Standard D4s v3|4|16 GB|
|Standard D8s v3|8|32 GB|
|Standard D16s v3|16|64 GB|
|Standard D32s v3|32|128 GB|

## <a name="next-steps"></a>次の手順

[Azure Red Hat OpenShift クラスターの作成](tutorial-create-cluster.md)に関するチュートリアルを試してください。