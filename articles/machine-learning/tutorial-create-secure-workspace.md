---
title: セキュリティで保護されたワークスペースを作成する
titleSuffix: Azure Machine Learning
description: セキュリティで保護された仮想ネットワーク内に Azure Machine Learning ワークスペースと必要な Azure サービスを作成します。
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.reviewer: jhirono
ms.author: larryfr
author: blackmist
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: subject-rbac-steps
ms.openlocfilehash: c488b3ec12d0aabcfb84b0ebb700eb738e8698e1
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131558062"
---
# <a name="how-to-create-a-secure-workspace"></a>セキュリティで保護されたワークスペースを作成する方法

この記事では、セキュリティで保護された Azure Machine Learning ワークスペースを作成して接続する方法について説明します。 セキュリティで保護されたワークスペースでは、Azure Virtual Network を使用して、Azure Machine Learning で使用されるリソースの周囲にセキュリティ境界を作成します。 

このチュートリアルでは、以下のタスクを実行します。

> [!div class="checklist"]
> * __仮想ネットワーク 内のサービス間の通信をセキュリティで保護__ するために Azure Virtual Network (VNet) を作成します。
> * VNet の背後に Azure ストレージ アカウント (BLOB とファイル) を作成します。 このサービスは、__ワークスペースの既定のストレージ__ として使用されます。
> * VNet の背後に Azure Key Vault を作成します。 このサービスは、__ワークスペースで使用されるシークレットを格納する__ ために使用されます。 たとえば、ストレージ アカウントにアクセスするために必要なセキュリティ情報などです。
> * Azure Container Registry (ACR) を作成します。 このサービスは、Docker イメージのリポジトリとして使用されます。 __Docker イメージは、機械学習モデルをトレーニングするときや、トレーニング済みのモデルをエンドポイントとしてデプロイするときに必要なコンピューティング環境を提供します__。
> * Azure Machine Learning ワークスペースを作成します。
> * ジャンプ ボックスを作成します。 ジャンプ ボックスは、VNet の背後にある Azure 仮想マシンです。 VNet ではパブリック インターネットからのアクセスが制限されるため、__VNet の背後にあるリソースに接続する方法としてジャンプ ボックスが使用されます__。
> * VNet の背後で動作するように Azure Machine Learning スタジオを構成します。 スタジオには、__Azure Machine Learning 用の Web インターフェイス__ が用意されています。
> * Azure Machine Learning コンピューティング クラスターを作成する。 コンピューティング クラスターは、__クラウドで機械学習モデルをトレーニングする__ ときに使用されます。 これは、Azure Container Registry が VNet の背後にある構成では、Docker イメージのビルドにも使用されます。
> * ジャンプ ボックスに接続し、Azure Machine Learning スタジオを使用します。

