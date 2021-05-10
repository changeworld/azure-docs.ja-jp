---
title: コンピューティング クラスターおよびインスタンスのホスト OS をアップグレードする
titleSuffix: Azure Machine Learning
description: コンピューティング クラスター用のホスト OS とコンピューティング インスタンスを Ubuntu 16.04 LTS から 18.04 LTS にアップグレードします。
services: machine-learning
author: nishankgu
ms.author: nigup
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 03/03/2021
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 710a860b1ed87f176b6f42b4963dad17acb323b1
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "104954056"
---
# <a name="upgrade-compute-instance-and-compute-cluster-host-os"></a>コンピューティング インスタンスとコンピューティング クラスター ホスト OS をアップグレードする

Azure Machine Learning __コンピューティング クラスター__ と __コンピューティング インスタンス__ は、マネージド コンピューティング インフラストラクチャです。 管理サービスとして、Microsoft はホスト OS およびインストールされているパッケージとソフトウェアのバージョンを管理します。

コンピューティング クラスターとコンピューティング インスタンスのホスト OS が Ubuntu 16.04 LTS になりました。 **2021 年 4 月 30 日** に、Ubuntu の 16.04 のサポートは終了します。 __2021 年 3 月 15 日__ 以降、Microsoft はホスト OS を Ubuntu 18.04 LTS に自動的に更新します。 18.04 に更新すると、Ubuntu コミュニティから継続的なセキュリティ更新プログラムとサポートが保証されます。 この更新プログラムは、Azure リージョン間でロール アウトされ、__2021 年 4 月 9 日__ までにすべてのリージョンで利用できるようになります。 Ubuntu の 16.04 のサポート終了の詳細については、[Ubuntu リリースのブログ](https://wiki.ubuntu.com/Releases)を参照してください。

> [!TIP]
> * ホスト OS は、モデルのトレーニングまたはデプロイ時に[環境](how-to-use-environments.md)に対して指定できる OS バージョンではありません。 環境は Docker 内で実行されます。 Docker はホスト OS で実行されます。
> * 現在、トレーニングまたはデプロイに Ubuntu 16.04 ベースの環境を使用している場合は、Ubuntu 18.04 ベースのイメージの使用に切り替えることをお勧めします。 詳細については、「[環境の使用方法](how-to-use-environments.md)」と [Azure Machine Learning コンテナー リポジトリ](https://github.com/Azure/AzureML-Containers/tree/master/base)に関するページを参照してください。
> * Ubuntu 18.04 に基づいて Azure Machine Learning コンピューティング インスタンスを使用する場合、Python の既定のバージョンは _Python 3.8_ です。
## <a name="creating-new-resources"></a>新しいリソースの作成

__2021 年 4 月 9 日__ より後に作成されたコンピューティング クラスターまたはコンピューティング インスタンスは、Ubuntu 18.04 LTS を既定のホスト OS として使用します。 他のホスト OS は選択できません。

## <a name="upgrade-existing-resources"></a>既存のリソースをアップグレードする

__2021 年 3 月 15 日__ より前に作成された既存のコンピューティング クラスターまたはコンピューティング インスタンスがある場合は、ホスト OS を Ubuntu 18.04 にアップグレードするアクションを実行する必要があります。 Azure Machine Learning にアクセスするリージョンによっては、__2021 年 4 月 9 日__ より後に、変更がすべてのリージョンにロール アウトされていることを確認するためにこれらのアクションを実行することをお勧めします。

* __Azure Machine Learning コンピューティング クラスター__:

    * __最小ノード数が 0__ でクラスターが構成されている場合、すべてのジョブが完了すると自動的にアップグレードされ、ノード数が 0 になります。
    * __最小ノード数が 0 を超えている__ 場合、最小ノードを一時的に 0 に変更し、クラスター ノード数が 0 になるのを許可します。

    最小ノードの変更の詳細については、[az ml computetarget update amlcompute](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/update#ext_azure_cli_ml_az_ml_computetarget_update_amlcompute) Azure CLI コマンドまたは [AmlCompute.update()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#update-min-nodes-none--max-nodes-none--idle-seconds-before-scaledown-none-) SDK リファレンスを参照してください。

* __Azure Machine Learning コンピューティング インスタンス__: 新しいコンピューティング インスタンス (Ubuntu 18.04 を使用) を作成し、古いインスタンスを削除します。

    * ワークスペースのファイル共有内、データ ストア内、データセットに格納されているすべてのノートブックには、新しいコンピューティング インスタンスからアクセスできます。
    * カスタム Conda 環境を作成した場合は、既存のインスタンスからこれらの環境をエクスポートし、新しいインスタンスにインポートすることができます。 Conda のエクスポートとインポートの詳細については、docs.conda.io の [Conda のドキュメント](https://docs.conda.io/)を参照してください。

    詳細については、[コンピューティング インスタンスの概要](concept-compute-instance.md)に関する記事と「[Azure Machine Learning コンピューティング インスタンスを作成して管理する](how-to-create-manage-compute-instance.md)」を参照してください。

## <a name="check-host-os-version"></a>ホスト OS バージョンを確認する

ホストの OS バージョンを確認する方法の詳細については、[Ubuntu のバージョンを確認する方法](https://help.ubuntu.com/community/CheckingYourUbuntuVersion)に関する Ubuntu コミュニティ Wiki ページを参照してください。

> [!TIP]
> Wiki から `lsb_release -a` コマンドを使用するには、[コンピューティング インスタンスでターミナル セッションを使用](how-to-access-terminal.md)します。
## <a name="next-steps"></a>次のステップ

その他のご質問やご不明な点がある場合は、[ubuntu18azureml@service.microsoft.com](mailto:ubuntu18azureml@service.microsoft.com) にご連絡ください。
