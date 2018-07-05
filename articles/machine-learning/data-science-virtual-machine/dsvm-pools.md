---
title: Data Science Virtual Machine プール - Azure | Microsoft Docs
description: Data Science VM のプールをチームの共有リソースとして展開します
keywords: ディープ ラーニング, AI, データ サイエンス ツール, データ サイエンス仮想マシン, 地理空間分析, チーム データ サイエンス プロセス
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
ms.openlocfilehash: 0740ff7542d066442146b8e80e188ad5ba49a2b5
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309400"
---
# <a name="create-a-shared-pool-of-data-science-virtual-machines"></a>Data Science Virtual Machine の共有プールを作成する

この記事では、チームで使用する Data Science Virtual Machine (DSVM) の共有プールを作成する方法について説明します。 共有プールを使用する利点は、リソース使用率が向上し、共有とコラボレーションが促進され、DSVM リソースをより効果的に管理できることです。 

多くの方法とテクノロジを使って、DSVM のプールを作成することができます。 この記事では、バッチ処理と対話型 VM 用のプールに注目します。

## <a name="batch-processing-pool"></a>バッチ処理プール
ジョブを主にオフライン バッチで実行するように DSVM のプールを設定する場合は、[Azure Batch AI](https://docs.microsoft.com/azure/batch-ai/) サービスまたは [Azure Batch](https://docs.microsoft.com/azure/batch/) サービスを使用できます。 この記事では、Azure Batch AI に重点を置いて説明します。

Azure Batch AI 内のイメージの 1 つとして DSVM の Ubuntu エディションがサポートされています。 Azure Batch AI クラスターを作成する Azure CLI または Python SDK で、`image` パラメーターを指定し、`UbuntuDSVM` に設定できます。 Azure で使用可能な[幅広い VM インスタンスの選択肢](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)から、必要な処理ノードの種類 (GPU ベースのインスタンスと CPU のみのインスタンス、CPU の数、メモリ) を選択できます。 

GPU ベースのノードで Batch AI の Ubuntu DSVM イメージを使用すると、必要なすべての GPU ドライバーとディープ ラーニング フレームワークがプレインストールされます。 プレインストールにより、バッチ ノードを準備するための時間がかなり短縮されます。 実際には、Ubuntu DSVM で対話的に開発する場合、環境の Batch AI ノードがまったく同じ設定と構成であることがわかります。 

通常、Batch AI クラスターを作成するときは、すべてのノードでマウントされるファイル共有も作成します。 ファイル共有は、データの入力と出力、およびバッチ ジョブのコード/スクリプトの格納に使われます。 

Batch AI クラスターを作成した後は、同じ CLI または Python SDK を使って、実行するジョブを送信できます。 支払いは、バッチ ジョブの実行に使用される時間に対してのみ行います。 

詳細については、次を参照してください。
* [Azure CLI](https://docs.microsoft.com/azure/batch-ai/quickstart-cli) を使って Batch AI を管理する手順のチュートリアル
* [Python](https://docs.microsoft.com/azure/batch-ai/quickstart-python) を使って Batch AI を管理する手順のチュートリアル
* Batch AI でさまざまな AI およびディープ ラーニング フレームワークを使用する方法を示す [Batch AI レシピ](https://github.com/Azure/BatchAI)

## <a name="interactive-vm-pool"></a>対話型の VM プール

AI/データ サイエンス チーム全体で共有される対話型 VM のプールにより、ユーザーは、ユーザーのセットごとに専用のインスタンスを持つ代わりに DSVM の使用可能なインスタンスにログインできます。 この設定により、可用性が向上し、リソースをより効果的に利用できます。 

対話型 VM プールの作成に使われるテクノロジは [Azure 仮想マシン スケール セット](https://docs.microsoft.com/azure/virtual-machine-scale-sets/)です。 スケール セットを使って、負荷分散と自動スケーリングが行われる同一の VM のグループを作成して管理できます。 

ユーザーは、メイン プールの IP または DNS アドレスにログインします。 スケール セットは、セッションをスケール セット内の使用可能な DSVM に自動的にルーティングします。 ユーザーはログインする VM に関係なく似た環境を望むので、スケール セット内の VM のすべてのインスタンスは Azure ファイル共有や NFS 共有などの共有ネットワーク ドライブをマウントします。 通常、ユーザーの共有ワークスペースは各インスタンスにマウントされている共有ファイル ストアに保持されます。 

Ubuntu DSVM インスタンスでスケール セットを作成する Azure Resource Manager テンプレートのサンプルが [GitHub](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json) にあります。 Azure Resource Manager テンプレート用の[パラメーター ファイル](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.parameters.json)のサンプルも同じ場所にあります。 

Azure CLI でパラメーター ファイルの値を指定することで、Azure Resource Manager テンプレートからスケール セットを作成できます。 

```
az group create --name [[NAME OF RESOURCE GROUP]] --location [[ Data center. For eg: "West US 2"]
az group deployment create --resource-group  [[NAME OF RESOURCE GROUP ABOVE]]  --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json --parameters @[[PARAMETER JSON FILE]]
```
上のコマンドでは次のものがあることが想定されています。
* パラメーター ファイルのコピーと、スケール セットのインスタンスに指定されている値。
* VM インスタンスの数。
* Azure ファイル共有へのポインター。
* 各 VM にマウントされるストレージ アカウントの資格情報。 

パラメーター ファイルは、コマンドでローカルに参照されています。 パラメーターをインラインで渡すか、スクリプトで要求することもできます。  

上記のテンプレートは、フロントエンドのスケール セットから Ubuntu DSVM のバックエンド プールへの SSH と JupyterHub ポートも有効にします。 ユーザーは、SSH または JupyterHub 上の VM に通常の方法でログインするだけです。 VM インスタンスは動的にスケールアップまたはスケールダウンできるので、マウントされた Azure Files 共有に状態を保存する必要があります。 同じアプローチを使用して、Windows DSVM のプールを作成できます。 

[Azure ファイル共有をマウントするスクリプト](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh)も、GitHub の Azure DataScienceVM リポジトリにあります。 スクリプトは、パラメーター ファイルで指定されているマウント ポイントに Azure ファイル共有をマウントします。 またスクリプトは、初期ユーザーのホーム ディレクトリにマウントされたドライブへのソフト リンクも作成します。 Azure ファイル共有内のユーザー固有の notebook ディレクトリは `$HOME/notebooks/remote` ディレクトリにリンクされており、ユーザーは Jupyter notebooks にアクセスしてそれを実行および保存できます。 VM 上に追加ユーザーを作成して各ユーザーの Jupyter ワークスペースで Azure ファイル共有をポイントするときも同じ規則を使用できます。 

仮想マシン スケール セットは自動スケーリングをサポートします。 追加インスタンスを作成するとき、およびインスタンスをスケールダウンするときのルールを設定できます。 たとえば、VM がまったく使われていないときはゼロ インスタンスにスケールダウンしてクラウド ハードウェア使用コストを節約できます。 仮想マシン スケール セットのドキュメント ページには、[自動スケーリング](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview)に関する詳しい手順が記載されています。

## <a name="next-steps"></a>次の手順

* [共通 ID を設定する](dsvm-common-identity.md)
* [クラウド リソースにアクセスするための資格情報を安全に保存する](dsvm-secure-access-keys.md)















