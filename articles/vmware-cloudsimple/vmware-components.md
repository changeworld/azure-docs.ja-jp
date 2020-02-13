---
title: Azure VMware Solutions (AVS) - AVS プライベート クラウド VMware コンポーネント
description: VMware コンポーネントを AVS プライベート クラウド上にインストールする方法について説明します。
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3ea6c22c3957f72a0a416ce7ae42c62ff5a0791a
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2020
ms.locfileid: "77016784"
---
# <a name="avs-private-cloud-vmware-components"></a>AVS プライベート クラウド VMware コンポーネント

AVS プライベート クラウドとは、管理ドメイン内の vCenter サーバーによって管理される、分離された VMware スタック (ESXi ホスト、vCenter、vSAN、NSX) 環境のことです。 AVS サービスを使用すると、Azure の場所にある Azure ベア メタル インフラストラクチャ上に VMware をネイティブにデプロイできます。 AVS プライベート クラウドは、Azure クラウドの残りの部分と統合されます。 AVS プライベート クラウドは、次の VMware スタック コンポーネントと共にデプロイされます。

* **VMware ESXi -** Azure 専用ノード上のハイパーバイザー
* **VMware vCenter -** AVS プライベート クラウド vSphere 環境を一元管理するためのアプライアンス
* **VMware vSAN -** ハイパー コンバージド インフラストラクチャ ソリューション
* **VMware NSX Data Center -** ネットワーク仮想化およびセキュリティ ソフトウェア  

## <a name="vmware-component-versions"></a>VMware コンポーネントのバージョン

AVS プライベート クラウド VMware スタックをデプロイするときに使用するソフトウェア バージョンを次に示します。

| コンポーネント | Version | ライセンス付きのバージョン |
|-----------|---------|------------------|
| ESXi | 6.7U2 | Enterprise Plus |
| vCenter | 6.7U2 | vCenter Standard |
| vSAN | 6.7 | Enterprise |
| NSX Data Center | 2.4.1 | 詳細設定 |

## <a name="esxi"></a>ESXi

VMware ESXi は、AVS プライベート クラウドを作成するときに、プロビジョニングされた AVS ノード上にインストールされます。 ESXi は、ワークロード仮想マシン (VM) をデプロイするためのハイパーバイザーです。 ノードにより、AVS プライベート クラウド上のハイパー コンバージド インフラストラクチャ (コンピューティングおよびストレージ) が提供されます。 これらのノードは、AVS プライベート クラウド上で vSphere クラスターを構成する一部となります。 各ノードでは、4 つの物理ネットワーク インターフェイスがアンダーレイ ネットワークに接続されています。 2 つの物理ネットワーク インターフェイスを使用して、vCenter 上の **vSphere 分散スイッチ (VDS)** と、**NSX マネージド仮想分散スイッチ (N-VDS)** が作成されます。 高可用性を実現するため、ネットワーク インターフェイスは、アクティブ/アクティブ モードで構成されます。

VMware ESXi の詳細を確認してください。

## <a name="vcenter-server-appliance"></a>vCenter サーバー アプライアンス

vCenter サーバー アプライアンス (VCSA) は、VMware Solutions (AVS) で、認証、管理、およびオーケストレーションの各機能を提供します。 Platform Services Controller (PSC) が組み込まれた VCSA は、AVS プライベート クラウドを作成するときにデプロイされます。 VCSA は、AVS プライベート クラウドをデプロイするときに作成される vSphere クラスターにデプロイされます。 各 AVS プライベート クラウドには、独自の VCSA があります。 AVS プライベート クラウドを拡張すると、AVS プライベート クラウド上の VCSA にノードが追加されます。

### <a name="vcenter-single-sign-on"></a>vCenter シングル サインオン

VCSA に組み込まれた Platform Services Controller は、**vCenter シングル サインオン ドメイン**に関連付けられています。 ドメイン名は **AVS.local** です。 vCenter にアクセスするために既定のユーザー **CloudOwner@AVS.com** が作成されます。 vCenter 用のオンプレミス/Azure Active Directory [ID ソース](set-vcenter-identity.md)を追加できます。

## <a name="vsan-storage"></a>vSAN ストレージ

AVS プライベート クラウドは、完全に構成されたオール フラッシュ vSAN ストレージを使用して、クラスター ローカルで作成されます。 vSAN データストアを含む vSphere クラスターを作成するには、同じ SKU のノードが 3 つ以上必要です。 既定では、vSAN データストア上では重複除去と圧縮が有効になっています。 vSphere クラスターの各ノード上には、2 つのディスク グループが作成されます。 各ディスク グループには、1 つのキャッシュ ディスクと 3 つのキャパシティ ディスクが含まれています。

