---
title: SAP HANA on Azure (L インスタンス) のインフラストラクチャと接続 | Microsoft Docs
description: SAP HANA on Azure (L インスタンス) を使用するために必要な接続インフラストラクチャを構成します。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 6/1/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f8fc161f83bcdb070ca4be1c1da3be84f1086c93
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2021
ms.locfileid: "111412549"
---
# <a name="sap-hana-large-instances-deployment"></a>SAP HANA (Large Instance) のデプロイ 

この記事では、データベースをデプロイするために必要な情報 (bareMetal インフラストラクチャ SAP HANA on Azure Large Instancesと呼ばれる) を一覧表示します。 最初に、背景については、以下を参照してください。
- [HANA Large Instances のコンピューティング](hana-know-terms.md)
-  [HANA ラージ インスタンス SKU](hana-available-skus.md)

## <a name="required-information"></a>必要な情報

Microsoft から SAP HANA on Azure Large Instances を購入し、デプロイする必要があります。 Microsoft では、お客様から次の情報が必要です。

- お客様名。
- 会社の連絡先情報 (メール アドレスや電話番号など)。
- 技術担当者の連絡先情報 (メール アドレスや電話番号など)。
- 技術担当者のネットワーキング用の連絡先情報 (メール アドレスや電話番号など)。
- Azure デプロイ リージョン (例: 米国西部、オーストラリア東部、北ヨーロッパ)。
- SAP HANA on Azure (L インスタンス) の SKU (構成)。
- Azure デプロイ リージョンごとに次の情報が必要となります。
    - Azure 仮想ネットワークを HANA L インスタンスに接続する ER-P2P 接続の /29 IP アドレス範囲。
    - HANA L インスタンスのサーバー IP プールに使用する /24 CIDR ブロック。
    - [ExpressRoute Global Reach](../../../expressroute/expressroute-global-reach.md) を 使用する場合のオプション。別の /29 IP アドレス範囲を予約します。 追加された範囲により、オンプレミスから HANA Large Instance ユニットへの直接ルーティングが可能になります。 追加された範囲により、異なる Azure リージョン内の HANA Large Instance ユニット間のルーティングも可能になります。 この特定の範囲は、以前に定義した IP アドレス範囲と重複しないようにする必要があります。
- HANA L インスタンスに接続するすべての Azure 仮想ネットワークの "仮想ネットワーク アドレス空間" 属性に使用される IP アドレス範囲の値。
- 各 HANA L インスタンス システムのデータ:
  - 適切なホスト名 (できれば完全修飾ドメイン名を含める)。
  - HANA L インスタンス ユニットの適切な IP アドレス (サーバー IP プールのアドレス範囲から)。 サーバー IP プール アドレス範囲に含まれる最初の 30 個の IP アドレスは、HANA L インスタンスの内部的な用途で予約されています。
  - SAP HANA インスタンス用の SAP HANA SID 名 (SAP HANA に関連する必要なディスク ボリュームの作成に必要)。 HANA SID は、NFS ボリューム上に sidadm のアクセス許可を作成するために必要となります。 これらのボリュームが、HANA L インスタンス ユニットにアタッチされます。 また、HANA SID は、マウントされるディスク ボリュームの名前の構成要素の 1 つとしても使用されます。 ユニット上で複数の HANA インスタンスを実行する場合は、複数の HANA SID の一覧を指定する必要があります。 それぞれに個別のボリュームのセットが割り当てられます。
  - Linux OS では、sidadm ユーザーにグループ ID が割り当てられます。 この ID は、SAP HANA 関連の必要なディスク ボリュームを作成するうえでの要件となっています。 SAP HANA のインストールでは通常、グループ ID 1001 の sapsys グループが作成されます。 sidadm ユーザーは、そのグループに属しています。
  - Linux OS では、sidadm ユーザーにユーザー ID が割り当てられます。 この ID は、SAP HANA 関連の必要なディスク ボリュームを作成するうえでの要件となっています。 ユニット上で複数の HANA インスタンスを実行している場合は、すべての sidadm ユーザーをリストしてください。 
- SAP HANA on Azure (L インスタンス) が直接接続する Azure サブスクリプションの Azure サブスクリプション ID。 このサブスクリプション ID は、Azure サブスクリプションを参照します。そのサブスクリプションに HANA L インスタンス ユニットが課金されます。

以上の情報を入力すると、Microsoft によって SAP HANA on Azure (L インスタンス) がプロビジョニングされます。 Microsoft からは、Azure 仮想ネットワークを HANA L インスタンスにリンクさせるための情報が送信されます。 HANA L インスタンス ユニットにアクセスすることもできます。

## <a name="next-steps"></a>次の手順

Microsoft が HANA Large Instances をデプロイした後に HANA Large Instances に接続するには、次の記事を順に参照してください。

1. [HANA L インスタンスへの Azure VM の接続](hana-connect-azure-vm-large-instances.md)
2. [HANA L インスタンスの ExpressRoute への VNet の接続](hana-connect-vnet-express-route.md)
3. [その他のネットワーク要件 (オプション)](hana-additional-network-requirements.md)
