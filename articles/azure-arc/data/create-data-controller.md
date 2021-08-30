---
title: データ コントローラーの作成
description: 既にデプロイされている一般的なマルチノード Kubernetes クラスターに Azure Arc データ コントローラーを作成します。
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 74c41ed89a3980967532955e46d4dade9eaacdb1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121728045"
---
# <a name="create-the-azure-arc-data-controller"></a>Azure Arc データ コントローラーを作成する


## <a name="overview-of-creating-the-azure-arc-data-controller"></a>Azure Arc データ コントローラーの作成に関する概要

Azure Arc 対応データ サービスは、複数の異なる種類の Kubernetes クラスター、および複数の異なる方法を使用してマネージド Kubernetes サービスに作成できます。

現時点でサポートされている Kubernetes サービスとディストリビューションの一覧は次のとおりです。

- Azure Kubernetes Service (AKS)
- Azure Stack HCI の Azure Kubernetes Service
- Azure RedHat OpenShift (ARO)
- OpenShift Container Platform (OCP)
- AWS Elastic Kubernetes Service (EKS)
- Google Kubernetes Engine (GKE)
- Kubeadm を使用して通常デプロイされるオープンソースのアップストリーム Kubernetes

> [!IMPORTANT]
> * Kubernetes のサポートされている最小バージョンは 1.17 です。 追加情報については、[既知の問題](./release-notes.md#known-issues)を参照してください。 
> * サポートされる OCP の最小バージョンは 4.5 です。
> * 環境と Azure の間に必要な接続については、「[接続要件](connectivity.md)」を参照してください。
> * 永続ストレージを構成する方法の詳細については、「[ストレージ構成ガイダンス](storage-configuration.md)」を参照してください。
> * Azure Kubernetes Service を使用している場合は、クラスターのワーカー ノードの VM サイズが少なくとも **Standard_D8s_v3** であり、**Premium ディスク** を使用している必要があります。 クラスターは複数の可用性ゾーンにまたがることはできません。 
> * 別の Kubernetes ディストリビューションまたはサービスを使用している場合は、最小ノード サイズとして 8 GB RAM および 4 コアが必要であり、かつすべての Kubernetes ノードで使用可能な容量として合計 32 GB RAM を確保する必要があります。 たとえば、32 GB RAM と 4 コアのノードを 1 つ使用することも、それぞれが 16 GB RAM と 4 コアのノードを 2 つ使用することもできます。

> [!NOTE]
> Azure で Red Hat OpenShift Container Platform を使用している場合は、利用できる中で最新のバージョンを使用することをお勧めします。

選択するオプションによっては、特定のツールが "_必要_" になりますが、Azure Arc データ コントローラーの作成を開始する前に、[すべてのクライアント ツール](./install-client-tools.md)をインストールしておくことをお勧めします。

選択するオプションに関わりなく、作成プロセス中に次の情報を指定する必要があります。

- **データ コントローラー名** - データ コントローラーのわかりやすい名前 (例: "Production data controller"、"Seattle data controller" など)。
- **データ コントローラーのユーザー名** - データ コントローラー管理者ユーザーの任意のユーザー名。
- **データ コントローラーのパスワード** - データ コントローラー管理者ユーザーのパスワード。
- **Kubernetes 名前空間の名前** - データ コントローラーを作成する Kubernetes 名前空間の名前。
- **接続モード** - 接続モードによって、Azure Arc 対応データ サービス環境から Azure への接続の程度が決定されます。 間接接続モードが一般提供されています。 直接接続モードはプレビュー段階です。  詳細については、[接続モード](./connectivity.md)に関するページを参照してください。 
- **Azure サブスクリプション ID** - Azure 内のデータ コントローラー リソースを作成する場所の Azure サブスクリプション GUID。
- **Azure リソース グループ名** - Azure 内のデータ コントローラー リソースを作成するリソース グループの名前。
- **Azure の場所** - Azure でデータ コントローラー リソース メタデータが格納される Azure の場所。 利用可能なリージョンの一覧については、「[Azure グローバル インフラストラクチャ/リージョン別の製品](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)」を参照してください。 デプロイしているデータ コントローラーにより管理される Azure リソースに関するメタデータと課金情報は、場所パラメーターとして指定した Azure 内の場所だけに保存されます。 直接接続モードでデプロイしている場合、データ コントローラーの場所パラメーターは、対象とするカスタム場所リソースの場所と同じになります。

## <a name="next-steps"></a>次の手順

次のように、Azure Arc データ コントローラーを作成するためのオプションは複数あります。

> **試してみたい場合**  
> Azure Kubernetes Service (AKS)、AWS Elastic Kubernetes Service (EKS)、Google Cloud Kubernetes Engine (GKE)、または Azure VM 上で [Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) をすぐに開始できます。
> 
- [CLI を使用して間接接続モードでデータ コントローラーを作成する](create-data-controller-indirect-cli.md)
- [Azure Data Studio を使用して間接接続モードでデータ コントローラーを作成する](create-data-controller-indirect-azure-data-studio.md)
- [Azure Data Studio で Jupyter ノートブックを使用して、データ コントローラーを Azure portal から間接接続モードで作成する](create-data-controller-indirect-azure-portal.md)
- [Kubectl や oc などの Kubernetes ツールを使用して、データ コントローラーを間接接続モードで作成する](create-data-controller-using-kubernetes-native-tools.md)
- [データ コントローラーを直接接続モードで作成する](create-data-controller-direct-prerequisites.md)
- [テスト デプロイのエクスペリエンスを向上させるために Azure Arc Jumpstart でデータ コントローラーを作成する](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/)