環境が前提条件を満たし、ARM テンプレートを使い慣れている場合は、[Azure にデプロイ] ボタンを選択して、このチュートリアルの最初の 5 つの手順を完了することもできます。 続きは「[ワークスペースに接続する](#connect-to-the-workspace)」から読み進めることができます。

[![Azure にデプロイ](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.machinelearningservices%2Fmachine-learning-workspace-vnet%2Fazuredeploy.json)
[![Azure US Gov にデプロイ](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazuregov.svg?sanitize=true)](https://portal.azure.us/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.machinelearningservices%2Fmachine-learning-workspace-vnet%2Fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

* Azure 仮想ネットワークと IP ネットワークについて熟知している
* この記事のほとんどの手順では Azure portal または Azure Machine Learning スタジオを使用しますが、一部の手順では Machine Learning 用の Azure CLI 拡張機能を使用します。

## <a name="create-a-virtual-network"></a>仮想ネットワークの作成

仮想ネットワークを作成するには、次の手順を使用します。

1. [Azure portal](https://portal.azure.com) で、左上隅にあるポータル メニューを選択します。 メニューで __[+ リソースの作成]__ を選択し、検索フィールドに「__Virtual Network__」と入力します。 「__仮想ネットワーク__」エントリを選択し、 __[作成]__ を選択します。


    :::image type="content" source="./media/tutorial-create-secure-workspace/create-resource-search-vnet.png" alt-text="リソースの作成の UI 検索":::

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-resource-vnet.png" alt-text="仮想ネットワークの作成":::

1. __[基本]__ タブで、このリソースに使用する Azure __サブスクリプション__ を選択し、__リソース グループ__ を選択するか、新規に作成します。 __[インスタンスの詳細]__ で、仮想ネットワークのわかりやすい __名前__ を入力し、それを作成する __リージョン__ を選択します。

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-vnet-basics.png" alt-text="基本的な仮想ネットワーク構成の画像":::

1. __[IP アドレス]__ タブを選択します。既定の設定は、次の画像のようになります。

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-vnet-ip-address-default.png" alt-text="既定の IP アドレス画面":::

    次の手順を使用して、IP アドレスを構成し、リソースのトレーニングとスコアリング用のサブネットを構成します。

    > [!TIP]
    > すべての Azure ML リソースに 1 つのサブネットを使用できますが、この記事の手順では、トレーニングとスコアリングのリソースを分けるために 2 つのサブネットを作成する方法を示します。
    >
    > ワークスペースと他の依存関係サービスは、トレーニングのサブネットに入ります。 これらは、スコアリングのサブネットなど、他のサブネット内のリソースでも使用できます。

    1. __[IPv4 アドレス空間]__ の既定値を見てください。 スクリーンショットの値は __172.17.0.0/16__ です。 __値は、人によって異なる可能性があります__。 別の値を使用できますが、このチュートリアルの残りの手順は 172.17.0.0/16 の値に基づいています。
    1. __既定__ のサブネットを選択し、 __[サブネットの削除]__ を選択します。
    
        :::image type="content" source="./media/tutorial-create-secure-workspace/delete-default-subnet.png" alt-text="既定のサブネットの削除のスクリーンショット":::

    1. トレーニングに使用されるワークスペース、依存関係サービス、およびリソースを含めるサブネットを作成するために、 __[+ サブネットの追加]__ を選択し、サブネットに次の値を使用します。
        * __サブネット名:__ Training
        * __サブネット アドレス範囲__: 172.17.0.0/24

        :::image type="content" source="./media/tutorial-create-secure-workspace/vnet-add-training-subnet.png" alt-text="Training サブネットのスクリーンショット":::

        > [!TIP]
        > _サービス エンドポイント_ を使用して、Azure ストレージ アカウント、Azure Key Vault、 Azure Container Registry を VNet に追加する予定の場合は、[__サービス__] で次を選択します。
        > * __Microsoft.Storage__
        > * __Microsoft.KeyVault__
        > * __Microsoft.ContainerRegistry__
        >
        > _プライベート エンドポイント_ を使用してこれらのサービスを VNet に追加する予定の場合、これらのエントリを選択する必要はありません。 この記事の手順では、これらのサービスのプライベート エンドポイントを使用するため、次の手順を実行するときにそれらを選択する必要はありません。

    1. モデルのスコアリングに使用するコンピューティング リソース用のサブネットを作成するために、もう一度 __[+ サブネットの追加]__ を選択し、次の値を使用します。
        * __サブネット名:__ Scoring
        * __サブネット アドレス範囲__: 172.17.1.0/24

        :::image type="content" source="./media/tutorial-create-secure-workspace/vnet-add-scoring-subnet.png" alt-text="Scoring サブネットのスクリーンショット":::

        > [!TIP]
        > _サービス エンドポイント_ を使用して、Azure ストレージ アカウント、Azure Key Vault、 Azure Container Registry を VNet に追加する予定の場合は、[__サービス__] で次を選択します。
        > * __Microsoft.Storage__
        > * __Microsoft.KeyVault__
        > * __Microsoft.ContainerRegistry__
        >
        > _プライベート エンドポイント_ を使用してこれらのサービスを VNet に追加する予定の場合、これらのエントリを選択する必要はありません。 この記事の手順では、これらのサービスのプライベート エンドポイントを使用するため、次の手順を実行するときにそれらを選択する必要はありません。

1. __[セキュリティ]__ を選択します。 __[BastionHost]__ で __[有効化]__ を選択します。 [Azure Bastion](../bastion/bastion-overview.md) により、後の手順で VNet 内に作成する VM ジャンプ ボックスに安全にアクセスする方法が提供されます。 残りのフィールドには、次の値を使用します。

    * __Bastion 名__: この Bastion インスタンスの一意の名前
    * __AzureBastionSubnet のアドレス空間__: 172.17.2.0/27
    * __パブリック IP アドレス__: 新しいパブリック IP アドレスを作成します。

    他のフィールドは既定値のままにします。

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-bastion.png" alt-text="Bastion の構成のスクリーンショット":::

1. __[確認と作成]__ を選択します。

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-vnet-ip-address-final.png" alt-text="[確認と作成] ボタンを示すスクリーンショット":::

1. 情報が正しいことを確認し、 __[作成]__ を選択します。

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-vnet-review.png" alt-text="確認ページのスクリーンショット":::

## <a name="create-a-storage-account"></a>ストレージ アカウントを作成する

1. [Azure portal](https://portal.azure.com) で、左上隅にあるポータル メニューを選択します。 メニューで __[+ リソースの作成]__ を選択し、「__Storage account__」と入力します。 __[ストレージ アカウント]__ エントリを選択し、 __[作成]__ を選択します。
1. __[基本]__ タブで、仮想ネットワークに対して前に使用した __サブスクリプション__、__リソース グループ__、および __リージョン__ を選択します。 一意の __ストレージ アカウント名__ 入力し、 __[冗長性]__ を __[ローカル冗長ストレージ (LRS)]__ に設定します。

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-storage.png" alt-text="ストレージ アカウントの基本の構成の画像":::

1. __[ネットワーク]__ タブで __[プライベート エンドポイント]__ を選択し、 __[+ プライベート エンドポイントの追加]__ を選択します。

    :::image type="content" source="./media/tutorial-create-secure-workspace/storage-enable-private-endpoint.png" alt-text="BLOB プライベート ネットワークを追加する UI":::

1. __[プライベート エンドポイントの作成]__ フォームで、次の値を使用します。
    * __サブスクリプション__: 前に作成したリソースを含むものと同じ Azure サブスクリプション。
    * __リソース グループ__: 前に作成したリソースを含むものと同じ Azure リソース グループ。
    * __場所__: 前に作成したリソースを含むものと同じ Azure リージョン。
    * __名前__: このプライベート エンドポイントの一意の名前。
    * __対象サブリソース__: BLOB
    * __仮想ネットワーク__: 前に作成した仮想ネットワーク。
    * __サブネット__: Training (172.17.0.0/24)
    * __プライベート DNS 統合__: はい
    * __プライベート DNS ゾーン__: privatelink.blob.core.windows.net

    __[OK]__ を選択して、プライベート エンドポイントを作成します。

1. __[確認と作成]__ を選択します。 情報が正しいことを確認し、 __[作成]__ を選択します。

1. ストレージ アカウントが作成されたら、 __[リソースに移動]__ を選択します。

    :::image type="content" source="./media/tutorial-create-secure-workspace/storage-go-to-resource.png" alt-text="新しいストレージ リソースに移動":::

1. 左側のナビゲーションで __[ネットワーク]__ を選択して、 __[プライベート エンドポイント接続]__ タブを選択し、 __[+ プライベート エンドポイント]__ を選択します。

    > [!NOTE]
    > 前の手順で BLOB ストレージ用のプライベート エンドポイントを作成しましたが、ファイル ストレージ用にも 1 つ作成する必要があります。

    :::image type="content" source="./media/tutorial-create-secure-workspace/storage-file-networking.png" alt-text="ストレージ アカウントのネットワークの UI":::

1. __[プライベート エンドポイントの作成]__ フォームで、前のリソースに使用した __サブスクリプション__、__リソース グループ__、および __リージョン__ を使用します。 一意の __名前__ を入力します。

    :::image type="content" source="./media/tutorial-create-secure-workspace/storage-file-private-endpoint.png" alt-text="ファイルのプライベート エンドポイントを追加する UI":::

1. __[次へ: リソース]__ を選択し、 __[対象サブリソース]__ を __[ファイル]__ に設定します。

    :::image type="content" source="./media/tutorial-create-secure-workspace/storage-file-private-endpoint-resource.png" alt-text="[ファイル] サブリソースの追加":::

1. __[次へ: 構成]__ を選択し、次の値を使用します。
    * __仮想ネットワーク__: 前に作成した仮想ネットワーク
    * __サブネット__: Training
    * __プライベート DNS ゾーンと統合する__: はい
    * __プライベート DNS ゾーン__: privatelink.file.core.windows.net

    :::image type="content" source="./media/tutorial-create-secure-workspace/storage-file-private-endpoint-config.png" alt-text="ファイルのプライベート エンドポイントを構成する UI":::

1. __[確認および作成]__ を選択します。 情報が正しいことを確認し、 __[作成]__ を選択します。

> [!TIP]
> パイプラインで [ParallelRunStep](./tutorial-pipeline-batch-scoring-classification.md) を使用する予定の場合は、プライベート エンドポイントの対象となる **キュー** と **テーブル** のサブリソースを構成することも必要になります。 ParallelRunStep は、タスクのスケジュール設定とディスパッチのために、内部でキューとテーブルを使用します。

## <a name="create-a-key-vault"></a>Key Vault を作成します

1. [Azure portal](https://portal.azure.com) で、左上隅にあるポータル メニューを選択します。 メニューで __[+ リソースの作成]__ を選択し、「__Key Vault__」と入力します。 __[Key Vault]__ エントリを選択し、 __[作成]__ を選択します。
1. __[基本]__ タブで、仮想ネットワークに対して前に使用した __サブスクリプション__、__リソース グループ__、および __リージョン__ を選択します。 一意の __キー コンテナー名__ を入力します。 他のフィールドは既定値のままにします。

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-key-vault.png" alt-text="新しいキー コンテナーの作成":::

1. __[ネットワーク]__ タブで __[プライベート エンドポイント]__ を選択し、 __[+ 追加]__ を選択します。

    :::image type="content" source="./media/tutorial-create-secure-workspace/key-vault-networking.png" alt-text="キー コンテナーのネットワーク":::

1. __[プライベート エンドポイントの作成]__ フォームで、次の値を使用します。
    * __サブスクリプション__: 前に作成したリソースを含むものと同じ Azure サブスクリプション。
    * __リソース グループ__: 前に作成したリソースを含むものと同じ Azure リソース グループ。
    * __場所__: 前に作成したリソースを含むものと同じ Azure リージョン。
    * __名前__: このプライベート エンドポイントの一意の名前。
    * __対象サブリソース__: ボールト
    * __仮想ネットワーク__: 前に作成した仮想ネットワーク。
    * __サブネット__: Training (172.17.0.0/24)
    * __プライベート DNS 統合__: はい
    * __プライベート DNS ゾーン__: privatelink.vaultcore.azure.net

    __[OK]__ を選択して、プライベート エンドポイントを作成します。

    :::image type="content" source="./media/tutorial-create-secure-workspace/key-vault-private-endpoint.png" alt-text="キー コンテナーのプライベート エンドポイントの構成":::

1. __[確認と作成]__ を選択します。 情報が正しいことを確認し、 __[作成]__ を選択します。

## <a name="create-a-container-registry"></a>コンテナー レジストリを作成する

1. [Azure portal](https://portal.azure.com) で、左上隅にあるポータル メニューを選択します。 メニューで __[+ リソースの作成]__ を選択し、「__Container Registry__」と入力します。 __[Container Registry]__ エントリを選択し、 __[作成]__ を選択します。
1. __[基本]__ タブで、仮想ネットワークに対して前に使用した __サブスクリプション__、__リソース グループ__、および __場所__ を選択します。 一意の __レジストリ名__ を入力し、 __[SKU]__ を __[Premium]__ に設定します。

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-container-registry.png" alt-text="コンテナー レジストリを作成する":::

1. __[ネットワーク]__ タブで __[プライベート エンドポイント]__ を選択し、 __[+ 追加]__ を選択します。

    :::image type="content" source="./media/tutorial-create-secure-workspace/container-registry-networking.png" alt-text="コンテナー レジストリのネットワーク":::

1. __[プライベート エンドポイントの作成]__ フォームで、次の値を使用します。
    * __サブスクリプション__: 前に作成したリソースを含むものと同じ Azure サブスクリプション。
    * __リソース グループ__: 前に作成したリソースを含むものと同じ Azure リソース グループ。
    * __場所__: 前に作成したリソースを含むものと同じ Azure リージョン。
    * __名前__: このプライベート エンドポイントの一意の名前。
    * __対象サブリソース__: registry (レジストリ)
    * __仮想ネットワーク__: 前に作成した仮想ネットワーク。
    * __サブネット__: Training (172.17.0.0/24)
    * __プライベート DNS 統合__: はい
    * __プライベート DNS ゾーン__: privatelink.azurecr.io

    __[OK]__ を選択して、プライベート エンドポイントを作成します。

    :::image type="content" source="./media/tutorial-create-secure-workspace/container-registry-private-endpoint.png" alt-text="コンテナー レジストリのプライベート エンドポイントの構成":::

1. __[確認と作成]__ を選択します。 情報が正しいことを確認し、 __[作成]__ を選択します。
1. コンテナー レジストリが作成されたら、 __[リソースに移動]__ を選択します。

    :::image type="content" source="./media/tutorial-create-secure-workspace/container-registry-go-to-resource.png" alt-text="[リソースに移動] の選択":::

1. ページの左側で、 __[アクセス キー]__ を選択し、 __[管理者ユーザー]__ を有効にします。 この設定は、仮想ネットワーク内で Azure Container Registry を Azure Machine Learning と共に使用する場合に必要です。

    :::image type="content" source="./media/tutorial-create-secure-workspace/container-registry-admin-user.png" alt-text="管理者ユーザーのトグルのスクリーンショット":::

## <a name="create-a-workspace"></a>ワークスペースの作成

1. [Azure portal](https://portal.azure.com) で、左上隅にあるポータル メニューを選択します。 メニューで __[+ リソースの作成]__ を選択し、「__Machine Learning__」と入力します。 __[Machine Learning]__ エントリを選択し、 __[作成]__ を選択します。

    :::image type="content" source="./media/tutorial-create-secure-workspace/machine-learning-create.png" alt-text="{alt-text}":::

1. __[基本]__ タブで、仮想ネットワークに対して前に使用した __サブスクリプション__、__リソース グループ__、および __リージョン__ を選択します。 その他のフィールドには次の値を使用します。
    * __ワークスペース名__: ワークスペースの一意の名前。
    * __ストレージ アカウント__: 前に作成したストレージ アカウントを選択します。
    * __キー コンテナー__: 前に作成したキー コンテナーを選択します。
    * __Application Insights__: 既定値を使用します。
    * __コンテナー レジストリ__: 前に作成したコンテナー レジストリを使用します。

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-machine-learning-workspace.png" alt-text="基本のワークスペースの構成":::

1. __[ネットワーク]__ タブで __[プライベート エンドポイント]__ を選択し、 __[+ 追加]__ を選択します。

    :::image type="content" source="./media/tutorial-create-secure-workspace/machine-learning-workspace-networking.png" alt-text="ワークスペースのネットワーク":::

1. __[プライベート エンドポイントの作成]__ フォームで、次の値を使用します。 
    * __サブスクリプション__: 前に作成したリソースを含むものと同じ Azure サブスクリプション。
    * __リソース グループ__: 前に作成したリソースを含むものと同じ Azure リソース グループ。
    * __場所__: 前に作成したリソースを含むものと同じ Azure リージョン。
    * __名前__: このプライベート エンドポイントの一意の名前。
    * __対象サブリソース__: amlworkspace
    * __仮想ネットワーク__: 前に作成した仮想ネットワーク。
    * __サブネット__: Training (172.17.0.0/24)
    * __プライベート DNS 統合__: はい
    * __プライベート DNS ゾーン__: 2 つのプライベート DNS ゾーンは、既定値の __privatelink.api.azureml.ms__ と __privatelink.notebooks.azure.net__ のままにします。

    __[OK]__ を選択して、プライベート エンドポイントを作成します。

    :::image type="content" source="./media/tutorial-create-secure-workspace/machine-learning-workspace-private-endpoint.png" alt-text="ワークスペースのプライベート ネットワークの構成のスクリーンショット":::

1. __[確認と作成]__ を選択します。 情報が正しいことを確認し、 __[作成]__ を選択します。
1. ワークスペースが作成されたら、 __[リソースに移動]__ を選択します。
1. 左側の __[設定]__ で __[プライベート エンドポイント接続]__ を選択し、 __[プライベート エンドポイント]__ 列のリンクを選択します。

    :::image type="content" source="./media/tutorial-create-secure-workspace/workspace-private-endpoint-connections.png" alt-text="ワークスペースのプライベート エンドポイント接続のスクリーンショット":::

1. プライベート エンドポイント情報が表示されたら、ページの左側にある __[DNS の構成]__ を選択します。 このページの IP アドレスと完全修飾ドメイン名 (FQDN) の情報は後で使用するため、保存します。

    :::image type="content" source="./media/tutorial-create-secure-workspace/workspace-private-endpoint-dns.png" alt-text="IP と FQDN のエントリのスクリーンショット":::

> [!IMPORTANT]
> ワークスペースを完全に使用するには、まだいくつかの構成手順が必要です。 ただし、これらを行うには、ワークスペースに接続する必要があります。

## <a name="enable-studio"></a>スタジオを有効にする

Azure Machine Learning スタジオは Web ベースのアプリケーションであり、これを使用すると、ワークスペースを簡単に管理できます。 ただし、VNet 内でセキュリティで保護されたリソースで使用するには、追加の構成が必要です。 次の手順を使用してスタジオを有効にします。

1. プライベート エンドポイントが設定された Azure Storage アカウントを使用する場合は、ストレージのプライベート エンドポイントの __[閲覧者]__ としてワークスペースのサービス プリンシパルを追加します。 Azure portal で、ご自分のストレージ アカウントを選択し、 __[ネットワーク]__ を選択します。 次に、 __[プライベート エンドポイント接続]__ を選択します。

    :::image type="content" source="./media/tutorial-create-secure-workspace/storage-private-endpoint-select.png" alt-text="ストレージのプライベート エンドポイントのスクリーンショット":::

1. __表示された各プライベート エンドポイント__ に対して、次の手順を使用します。

    1. __[プライベート エンドポイント]__ 列のリンクを選択します。
    
        :::image type="content" source="./media/tutorial-create-secure-workspace/storage-private-endpoint-selected.png" alt-text="選択するエンドポイントのスクリーンショット":::

    1. 左側から __[アクセス制御 (IAM)]__ を選択します。
    1. __[+ 追加]__ 、 __[ロールの割り当ての追加 (プレビュー)]__ の順に選択します。

        ![[ロールの割り当ての追加] メニューが開いている [アクセス制御 (IAM)] ページ。](../../includes/role-based-access-control/media/add-role-assignment-menu-generic.png)

    1. __[ロール]__ タブで、 __[閲覧者]__ を選択します。

        ![[ロール] タブが選択された [ロールの割り当ての追加] ページ。](../../includes/role-based-access-control/media/add-role-assignment-role-generic.png)

    1. __[メンバー]__ タブで、 __[アクセスの割り当て先]__ 領域で __[ユーザー、グループ、またはサービス プリンシパル]__ を選択し、 __[+ Select members]\(メンバーの選択\)__ を選択します。 __[Select members]\(メンバーの選択\)__ ダイアログで、Azure Machine Learning ワークスペースとしての名前を入力します。 ワークスペースのサービス プリンシパルを選択し、 __[選択]__ ボタンを使用します。

    1. **[確認と 割り当て]** タブで、 **[確認と割り当て]** を選択して ロールを割り当てます。

## <a name="connect-to-the-workspace"></a>ワークスペースに接続する

セキュリティで保護されたワークスペースに接続するには、いくつかの方法があります。 この記事の手順では、VNet 内の仮想マシンである __ジャンプ ボックス__ を使用します。 これに接続するには、Web ブラウザーと Azure Bastion を使用します。 次の表に、セキュリティで保護されたワークスペースに接続できるその他の方法をいくつか示します。

| 方法 | 説明 |
| ----- | ----- |
| [Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) | オンプレミスのネットワークをプライベート接続を介して VNet に接続します。 接続は、パブリック インターネットを介して行われます。 |
| [ExpressRoute](https://azure.microsoft.com/services/expressroute/) | オンプレミスのネットワークをプライベート接続を介してクラウドに接続します。 接続は、接続プロバイダーを使用して行われます。 |

> [!IMPORTANT]
> __VPN ゲートウェイ__ または __ExpressRoute__ を使用する場合は、オンプレミスのリソースと VNet 内のリソース間での名前解決の仕組みを計画する必要があります。 詳細については、[カスタム DNS サーバーの使用](how-to-custom-dns.md)に関する記事を参照してください。

### <a name="create-a-jump-box-vm"></a>ジャンプ ボックス (VM) を作成する

次の手順を使用して、ジャンプ ボックスとして使用する Data Science Virtual Machine を作成します。

1. [Azure portal](https://portal.azure.com) で、左上隅にあるポータル メニューを選択します。 メニューで __[+ リソースの作成]__ を選択し、「__Data science virtual machine__」と入力します。 __[Data science virtual machine - Windows]__ エントリを選択し、 __[作成]__ を選択します。
1. __[基本]__ タブで、仮想ネットワークに対して前に使用した __サブスクリプション__、__リソース グループ__、および __リージョン__ を選択します。 一意の __仮想マシン名__、__ユーザー名__、および __パスワード__ を入力します。 他のフィールドは既定値のままにします。

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-virtual-machine-basic.png" alt-text="VM の基本構成の画像":::

1. __[ネットワーク]__ を選択し、前に作成した __仮想ネットワーク__ を選択します。 次の情報を使用して、残りのフィールドを設定します。

    * __[Training]__ サブネットを選択します。
    * __[パブリック IP]__ を __[なし]__ に設定します。
    * 他のフィールドは既定値のままにします。

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-virtual-machine-network.png" alt-text="VM ネットワークの構成の画像":::

1. __[確認と作成]__ を選択します。 情報が正しいことを確認し、 __[作成]__ を選択します。


### <a name="connect-to-the-jump-box"></a>ジャンプ ボックスに接続する

1. 仮想マシンが作成されたら、 __[リソースに移動]__ を選択します。
1. ページの上部で、 __[接続]__ 、 __[Bastion]__ の順に選択します。

    :::image type="content" source="./media/tutorial-create-secure-workspace/virtual-machine-connect.png" alt-text="接続と Bastion の UI の画像":::

1. __[Bastion を使用する]__ を選択し、仮想マシンの認証情報を指定すると、ブラウザーで接続が確立されます。

    :::image type="content" source="./media/tutorial-create-secure-workspace/use-bastion.png" alt-text="[Bastion を使用する] ダイアログの画像":::

## <a name="create-a-compute-cluster-and-compute-instance"></a>コンピューティング クラスターとコンピューティング インスタンスを作成する

コンピューティング クラスターは、トレーニング ジョブで使用されます。 コンピューティング インスタンスは、ワークスペースに接続された共有コンピューティング リソースに対して Jupyter Notebook エクスペリエンスを提供します。

1. ジャンプ ボックスへの Azure Bastion 接続から、リモート デスクトップで __Microsoft Edge__ ブラウザーを開きます。
1. リモート ブラウザー セッションで、 __https://ml.azure.com__ に移動します。 ダイアログが表示されたら、ご自分の Azure AD アカウントを使用して認証します。
1. __[スタジオへようこそ]__ 画面で、前に作成した __Machine Learning ワークスペース__ を選択し、 __[作業の開始]__ を選択します。

    > [!TIP]
    > お使いの Azure AD アカウントが複数のサブスクリプションまたはディレクトリにアクセスできる場合は、 __[ディレクトリとサブスクリプション]__ ドロップダウンを使用して、ワークスペースを含むものを選択します。

    :::image type="content" source="./media/tutorial-create-secure-workspace/studio-select-workspace.png" alt-text="ワークスペースの選択ダイアログのスクリーンショット":::

1. スタジオで、 __[コンピューティング]__ 、 __[Compute clusters]\(コンピューティング クラスター\)__ 、 __[+ 新規]__ の順に選択します。

    :::image type="content" source="./media/tutorial-create-secure-workspace/studio-new-compute-cluster.png" alt-text="新しいコンピューティング クラスターのワークフローのスクリーンショット":::

1. __[仮想マシン]__ ダイアログで __[次へ]__ を選択して、既定の仮想マシン構成を受け入れます。

    :::image type="content" source="./media/tutorial-create-secure-workspace/studio-new-compute-vm.png" alt-text="コンピューティング クラスターの VM 設定のスクリーンショット":::
    
1. __[設定の構成]__ ダイアログで、 __[コンピューティング名]__ として「__cpu-cluster__」と入力します。 __[サブネット]__ を __[Training]__ に設定し、 __[作成]__ を選択してクラスターを作成します。

    > [!TIP]
    > コンピューティング クラスターは、必要に応じてクラスター内のノードを動的にスケーリングします。 クラスターが使用されていないときにコストを削減するために、最小ノード数を 0 のままにすることをお勧めします。

    :::image type="content" source="./media/tutorial-create-secure-workspace/studio-new-compute-settings.png" alt-text="新しいコンピューティング クラスターの設定のスクリーンショット":::

1. スタジオで、 __[コンピューティング]__ 、 __[Compute instances]\(コンピューティング インスタンス\)__ 、 __[+ 新規]__ の順に選択します。

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-compute-instance.png" alt-text="新しいコンピューティング インスタンスのワークフローのスクリーンショット":::

1. __[仮想マシン]__ ダイアログで、一意の __コンピューター名__ を入力し、 __[次へ: 詳細設定]__ を選択します。

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-compute-instance-vm.png" alt-text="コンピューティング インスタンスの VM 設定のスクリーンショット":::

1. __[詳細設定]__ ダイアログで、 __[サブネット]__ を __[Training]__ に設定し、 __[作成]__ を選択します。

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-compute-instance-settings.png" alt-text="コンピューティング インスタンスの設定のスクリーンショット":::

> [!TIP]
> コンピューティング クラスターまたはコンピューティング インスタンスを作成すると、Azure Machine Learning によってネットワーク セキュリティ グループ (NSG) が動的に追加されます。 この NSG には、コンピューティング クラスターとコンピューティング インスタンスに固有の次のルールが含まれています。
> 
> * `BatchNodeManagement` サービス タグからのポート 29876 から 29877 での受信 TCP トラフィックを許可します。
> * `AzureMachineLearning` サービス タグからのポート 44224 での受信 TCP トラフィックを許可します。
>
> 次のスクリーンショットは、これらのルールの例を示しています。
>
> :::image type="content" source="./media/how-to-secure-training-vnet/compute-instance-cluster-network-security-group.png" alt-text="NSG のスクリーンショット":::

コンピューティング クラスターとコンピューティング インスタンスの作成方法 (Python と CLI での実施方法を含む) の詳細については、次の記事を参照してください。

* [コンピューティング クラスターを作成する](how-to-create-attach-compute-cluster.md)
* [コンピューティング インスタンスを作成する](how-to-create-manage-compute-instance.md)

## <a name="configure-image-builds"></a>イメージのビルドを構成する

Azure Container Registry が仮想ネットワークの背後にある場合、Azure Machine Learning はそれを使用して Docker イメージ (トレーニングとデプロイに使用) を直接ビルドすることはできません。 代わりに、前に作成したコンピューティング クラスターを使用するようにワークスペースを構成します。 次の手順を使用して、コンピューティング クラスターを作成し、それを使用してイメージをビルドするようにワークスペースを構成します。

1. [https://shell.azure.com/](https://shell.azure.com/) にアクセスして、Azure Cloud Shell を開きます。
1. Cloud Shell から、次のコマンドを使用して、Azure Machine Learning 用の 1.0 CLI をインストールします。

    ```azurecli-interactive
    az extension add -n azure-cli-ml
    ```

1. コンピューティング クラスターを使用して Docker イメージをビルドするようにワークスペースを更新します。 `docs-ml-rg` は、お使いのリソース グループに置き換えます。 `docs-ml-ws` は、お使いのワークスペースに置き換えます。 `cpu-cluster` は、使用するコンピューティング クラスターに置き換えます。

    ```azurecli-interactive
    az ml workspace update -g docs-ml-rg -w docs-ml-ws --image-build-compute cpu-cluster
    ```

    > [!NOTE]
    > 同じコンピューティング クラスターを使用して、モデルをトレーニングし、ワークスペースの Docker イメージをビルドできます。

## <a name="use-the-workspace"></a>ワークスペースの使用

この時点で、スタジオを使用して、コンピューティング インスタンスでノートブックを対話的に操作し、コンピューティング クラスターでトレーニング ジョブを実行できます。 コンピューティング インスタンスとコンピューティング クラスターの使用方法に関するチュートリアルについては、「[Python スクリプトを実行する](tutorial-1st-experiment-hello-world.md)」を参照してください。

## <a name="stop-compute-instance-and-jump-box"></a>コンピューティング インスタンスとジャンプ ボックスを停止する

> [!WARNING]
> コンピューティング インスタンスとジャンプ ボックスは、実行している間 (開始済み)、サブスクリプションに課金され続けます。 過剰なコストを回避するため、それらを使用していない場合は __停止__ してください。

コンピューティング クラスターは、作成時に設定したノードの最小数と最大数の間で動的にスケーリングします。 既定値をそのまま使用した場合、最小値は 0 です。これにより、クラスターが使用されていないときには実質的にオフになります。
### <a name="stop-the-compute-instance"></a>コンピューティング インスタンスの停止

スタジオで、 __[コンピューティング]__ 、 __[Compute clusters]\(コンピューティング クラスター\)__ の順に選択し、コンピューティング インスタンスを選択します。 最後に、ページの上部で __[停止]__ を選択します。

:::image type="content" source="./media/tutorial-create-secure-workspace/compute-instance-stop.png" alt-text="コンピューティング インスタンスの [停止] ボタンのスクリーンショット":::
### <a name="stop-the-jump-box"></a>ジャンプ ボックスを停止する

一度作成した後は、Azure portal でその仮想マシンを選択し、 __[停止]__ ボタンを使用します。 再びこれを使用する準備ができたら、 __[開始]__ ボタンを押して開始します。

:::image type="content" source="./media/tutorial-create-secure-workspace/virtual-machine-stop.png" alt-text="VM の [停止] ボタンのスクリーンショット":::

また、ジャンプボックスを、特定の時刻に自動的にシャットダウンするように構成することもできます。 そのためには、 __[自動シャットダウン]__ 、 __[有効化]__ を選択し、時刻を設定してから、 __[保存]__ を選択します。

:::image type="content" source="./media/tutorial-create-secure-workspace/virtual-machine-auto-shutdown.png" alt-text="自動シャットダウンのオプションのスクリーンショット":::

## <a name="clean-up-resources"></a>リソースのクリーンアップ

セキュリティで保護されたワークスペースおよびその他のリソースを引き続き使用する予定の場合は、このセクションをスキップしてください。

このチュートリアルで作成したすべてのリソースを削除するには、次の手順を使用します。

1. Azure Portal で、左端にある __[リソース グループ]__ を選択します。
1. 一覧から、このチュートリアルで作成したリソース グループを選択します。
1. __[リソース グループの削除]__ を選択します。

    :::image type="content" source="./media/tutorial-create-secure-workspace/delete-resources.png" alt-text="[リソース グループ削除] ボタンのスクリーンショット":::

1. リソース グループ名を入力し、 __[削除]__ を選択します。
## <a name="next-steps"></a>次のステップ

セキュリティで保護されたワークスペースを作成し、スタジオにアクセスできるようになったので、Azure Machine Learning を使用して [Python スクリプトを実行する](tutorial-1st-experiment-hello-world.md)方法について学習します。
