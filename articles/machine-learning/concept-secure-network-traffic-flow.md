---
title: セキュリティで保護されたネットワーク トラフィック フロー
titleSuffix: Azure Machine Learning
description: Azure Machine Learning ワークスペースがセキュリティで保護された仮想ネットワーク内にある場合に、コンポーネント間のネットワーク トラフィックがどのように流れるかについて学習します。
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: conceptual
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 11/09/2021
ms.openlocfilehash: d71d986ca975b9617af9b966c8795cd30d7db01d
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132134940"
---
# <a name="network-traffic-flow-when-using-a-secured-workspace"></a>セキュリティで保護されたワークスペースを使用する場合のネットワーク トラフィック フロー

Azure Machine Learning ワークスペースと関連付けられているリソースが Azure Virtual Network で保護されている場合は、リソース間のネットワーク トラフィックが変わります。 仮想ネットワークを使用しない場合、ネットワーク トラフィックはパブリック インターネット経由で、あるいは Azure データ センター内で流れます。 仮想ネットワーク (VNet) が導入されたら、ネットワーク セキュリティを強化することもできます。 たとえば、VNet とパブリック インターネット間の受信と送信の通信をブロックします。 しかし、Azure Machine Learning では、パブリック インターネット上の一部のリソースへのアクセスが必要となります。 たとえば、デプロイと管理操作には Azure Resource Management が使用されます。

この記事では、パブリック インターネットとの間で必要なトラフィックをリストします。 また、次のシナリオにおいて、クライアントの開発環境とセキュリティで保護された Azure Machine Learning ワークスペースの間のネットワーク トラフィックがどのように流れるかについて説明します。

* Azure Machine Learning __スタジオ__ を使用して、以下を操作する:

    * ワークスペース
    * AutoML
    * Designer
    * データセットとデータストア

    > [!TIP]
    > Azure Machine Learning スタジオは、Web ブラウザーで部分的に実行される Web ベースの UI であり、Azure サービスへの呼び出しを行って、モデルのトレーニング、デザイナーの使用、データセットの表示などのタスクを実行します。 これらの呼び出しには、SDK、CLI、REST API、VS Code を使用する場合とは異なる通信フローを使用するものがあります。

* Azure Machine Learning __スタジオ__、__SDK__、__CLI__、または __REST API__ を使用して、以下を操作する:

    * コンピューティング インスタンスとクラスター
    * Azure Kubernetes Service
    * Azure Machine Learning によって管理される Docker イメージ

> [!TIP]
> シナリオまたはタスクがここにリストされていない場合は、セキュリティで保護されたワークスペースの有無にかかわらず、同じように動作するはずです。

## <a name="assumptions"></a>前提条件

この記事では、以下の構成を前提としています。

* VNet との通信にプライベート エンドポイントを使用する Azure Machine Learning ワークスペース。
* また、ワークスペースで使用される Azure Storage アカウント、Key Vault、および Container Registry では、プライベート エンドポイントを使って VNet と通信を行います。
* VPN ゲートウェイまたは ExpressRoute は、VNet にアクセスするためにクライアント ワークステーションで使用されます。

## <a name="inbound-and-outbound-requirements"></a>受信と送信の要件


