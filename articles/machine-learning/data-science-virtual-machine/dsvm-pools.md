---
title: データ サイエンス仮想マシン プール - Azure | Microsoft Docs
description: データ サイエンス VM のプールをチームの共有リソースとしてデプロイする
keywords: ディープ ラーニング、AI、データ サイエンス ツール、データ サイエンス仮想マシン、地理空間分析、チーム データ サイエンス プロセス
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: c7aab0435ecbd0aee57a15008ac0270159ec2eb3
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837084"
---
# <a name="creating-a-shared-pool-of-data-science-virtual-machines"></a>データ サイエンス仮想マシンの共有プールの作成

この記事では、データ サイエンス仮想マシン (DSVM) の共有プールをチームで使用するために作成する方法について説明します。 共有プールを使用する利点は、リソース使用率が向上し、共有とコラボレーションが促進され、IT 部門が DSVM リソースをより効果的に管理できることです。 

DSVM のプールの作成に使用できる多くの方法とさまざまなテクノロジがあります。  主なシナリオを次に示します。

* バッチ処理用のプール
* 対話型 VM 用のプール

## <a name="batch-processing-pool"></a>バッチ処理プール
ジョブを主にオフライン バッチで実行するように DSVM のプールを設定する場合は、[Azure Batch AI](https://docs.microsoft.com/azure/batch-ai/) サービスまたは [Azure Batch](https://docs.microsoft.com/azure/batch/) を使用できます。 

### <a name="azure-batch-ai"></a>Azure Batch AI
Azure Batch AI 内のイメージの 1 つとして DSVM の Ubuntu エディションがサポートされています。 Azure Batch AI クラスターを作成する Azure CLI または Python SDK で、```image``` パラメーターを指定し、```UbuntuDSVM``` に設定できます。 Azure で使用可能な[幅広い VM インスタンスの選択肢](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)から、必要な処理ノードの種類 (GPU ベースのインスタンスと CPU のみのインスタンス、CPU の数、メモリ) を選択できます。 GPU ベースのノードで Batch AI の Ubuntu DSVM イメージを使用すると、必要なすべての GPU ドライバーと深層学習フレームワークがプレインストールされて、バッチ ノードを準備するための時間がかなり短縮されます。 実際には、Ubuntu DSVM で対話的に開発する場合、環境の Batch AI ノードがまったく同じ設定と構成であることがわかります。 通常は、Batch AI クラスターの作成時に、すべてのノードによってマウントされ、データの入力および出力とバッチ ジョブ コード/スクリプトの格納に使用されるファイル共有も作成します。 

Batch AI クラスターが作成されたら、同じ CLI または Python SDK を使用して、実行するジョブを送信できます。 支払いは、バッチ ジョブの実行に使用される時間に対してのみ行います。 

#### <a name="more-information"></a>詳細情報
* Batch AI を管理するための [Azure CLI](https://docs.microsoft.com/azure/batch-ai/quickstart-cli) の使用のチュートリアル
* Batch AI を管理するための [Python](https://docs.microsoft.com/azure/batch-ai/quickstart-python) の使用のチュートリアル
* Batch AI でさまざまな AI/ディープ ラーニング フレームワークを使用する方法を示す [Batch AI レシピ](https://github.com/Azure/BatchAI)を利用できます。

## <a name="interactive-vm-pool"></a>対話型の VM プール

AI/データ サイエンス チーム全体で共有される対話型 DSVM のプールにより、ユーザーは、ユーザーのセットごとに専用のインスタンスを持つ代わりに DSVM の使用可能なインスタンスにログインできます。 これにより、可用性が向上し、リソースをより効果的に利用できます。 

対話型 VM プールの作成に使用されるテクノロジは [Azure Virtual Machine Scale Sets](https://docs.microsoft.com/azure/virtual-machine-scale-sets/) (VMSS) で、これにより同一の負荷分散された自動スケーリング VM のグループを作成および管理できます。 ユーザーは、メイン プールの IP または DNS アドレスにログインします。 スケール セットは、セッションをスケール セット内の使用可能な DSVM に自動的にルーティングします。 ユーザーはログインする VM に関係なく類似の環境を好むので、スケール セット内の VM のすべてのインスタンスは Azure Files や NFS 共有などの共有ネットワーク ドライブをマウントします。 通常、ユーザーの共有ワークスペースは各インスタンスにマウントされている共有ファイル ストアに保持されます。 

Ubuntu DSVM インスタンスと共に VM スケール セットを作成するサンプルの Azure Resource Manager テンプレートが [github](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json) にあります。 Azure Resource Manager テンプレートの[パラメーター ファイル](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.parameters.json)のサンプルも同じ場所にあります。 

Azure CLI を使用してパラメーター ファイルの適切な値を指定することで、Azure Resource Manager テンプレートから VM スケール セットを作成できます。 

```
az group create --name [[NAME OF RESOURCE GROUP]] --location [[ Data center. For eg: "West US 2"]
az group deployment create --resource-group  [[NAME OF RESOURCE GROUP ABOVE]]  --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json --parameters @[[PARAMETER JSON FILE]]
```
上記のコマンドは、VM スケール セットのインスタンス、VM インスタンスの数、Azure Files へのポインターの値が指定されたパラメーター ファイルのコピーと、各 VM にマウントされるストレージ アカウントの資格情報があることを想定しています。 パラメーター ファイルは、上記のコマンドでローカルに参照されています。 パラメーターをインラインで渡すか、スクリプトで要求することもできます。  

上記のテンプレートは、フロントエンドの VM スケール セットから Ubuntu DSVM のバックエンド プールへの SSH と Jupyterhub ポートも有効にします。  ユーザーは、SSH または JupyterHub 上の VM に通常の方法でログインするだけです。 VM インスタンスは動的にスケールアップまたはスケールダウンできるので、マウントされた Azure Files 共有に状態を保存する必要があります。 同じアプローチを使用して、Windows DSVM のプールを作成できます。 

[Azure Files をマウントするスクリプト](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh)も、Azure DataScienceVM Github にあります。 パラメーター ファイルで指定されたマウント ポイントに Azure Files をマウントすることに加えて、初期ユーザーのホーム ディレクトリ内のマウントされたドライブへの追加のソフト リンクも作成され、共有 Azure Files 内のユーザー固有のノートブック ディレクトリが ```$HOME/notebooks/remote``` ディレクトリにソフト リンクされるので、ユーザーは Jupyter Notebook にアクセスし、実行、保存できます。  VM 上に追加のユーザーを作成するときに同じ規則を使用して、共有 Azure Files への各ユーザーの Jupyter ワークスペースをポイントできます。 

Azure VM Scale Sets では、追加のインスタンスをいつ作成するか、どのような状況でインスタンスをスケールダウンするか (VM がまったく使用されていないときにインスタンスをゼロにしてクラウド ハードウェアの使用コストを削減するなど) に関するルールを設定できる自動スケーリングがサポートされます。 VM Scale Sets のドキュメント ページには、[自動スケーリング](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview)に関する詳しい手順が記載されています。

## <a name="next-steps"></a>次の手順

* [共通 ID を設定する](dsvm-common-identity.md)
* [クラウド リソースにアクセスするための資格情報を安全に保存する](dsvm-secure-access-keys.md)















