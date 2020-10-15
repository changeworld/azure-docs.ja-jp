---
title: Microsoft Azure Stack Edge Pro デバイスでの Kubernetes クラスターの概要 | Microsoft Docs
description: ご利用の Azure Stack Edge Pro デバイスに Kubernetes を実装する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 089476980ff3e8b2d3b72cb2d229934cf7850c03
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "90899250"
---
# <a name="kubernetes-on-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU デバイスでの Kubernetes

Kubernetes はコンテナー化されたアプリケーションを調整するための一般的なオープンソース プラットフォームです。 この記事では、Kubernetes の概要を説明し、さらに Azure Stack Edge Pro デバイス上で Kubernetes がどのように機能するかについて説明します。 

## <a name="about-kubernetes"></a>Kubernetes について 

Kubernetes とは、コンテナーベースのアプリケーションとそれに関連するネットワークおよびストレージのコンポーネントを管理するための簡単で信頼性の高いプラットフォームです。 Kubernetes を使用すれば、コンテナー化されたアプリを迅速にビルド、配信、スケーリングすることができます。

オープン プラットフォームとして Kubernetes を使用すれば、使いたいプログラミング言語、OS ライブラリ、またはメッセージング バスでアプリケーションを開発することができます。 リリースをスケジュール設定してデプロイする場合は、Kubernetes を既存の継続的インテグレーションおよび継続的デリバリー ツールと連携させることができます。