| __シナリオ__ | __必要な受信__ | __必要な送信__ | __追加構成__ | 
| ----- | ----- | ----- | ----- |
| [スタジオからワークスペースにアクセスする](#scenario-access-workspace-from-studio) | NA | <ul><li>Azure Active Directory</li><li>Azure Front Door</li><li>Azure Machine Learning service</li></ul> | 場合によっては、カスタム DNS サーバーを使用する必要があります。 詳細については、[カスタム DNS でのワークスペースの使用](how-to-custom-dns.md)に関するページを参照してください。 | 
| [スタジオから AutoML、デザイナー、データセット、およびデータストアを使用する](#scenario-use-automl-designer-dataset-and-datastore-from-studio) | NA | NA | <ul><li>ワークスペース サービス プリンシパルの構成</li><li>信頼された Azure サービスからのアクセスを許可する</li></ul>詳細については、[仮想ネットワーク内のワークスペースをセキュリティで保護する方法](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts)に関するページを参照してください。 | 
| [コンピューティング インスタンスとコンピューティング クラスターを使用する](#scenario-use-compute-instance-and-compute-cluster) | <ul><li>Azure Machine Learning service (ポート 44224)</li><li>Azure Batch 管理サービス (ポート 29876 から 29877)</li></ul> | <ul><li>Azure Active Directory</li><li>Azure Resource Manager</li><li>Azure Machine Learning service</li><li>Azure Storage アカウント</li><li>Azure Key Vault</li></ul> | ファイアウォールを使用する場合は、ユーザー定義のルートを作成します。 詳細については、[受信および送信トラフィックの構成](how-to-access-azureml-behind-firewall.md)に関するページを参照してください。 | 
| [Azure Kubernetes Service を使用する](#scenario-use-azure-kubernetes-service) | NA | AKS の送信構成については、[Azure Kubernetes Service にデプロイする方法](how-to-deploy-azure-kubernetes-service.md#understand-connectivity-requirements-for-aks-inferencing-cluster)に関するページを参照してください。 | 内部ロード バランサーを構成します。 詳細については、[Azure Kubernetes Service にデプロイする方法](how-to-deploy-azure-kubernetes-service.md#understand-connectivity-requirements-for-aks-inferencing-cluster)に関するページを参照してください。 | 
| [Azure Machine Learning によって管理される Docker イメージを使用する](#scenario-use-docker-images-managed-by-azure-ml) | NA | <ul><li>Microsoft Container Registry</li><li>`viennaglobal.azurecr.io` グローバル コンテナー レジストリ</li></ul> | ワークスペースの Azure Container Registry が VNet の背後にある場合は、コンピューティング クラスターを使用してイメージを作成するようにワークスペースを構成します。 詳細については、[仮想ネットワーク内のワークスペースをセキュリティで保護する方法](how-to-secure-workspace-vnet.md#enable-azure-container-registry-acr)に関するページを参照してください。 | 

> [!IMPORTANT]
> Azure Machine Learning は複数のストレージ アカウントを使用します。 それぞれが異なるデータを格納し、目的も異なります。
>
> * __ご使用のストレージ__: Azure サブスクリプションの Azure Storage アカウントを使用して、データと成果物 (モデル、トレーニング データ、トレーニング ログ、Python スクリプトなど) を格納します。 たとえば、ワークスペースの _デフォルト_ のストレージ アカウントは、サブスクリプションに含まれています。 Azure Machine Learning のコンピューティング インスタンスとコンピューティング クラスターは、ポート 445 (SMB) と 443 (HTTPS) を介して、このストレージ内の __ファイル__ と __BLOB__ データにアクセスします。
> 
>    コンピューティング インスタンスまたはコンピューティング クラスターを使用する場合、ストレージ アカウントは、SMB プロトコルを使用してファイル共有としてマウントされます。 コンピューティングインスタンス/クラスターがデータにアクセスする方法は次のようになります。
>
> * __Microsoft ストレージ__: Azure Machine Learning コンピューティング インスタンスとコンピューティング クラスターは、Azure Batch に依存し、Microsoft サブスクリプションにあるストレージにアクセスします。 このストレージは、コンピューティング インスタンス/クラスターの管理にのみ使用されます。 ここにはデータが格納されていません。 コンピューティング インスタンスとコンピューティング クラスターは、ポート 443 (HTTPS) を使用して、このストレージ内の __BLOB__、__テーブル__、__キュー__ のデータにアクセスします。
>
> Azure Machine Learning では、メタデータは Azure Cosmos DB インスタンスにも格納されます。 既定では、このインスタンスは Microsoft サブスクリプションでホストされ、Microsoft によって管理されます。 必要に応じて、Azure サブスクリプションで Azure Cosmos DB インスタンスを使用できます。 詳細については、[Azure Machine Learning を使用したデータの暗号化](concept-data-encryption.md#azure-cosmos-db)に関するページを参照してください。

## <a name="scenario-access-workspace-from-studio"></a>シナリオ: スタジオからワークスペースにアクセスする

> [!NOTE]
> このセクションの情報は、Azure Machine Learning スタジオからのワークスペースの使用に固有のものです。 Azure Machine Learning SDK、REST API、CLI、または Visual Studio Code を使用する場合、このセクションの情報は適用されません。

スタジオからワークスペースにアクセスする場合、ネットワーク トラフィック フローは次のようになります。

* リソースに対する認証には、__Azure Active Directory__ が使用されます。
* 管理およびデプロイ操作では、__Azure Resource Manager__ が使用されます。
* Azure Machine Learning 固有のタスクでは、__Azure Machine Learning service__ が使用されます
* Azure Machine Learning スタジオへのアクセス (https://ml.azure.com) ) では、__Azure FrontDoor__ が使用されます。
* ほとんどのストレージ操作では、トラフィックは、ワークスペース用の既定のストレージのプライベート エンドポイントを経由して流れます。 例外については、「[AutoML、デザイナー、データセット、およびデータストアを使用する](#scenario-use-automl-designer-dataset-and-datastore-from-studio)」セクションで説明します。
* また、VNet 内のリソースの名前を解決できるようにする DNS ソリューションを構成する必要もあります。 詳細については、[カスタム DNS でのワークスペースの使用](how-to-custom-dns.md)に関するページを参照してください。

:::image type="content" source="./media/concept-secure-network-traffic-flow/workspace-traffic-studio.png" alt-text="スタジオを使用する場合のクライアントとワークスペースの間のネットワーク トラフィックの図":::

## <a name="scenario-use-automl-designer-dataset-and-datastore-from-studio"></a>シナリオ: スタジオから AutoML、デザイナー、データセット、およびデータストアを使用する

Azure Machine Learning スタジオの次の機能では、''_データ プロファイル_'' が使用されます。

* データセット: スタジオからデータセットを調べます。
* デザイナー: モジュールの出力データを視覚化します。
* AutoML: データ プレビューまたはプロファイルを表示し、ターゲット列を選択します。
* ラベル付け

データ プロファイルは、ワークスペース用の既定の Azure Storage アカウントにアクセスできる Azure Machine Learning マネージド サービスに依存します。 マネージド サービスは ''_VNet に存在しない_'' ため、VNet 内のストレージ アカウントに直接アクセスすることはできません。 代わりに、ワークスペースではサービス プリンシパルを使用してストレージにアクセスします。

> [!TIP]
> サービス プリンシパルは、ワークスペースを作成するときに指定できます。 そうしないと、自動的に作成され、ワークスペースと同じ名前になります。

ストレージ アカウントへのアクセスを許可するには、ワークスペースに __リソース インスタンス__ を許可するようにストレージ アカウントを構成するか、 __[信頼されたサービスの一覧にある Azure サービスがこのストレージ アカウントにアクセスすることを許可します]__ を選択します。 この設定により、マネージド サービスは Azure データ センター ネットワークを介してストレージにアクセスできるようになります。 

次に、ワークスペースのサービス プリンシパルを、ストレージ アカウントのプライベート エンドポイントに対する __閲覧者__ ロールに追加します。 このロールは、ワークスペースとストレージ サブネットの情報を確認するために使用されます。 それらが同じである場合は、アクセスが許可されます。 最後に、サービス プリンシパルには、ストレージ アカウントへの __Blob データ共同作成者__ のアクセスも必要です。

詳細については、[仮想ネットワーク内のワークスペースをセキュリティで保護する方法](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts)に関するページの「Azure Storage アカウント」セクションを参照してください。

:::image type="content" source="./media/concept-secure-network-traffic-flow/storage-traffic-studio.png" alt-text="クライアント、データ プロファイル、およびストレージ間のトラフィックの図":::

## <a name="scenario-use-compute-instance-and-compute-cluster"></a>シナリオ: コンピューティング インスタンスとコンピューティング クラスターを使用する

Azure Machine Learning コンピューティング インスタンスとコンピューティング クラスターは、Microsoft によってホストされるマネージド サービスです。 それらは Azure Batch サービス上に構築されます。 Microsoft が管理する環境に存在する場合は、VNet にも挿入されます。

コンピューティング インスタンスまたはコンピューティング クラスターを作成すると、次のリソースも VNet に作成されます。

* 必要な送信規則を使用するネットワーク セキュリティ グループ。 これらの規則では、Azure Machine Learning (ポート 44224 の TCP) および Azure Batch サービス (ポート 29876 から 29877 の TCP) からの __受信__ アクセスが許可されます。

    > [!IMPORTANT]
    > ファイアウォールを使用して VNet へのインターネット アクセスをブロックする場合は、このトラフィックを許可するようにファイアウォールを構成する必要があります。 たとえば、Azure Firewall を使用して、ユーザー定義のルートを作成できます。 詳細については、[ファイアウォールで Azure Machine Learning を使用する方法](how-to-access-azureml-behind-firewall.md#inbound-configuration)に関するページを参照してください。

* パブリック IP を使用するロード バランサー。

また、次のサービス タグへの __送信__ アクセスを許可します。 各タグについて、`region` をコンピューティング インスタンスまたはクラスターの Azure リージョンに置き換えます。

* `Storage.region` - この送信アクセスは、Azure Batch サービスで管理されている VNet 内の Azure Storage アカウントに接続するために使用されます。
* `Keyvault.region` - この送信アクセスは、Azure Batch サービスで管理されている VNet 内の Azure Key Vault アカウントに接続するために使用されます。

コンピューティング インスタンスまたはクラスターからのデータ アクセスは、VNet 用のストレージ アカウントのプライベート エンドポイントを経由します。

コンピューティング インスタンスで Visual Studio Code を使用する場合は、他の送信トラフィックを許可する必要があります。 詳細については、[ファイアウォールで Azure Machine Learning を使用する方法](how-to-access-azureml-behind-firewall.md)に関するページを参照してください。

:::image type="content" source="./media/concept-secure-network-traffic-flow/compute-instance-and-cluster.png" alt-text="コンピューティング インスタンスまたはクラスターを使用する場合のトラフィック フローの図":::

## <a name="scenario-use-azure-kubernetes-service"></a>シナリオ: Azure Kubernetes Service を使用する

Azure Kubernetes Service に必要な送信構成については、[Azure Kubernetes Service にデプロイする方法](how-to-deploy-azure-kubernetes-service.md#understand-connectivity-requirements-for-aks-inferencing-cluster)に関するページの接続要件セクションを参照してください。

> [!NOTE]
> Azure Kubernetes Service のロード バランサーは、Azure Machine Learning によって作成されるロード バランサーと同じではありません。 モデルをセキュリティで保護されたアプリケーションとしてホストし、VNet でのみ使用できるようにする場合は、Azure Machine Learning によって作成される内部ロード バランサーを使用します。 パブリック アクセスを許可する場合は、Azure Machine Learning によって作成されるパブリック ロード バランサーを使用します。

モデルで外部データ ソースなどへの追加の受信または送信接続が必要な場合は、ネットワーク セキュリティ グループまたはファイアウォールを使用してトラフィックを許可します。

## <a name="scenario-use-docker-images-managed-by-azure-ml"></a>シナリオ: Azure ML によって管理される Docker イメージを使用する

Azure Machine Learning では、モデルのトレーニングや推論の実行に使用できる Docker イメージが提供されます。 独自のイメージを指定しない場合は、Azure Machine Learning によって提供されるものが使用されます。 これらのイメージは、Microsoft Container Registry (MCR) でホストされます。 また、それらは、`viennaglobal.azurecr.io` という名前の geo レプリケーション Azure Container Registry でホストされます。

自分で指定した Azure Container Registry など、独自の docker イメージを指定する場合は、MCR や `viennaglobal.azurecr.io` との送信通信は必要ありません。

> [!TIP]
> Azure Container Registry が VNet でセキュリティ保護されている場合、Azure Machine Learning で Docker イメージをビルドするために使用することはできません。 代わりに、イメージをビルドするために Azure Machine Learning コンピューティング クラスターを指定する必要があります。 詳細については、[仮想ネットワーク内のワークスペースをセキュリティで保護する方法](how-to-secure-workspace-vnet.md#enable-azure-container-registry-acr)に関するページを参照してください。

:::image type="content" source="./media/concept-secure-network-traffic-flow/azure-machine-learning-docker-images.png" alt-text="提供された Docker イメージを使用する場合のトラフィック フローの図":::
## <a name="next-steps"></a>次のステップ

ここまでで、セキュリティで保護された構成でのネットワーク トラフィックの流れについて学習しました。次は、「[仮想ネットワークの分離とプライバシーの概要](how-to-network-security-overview.md)」の記事を読んで、仮想ネットワークでの Azure ML のセキュリティ保護について詳しく学習します。

ベスト プラクティスについては、「[Azure Machine Learning のエンタープライズ セキュリティに関するベスト プラクティス](/azure/cloud-adoption-framework/ready/azure-best-practices/ai-machine-learning-enterprise-security)」の記事を参照してください。