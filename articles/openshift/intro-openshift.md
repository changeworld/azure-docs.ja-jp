---
title: Azure Red Hat OpenShift の概要
description: コンテナーベースのアプリケーションをデプロイして管理するための、Microsoft Azure Red Hat OpenShift の機能とメリットについて説明します。
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: overview
ms.date: 05/08/2019
ms.custom: mvc
ms.openlocfilehash: 4be388eec3851154fd0f6af37d03a468fc52197b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2020
ms.locfileid: "76276073"
---
# <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Microsoft *Azure Red Hat OpenShift* サービスを使用すると、フル マネージドの [OpenShift](https://www.openshift.com/) クラスターをデプロイできます。

Azure Red Hat OpenShift は [Kubernetes](https://kubernetes.io/) を拡張します。 Kubernetes を使用した運用環境でコンテナーを実行するには、イメージ レジストリ、ストレージ管理、ネットワーク ソリューション、ログ記録と監視のツールなど、その他のツールやリソースが必要になります。これらはすべて、まとめてバージョン管理およびテストする必要があります。 コンテナーベースのアプリケーションを構築するには、さらにミドルウェア、フレームワーク、データベース、CI/CD ツールとの統合作業も必要です。 Azure Red Hat OpenShift は、これらすべてを 1 つのプラットフォームに統合することで、IT チームにとって運用が容易になると共に、アプリケーション チームには実行すべきことが指定されます。

Azure Red Hat OpenShift は、Red Hat と Microsoft が共同で設計、運用、サポートを行い、統合されたサポート エクスペリエンスを実現しています。 操作すべき仮想マシンはなく、修正プログラムの適用も必要ありません。 マスター、インフラストラクチャ、およびアプリケーション ノードの修正プログラムの適用、更新、監視は、ユーザーに代わって Microsoft と Red Hat が行っています。 Azure Red Hat OpenShift クラスターは、Azure サブスクリプションにデプロイされ、Azure の課金内容に含まれます。

自動でのソース コード管理、コンテナーとアプリケーションのビルド、デプロイ、スケーリング、正常性の管理などには、独自のレジストリ、ネットワーク、ストレージ、CI/CD のソリューションを選択するか、組み込みのソリューションを使用することができます。 Azure Red Hat OpenShift は、Azure Active Directory を通じて、統合されたサインオン エクスペリエンスを提供します。

最初に、[Azure Red Hat OpenShift クラスターの作成](tutorial-create-cluster.md)に関するチュートリアルを完了してください。

## <a name="access-security-and-monitoring"></a>アクセス、セキュリティ、および監視

セキュリティと管理の強化のため、Azure Red Hat OpenShift では、Azure Active Directory (Azure AD) と統合して、Kubernetes のロールベースのアクセス制御 (RBAC) を使用することができます。 また、クラスターとリソースの正常性を監視することもできます。

## <a name="cluster-and-node"></a>クラスターとノード

Azure Red Hat OpenShift ノードは Azure 仮想マシン上で実行されます。 ストレージをノードやポッドに接続し、クラスター コンポーネントをアップグレードして、GPU を使用することができます。

## <a name="virtual-networks-and-ingress"></a>仮想ネットワークとイングレス

ピアリングを介して[既存の仮想ネットワークに Azure Red Hat OpenShift クラスターを接続](https://docs.microsoft.com/azure/openshift/tutorial-create-cluster#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network)できます。 この構成では、ポッドはピアリングされた仮想ネットワーク内の他のサービスに接続できます。

詳細については、「[クラスターの仮想ネットワークを既存の仮想ネットワークに接続する](tutorial-create-cluster.md#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network)」を参照してください。

## <a name="kubernetes-certification"></a>Kubernetes 認定

Azure Red Hat OpenShift サービスは、Kubernetes 準拠として CNCF 認定されています。

## <a name="next-steps"></a>次のステップ

Azure Red Hat OpenShift の前提条件を確認してください。

> [!div class="nextstepaction"]
> [開発環境のセットアップ](howto-setup-environment.md)
