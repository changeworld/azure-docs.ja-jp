---
title: SAP HANA on Azure (L インスタンス) のアーキテクチャ | Microsoft Docs
description: SAP HANA on Azure (L インスタンス) をデプロイするためのアーキテクチャについて説明します。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/21/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fee1aab009bdbf84acf1a73244d6686db50e4e3f
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114450708"
---
# <a name="sap-hana-large-instances-architecture-on-azure"></a>Azure の SAP HANA (L インスタンス) のアーキテクチャ

この記事では、SAP HANA on Azure Large Instances (BareMetal インフラストラクチャとも呼ばれます) をデプロイするためのアーキテクチャについて説明します。 

大まかに言うと、SAP HANA on Azure (L インスタンス) ソリューションでは、仮想マシン (VM) 上に SAP アプリケーション レイヤーがあります。 データベース レイヤーは、SAP 認定の HANA L インスタンス (HLI) 上にあります。 HLI は、Azure IaaS VM と同じ Azure リージョンに配置されます。

> [!NOTE]
> SAP アプリケーション レイヤーは、SAP データベース管理システム (DBMS) レイヤーと同じ Azure リージョンにデプロイします。 この規則は、Azure 上の SAP ワークロードに関して公開されている情報で明確に文書化されています。 

## <a name="architectural-overview"></a>アーキテクチャの概要

SAP HANA on Azure (L インスタンス) の全体的なアーキテクチャでは、SAP TDI 認定のハードウェア構成が提供されます。 このハードウェアは、SAP HANA データベース用の仮想化されていない、ベア メタルの高パフォーマンス サーバーです。 SAP アプリケーション レイヤーのリソースをニーズに合わせてスケーリングするための柔軟性も提供されます。

![SAP HANA on Azure (L インスタンス) のアーキテクチャの概要](./media/hana-overview-architecture/image1-architecture.png)

上記のアーキテクチャは、次の 3 つのセクションに分かれています。

- **右**: エンド ユーザーが基幹業務 (LOB) アプリケーション (SAP など) にアクセスできるように、データ センターでさまざまなアプリケーションを実行するオンプレミスのインフラストラクチャを示します。 理想的には、このオンプレミス インフラストラクチャは [ExpressRoute](https://azure.microsoft.com/services/expressroute/) で Azure に接続されます。

- **中央**: Azure IaaS を示します。この例では、VM を使用して、SAP アプリケーションや、SAP HANA を DBMS として使用する他のアプリケーションをホストします。 VM が提供するメモリを使用して機能する小規模な HANA インスタンスは、アプリケーション層と共に VM にデプロイされます。 仮想マシンの詳細については、「[Virtual Machines](https://azure.microsoft.com/services/virtual-machines/)」をご覧ください。

   Azure ネットワーク サービスは、SAP システムと他のアプリケーションをグループ化して仮想ネットワークを作成する際に使用されます。 これらの仮想ネットワークは、オンプレミスのシステムおよび SAP HANA on Azure (L インスタンス) に接続します。

   Azure での実行がサポートされている SAP NetWeaver アプリケーションおよびデータベースについては、次を参照してください。「[SAP Support Note #1928533 – SAP applications on Azure: Supported products and Azure VM types (SAP サポート ノート #1928533 - Azure 上の SAP アプリケーション: サポート対象の製品と Azure VM の種類)](https://launchpad.support.sap.com/#/notes/1928533)」。 Azure で SAP ソリューションをデプロイする方法については、次のドキュメントをご覧ください。

  -  [Windows 仮想マシンにおける SAP の使用](./get-started.md?toc=/azure/virtual-machines/linux/toc.json)
  -  [Azure 仮想マシンにおける SAP ソリューションの使用](get-started.md)

- **左**: Azure L インスタンス スタンプの SAP HANA TDI 認定ハードウェアを示します。 HANA L インスタンス ユニットでは、オンプレミス サーバーから Azure への接続に使用されるものと同じテクノロジを使用して、Azure サブスクリプションの仮想ネットワークに接続します。 2019 年 5 月に、ExpressRoute ゲートウェイを使用せずに HANA L インスタンス ユニットと Azure VM の間の通信を可能にする最適化を導入しました。 この最適化は、ExpressRoute FastPath と呼ばれ、前の図で赤い線で示されています。

## <a name="components-of-the-azure-large-instance-stamp"></a>Azure L インスタンス スタンプのコンポーネント

Azure L インスタンス スタンプ自体は次のコンポーネントを結合します。

- **コンピューティング**: 必要なコンピューティング能力を提供し、SAP HANA 認定を取得している各世代の Intel Xeon プロセッサに基づいたサーバー。
- **ネットワーク**:コンピューティング、ストレージ、および LAN コンポーネントを相互接続する統合された高速ネットワーク ファブリック。
- **ストレージ**: 統合されたネットワーク ファブリック経由でアクセスされるストレージ インフラストラクチャ。 提供されるストレージ容量は、デプロイされる SAP HANA on Azure (L インスタンス) 構成に応じて異なります。 追加の月額料金をお支払いいただくと、ストレージ容量を増やすことができます。

## <a name="tenants"></a>テナント

L インスタンス スタンプのマルチテナント インフラストラクチャ内では、分離されたテナントとしてお客様の環境がデプロイされます。 テナントのデプロイ時に、Azure 加入契約内で Azure サブスクリプションに名前を付けます。 これが、HANA L インスタンスの課金対象となる Azure サブスクリプションになります。 これらのテナントには Azure サブスクリプションとの 1 対 1 の関係があります。 

ネットワークについては、異なる Azure サブスクリプションに属する異なる仮想ネットワークから、1 つの Azure リージョン内の 1 つのテナントにデプロイされた HANA L インスタンスにアクセスできます。 それらの Azure サブスクリプションが同じ Azure 加入契約に属している必要があります。

## <a name="availability-across-regions"></a>リージョン全体での可用性

VM と同様に、SAP HANA on Azure (L インスタンス) は複数の Azure リージョンで提供されます。 ディザスター リカバリー機能を提供するために、オプトインを選択できます。 1 つの地政学的リージョン内の異なる L インスタンス スタンプが相互に接続されます。 たとえば、米国西部と米国東部の HANA L インスタンス スタンプは、ディザスター リカバリー レプリケーションのために、専用のネットワーク リンクを介して接続されます。

## <a name="available-skus"></a>利用可能な SKU

Azure で異なる VM の種類から選択できるのと同様に、HANA L インスタンスのさまざまな SKU から選択できます。 特定の SAP HANA ワークロードの種類に適した SKU を選択できます。 SAP では、Intel プロセッサの世代に基づいて、さまざまなワークロードにメモリ対プロセッサ ソケット比を適用します。 使用可能な SKU の詳細については、「[HANA Large Instances で利用できる SKU](hana-available-skus.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

SAP HANA on Azure Large Instances のネットワーク アーキテクチャについて確認します。

> [!div class="nextstepaction"]
> [SAP HANA (L インスタンス) のネットワーク アーキテクチャ](hana-network-architecture.md)