既定の vSAN ストレージ ポリシーが vSphere クラスター上に作成され、vSAN データストアに適用されます。 必要なサービス レベルを保証するため、このポリシーでは、VM ストレージ オブジェクトをデータ ストア内でプロビジョニングおよび割り当てする方法が決定されます。 ストレージ ポリシーでは、**許容エラー (FTT)** と **エラー許容メソッド**が定義されます。 新規のストレージ ポリシーを作成して VM に適用することが可能です。 SLA を維持するため、vSAN データストア上では、25% のキャパシティを予備として確保しておく必要があります。 

### <a name="default-vsan-storage-policy"></a>既定の vSAN ストレージ ポリシー

次の表は、vSAN ストレージ ポリシー パラメーターの既定値を示しています。

| Number of nodes in vSphere Cluster (vSphere クラスター内のノード数) | FTT | Failure tolerance method (エラー許容メソッド) |
|------------------------------------|-----|--------------------------|
| 3 ～ 4 個のノード | 1 | RAID 1 (ミラーリング) - 2 つのコピーを作成 |
| 5 ～ 16 個のノード | 2 | RAID 1 (ミラーリング) - 3 つのコピーを作成 |

## <a name="nsx-data-center"></a>NSX Data Center

NSX Data Center は、AVS プライベート クラウド上でネットワーク仮想化、マイクロ セグメント化、およびネットワーク セキュリティの各機能を提供します。 AVS プライベート クラウド上の NSX Data Center でサポートされるすべてのサービスは、NSX を通じて構成できます。 AVS プライベート クラウドを作成すると、次の NSX コンポーネントがインストールされて構成されます。

* NSXT Manager
* トランスポート ゾーン
* ホストおよびエッジのアップリンク プロファイル
* エッジ トランスポート用の論理スイッチ (Ext1 および Ext2)
* ESXi トランスポート ノード用の IP プール
* エッジ トランスポート ノード用の IP プール
* エッジ ノード
* コントローラーおよびエッジ VM の DRS アンチ アフィニティ ルール
* 階層 0 ルーター
* 階層 0 ルーター上 BGP を有効化

## <a name="vsphere-cluster"></a>vSphere クラスター

ESXi ホストは、AVS プライベート クラウドの高可用性を保証するため、クラスターとして構成されます。 AVS プライベート クラウドを作成すると、最初のクラスターに vSphere の管理コンポーネントがデプロイされます。 管理コンポーネント用のリソース プールが作成され、このリソース プール内にすべての管理 VM がデプロイされます。 最初のクラスターを削除して、AVS プライベート クラウドを縮小することはできません。 vSphere クラスターでは、**vSphere HA** を使用して、VM の高可用性が実現されます。 許容エラーは、クラスター内の使用可能なノード数に基づきます。 ```Number of nodes = 2N+1``` という式が使用されます。ここで ```N``` は、許容エラーの数です。

### <a name="vsphere-cluster-limits"></a>vSphere クラスターの制限

| リソース | 制限 |
|----------|-------|
| AVS プライベート クラウドを作成するためのノードの最小数 (最初の vSphere クラスター) | 3 |
| AVS プライベート クラウド上の vSphere クラスター内のノードの最大数 | 16 |
| AVS プライベート クラウド内のノードの最大数 | 64 |
| AVS プライベート クラウド内の vSphere クラスターの最大数 | 21 |
| 新しい vSphere クラスター上のノードの最小数 | 3 |

## <a name="vmware-infrastructure-maintenance"></a>VMware インフラストラクチャのメンテナンス

VMware インフラストラクチャの構成変更が必要になることがあります。 現在のところ、この間隔は 1-2 か月に 1 回ですが、頻度は時間の経過と共に下がると予想されます。 この種類のメンテナンスは通常、AVS サービスの通常の利用を中断することなく実行できます。 VMware メンテナンスの期間中、次のサービスは影響なしで作動し続けます。

* VMware 管理プレーンとアプリケーション
* vCenter アクセス
* すべてのネットワークとストレージ
* すべての Azure トラフィック

## <a name="updates-and-upgrades"></a>更新とアップグレード

AVS は、AVS プライベート クラウド内の VMware ソフトウェア (ESXi、vCenter、PSC、NSX) のライフサイクル管理を担います。

ソフトウェア更新の内容:

* **パッチ**。 VMware によってリリースされたセキュリティ パッチまたはバグ修正プログラム。
* **[Updates]** (更新)。 VMware スタック コンポーネントのマイナー バージョン変更。
* **アップグレード**。 VMware スタック コンポーネントのメジャー バージョン変更。

VMware から重要なセキュリティ パッチが利用できるようになると、AVS で直ちにそのテストが行われます。 SLA に基づき、AVS では 1 週間以内にプライベート クラウド環境にそのセキュリティ パッチがロールアウトされます。

AVS では、VMware ソフトウェア コンポーネントのメンテナンス更新が四半期ごとに提供されます。 VMware ソフトウェアの新しいメジャー バージョンが利用可能になると、AVS は顧客と連携し、アップグレードのためのメンテナンス期間を調整します。 

## <a name="next-steps"></a>次のステップ

* [AVS のメンテナンスと更新](cloudsimple-maintenance-updates.md)