詳細については、[Kubernetes のしくみ](https://www.youtube.com/watch?v=q1PcAawa4Bg&list=PLLasX02E8BPCrIhFrc_ZiINhbRkYMKdPT&index=2&t=0s)に関するページを参照してください。

## <a name="kubernetes-on-azure-stack-edge-pro"></a>Azure Stack Edge Pro での Kubernetes

Azure Stack Edge Pro デバイス上で、Kubernetes クラスターを作成するにはコンピューティングを構成します。 コンピューティング ロールが構成されると、マスターおよびワーカー ノードを含む Kubernetes クラスターはすべて自動的にデプロイおよび構成されます。 その後、このクラスターは、`kubectl`、IoT Edge、または Azure Arc を介したワークロードのデプロイに使用されます。

Azure Stack Edge Pro デバイスは、インフラストラクチャ クラスターを構成する 1 ノード構成として使用できます。 Kubernetes クラスターはインフラストラクチャ クラスターから分離され、インフラストラクチャ クラスターの上にデプロイされます。 インフラストラクチャ クラスターは、Azure Stack Edge Pro デバイス用の永続的なストレージを提供します。一方、Kubernetes クラスターはアプリケーション オーケストレーション専用となります。 

この場合の Kubernetes クラスターには、マスター ノードとワーカー ノードがあります。 クラスター内の Kubernetes ノードは、ご利用のアプリケーションとクラウド ワークフローを実行する仮想マシンです。 

Kubernetes マスター ノードは、ご利用のクラスターにとって望ましい状態を維持する役割を担います。 マスター ノードではさらに、コンテナー化されたアプリケーションを実行するワーカー ノードも制御されます。 

次の図に、1 ノードの Azure Stack Edge Pro デバイスへの Kubernetes の実装を示します。 1 ノード デバイスは可用性が高くないため、1 つのノードで障害が発生した場合、デバイスはダウンします。 Kubernetes クラスターもダウンします。

![1 ノードの Azure Stack Edge Pro デバイスの Kubernetes アーキテクチャ](media/azure-stack-edge-gpu-kubernetes-overview/kubernetes-architecture-1-node.png)

Kubernetes クラスター アーキテクチャの詳細については、[Kubernetes の主要な概念](https://kubernetes.io/docs/concepts/architecture/)に関するドキュメントを参照してください。


<!--The Kubernetes cluster control plane components make global decisions about the cluster. The control plane has:

- *kubeapiserver* that is the front end of the Kubernetes API and exposes the API.
- *etcd* that is a highly available key value store that backs up all the Kubernetes cluster data.
- *kube-scheduler* that makes scheduling decisions.
- *kube-controller-manager* that runs controller processes such as those for node controllers, replications controllers, endpoint controllers, and service account and token controllers. -->

## <a name="storage-volume-provisioning"></a>ストレージ ボリュームのプロビジョニング

アプリケーション ワークロードをサポートするために、ご利用の Azure Stack Edge Pro デバイス共有上の永続的なデータ用にストレージ ボリュームをマウントできます。 静的ボリュームと動的ボリュームの両方を使用できます。 

詳細については、[Azure Stack Edge Pro デバイス用の Kubernetes ストレージ](azure-stack-edge-gpu-kubernetes-storage.md)に関するページに記載されているアプリケーション用のストレージ プロビジョニング オプションを参照してください。

## <a name="networking"></a>ネットワーク

Kubernetes ネットワークを使用すると、ご利用の Kubernetes ネットワーク内で通信を構成できます。これには、コンテナー間のネットワーク、ポッド間のネットワーク、ポッド対サービスのネットワーク、インターネット対サービスのネットワークなどが含まれます。 詳細については、[Azure Stack Edge Pro デバイス用の Kubernetes ネットワーク](azure-stack-edge-gpu-kubernetes-networking.md)に関するページにあるネットワーク モデルを参照してください。

## <a name="updates"></a>更新プログラム

新しい Kubernetes バージョンが利用可能になると、ご利用の Azure Stack Edge Pro デバイスに提供される標準の更新プログラムを使用してクラスターをアップグレードできます。 アップグレード方法の手順については、[Azure Stack Edge Pro の更新プログラムの適用](azure-stack-edge-gpu-install-update.md)に関するページを参照してください。

## <a name="access-monitoring"></a>アクセス、監視

ご利用の Azure Stack Edge Pro デバイス上の Kubernetes クラスターに対してロールベースのアクセス制御 (RBAC) を行うことができます。 詳細については、[Azure Stack Edge Pro デバイス上の Kubernetes クラスターに対するロールベースのアクセス制御](azure-stack-edge-gpu-kubernetes-rbac.md)に関するページを参照してください。

また、Kubernetes ダッシュボードを介して、クラスターとリソースの正常性を監視することもできます。 コンテナーのログも利用できます。 詳細については、[Kubernetes ダッシュボードを使用して Azure Stack Edge Pro デバイス上の Kubernetes クラスターの正常性の監視](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md)に関するページを参照してください。

Azure Monitor は、コンテナー、ノード、およびコントローラーから正常性データを収集するためのアドオンとしても利用できます。 詳細については、「[Azure Monitor の概要](../azure-monitor/overview.md)」を参照してください。

<!--## Private container registry

Kubernetes on Azure Stack Edge Pro device allows for the private storage of your images by providing a local container registry.-->

## <a name="application-management"></a>アプリケーション管理

ご利用の Azure Stack Edge Pro デバイスに Kubernetes クラスターが作成されたら、次のいずれかの方法を使用して、このクラスターにデプロイされたアプリケーションを管理できます。

- `kubectl` を介したネイティブ アクセス
- IoT Edge 
- Azure Arc

これらの方法については、以下のセクションで説明します。


### <a name="kubernetes-and-kubectl"></a>Kubernetes と kubectl

Kubernetes クラスターがデプロイされると、クラスターにデプロイされたアプリケーションをクライアント コンピューターからローカルで管理できます。 アプリケーションとやりとりするには、コマンド ラインを介して *kubectl* などのネイティブ ツールを使用します。 

Kubernetes クラスターのデプロイの詳細については、[Azure Stack Edge Pro デバイスへの Kubernetes クラスターのデプロイ](azure-stack-edge-gpu-create-kubernetes-cluster.md)に関するページを参照してください。 管理の詳細については、[kubectl を使用して Azure Stack Edge Pro デバイス上で Kubernetes クラスターを管理する](azure-stack-edge-gpu-create-kubernetes-cluster.md)方法に関するページを参照してください。


### <a name="kubernetes-and-iot-edge"></a>Kubernetes と IoT Edge

また、Kubernetes を Azure Stack Edge Pro デバイス上の IoT Edge ワークロードに統合することもできます。この場合、Kubernetes によってスケールとエコシステムが提供され、IoT によって IoT 中心のエコシステムが提供されます。 Kubernetes レイヤーは、Azure IoT Edge ワークロードをデプロイするためのインフラストラクチャ レイヤーとして使用されます。 モジュールの有効期間とネットワーク負荷分散が Kubernetes によって管理され、エッジ アプリケーション プラットフォームが IoT Edge によって管理されます。

IoT Edge を介した Kubernetes クラスターへのアプリケーションのデプロイの詳細については、次のページを参照してください。 

- [IoT Edge を介して Azure Stack Edge Pro デバイス上でステートレス アプリケーションを公開する](azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module.md)。


### <a name="kubernetes-and-azure-arc"></a>Kubernetes と Azure Arc

Azure Arc は、Kubernetes クラスターにアプリケーションをデプロイするためのハイブリッド管理ツールです。 また、Azure Arc を使用すると、コンテナーに対して Azure Monitor を使用して、ご利用のクラスターを表示および監視することもできます。 詳細については、[Azure Arc 対応 Kubernetes とは](https://docs.microsoft.com/azure/azure-arc/kubernetes/overview)に関するページを参照してください。 Azure Arc の価格については、[Azure Arc の価格](https://azure.microsoft.com/services/azure-arc/#pricing)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

- [Azure Stack Edge Pro デバイス](azure-stack-edge-gpu-kubernetes-storage.md)上の Kubernetes ストレージの詳細について説明します。
- [Azure Stack Edge Pro デバイス](azure-stack-edge-gpu-kubernetes-networking.md)上の Kubernetes ネットワーク モデルについて理解します。
- Azure portal に [Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md) をデプロイします。
