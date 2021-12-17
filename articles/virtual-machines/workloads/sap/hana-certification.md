---
title: SAP HANA on Azure (L インスタンス) の認定 | Microsoft Docs
description: SAP HANA on Azure (L インスタンス) の認定について説明します。
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
ms.date: 07/14/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ade0e82131f65183ac9471bb77d0ab0cc92ae285
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2021
ms.locfileid: "114219642"
---
# <a name="certification"></a>認定資格

SAP では、Azure IaaS や BareMetal インフラストラクチャなどの特定のインフラストラクチャ上の SAP HANA をサポートするための要件として、NetWeaver 認定資格のほか、SAP HANA の特別な認定資格を要求しています。

NetWeaver (および一部の SAP HANA 認定資格) に関する主要な SAP ノートは、「[SAP Note #1928533 - SAP applications on Azure: Supported products and Azure VM types (SAP ノート #1928533 - Azure 上の SAP アプリケーション: サポート対象の製品と Azure VM の種類)](https://launchpad.support.sap.com/#/notes/1928533)」です。

SAP HANA on Azure Large Instances の認定記録については、[SAP HANA 認定 IaaS プラットフォーム](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)のサイトを参照してください。 

SAP HANA 認定 IaaS プラットフォームのサイトで取り上げられている SAP HANA on Azure (Large Instances) タイプを使用すると、Microsoft と SAP のお客様は次をデプロイできます。

- 大規模な SAP Business Suite
- SAP BW
- S/4 HANA
- BW/4HANA
- Azure 内の他の SAP HANA ワークロード。 

このソリューションは、SAP-HANA 認定の専用のハードウェア スタンプ ([SAP HANA テーラード データ センター統合 - TDI](https://scn.sap.com/docs/DOC-63140)) に基づいています。 SAP HANA TDI で構成されたソリューションを実行すると、上記のすべての SAP HANA ベースのアプリケーションがハードウェア インフラストラクチャで動作します。

VM で SAP HANA を実行する場合と比較すると、このソリューションにはメモリ ボリュームが大幅に増加するという利点が提供されます。 

## <a name="key-concepts"></a>主要な概念

このソリューションを有効にする場合は、次の重要な側面を理解しておく必要があります。

- SAP アプリケーション層と SAP 以外のアプリケーションは、通常の Azure ハードウェア スタンプでホストされる VM で実行されます。
- お客様のオンプレミスのインフラストラクチャ、データ センター、アプリケーションのデプロイは、ExpressRoute (推奨) または仮想プライベート ネットワーク (VPN) 経由でクラウド プラットフォームに接続されます。 Active Directory と DNS も Azure に拡張されます。
- HANA ワークロード用の SAP HANA データベース インスタンスは SAP HANA on Azure (L インスタンス) で実行されます。 L インスタンス スタンプは Azure ネットワークに接続されるので、VM で実行されるソフトウェアは HANA L インスタンスで実行される HANA インスタンスとやり取りできます。
- SAP HANA on Azure (L インスタンス) のハードウェアは、SUSE Linux Enterprise Server または Red Hat Enterprise Linux がプレインストールされた IaaS で提供される専用のハードウェアです。 仮想マシンと同様に、オペレーティング システムの更新とメンテナンスはお客様が行います。
- HANA のインストール、または HANA L インスタンスのユニットで SAP HANA を実行するために必要なその他のコンポーネントのインストールはお客様が行います。 SAP HANA on Azure の個々の継続的な運用と管理もお客様が行います。
- また、Azure サブスクリプションには、SAP HANA on Azure (Large Instances) に接続する他のコンポーネントをインストールすることもできます。 たとえば、次のような SAP HANA データベースとの通信を可能にするコンポーネントなどがあります。        
    - ジャンプ サーバー
    - RDP サーバー
    - SAP HANA Studio
    - SAP BI シナリオ向けの SAP Data Services
    - ネットワーク監視ソリューション。
- Azure 内と同様に、HANA L インスタンスは高可用性とディザスター リカバリー機能をサポートします。

## <a name="next-steps"></a>次の手順

HANA Large Instances で利用できる SKU について確認します。

> [!div class="nextstepaction"]
> [HLI で利用可能な SKU](hana-available-skus.md)
