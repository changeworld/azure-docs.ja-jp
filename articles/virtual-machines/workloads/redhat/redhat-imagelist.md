---
title: Azure の Red Hat Enterprise Linux イメージ | Microsoft Docs
description: Microsoft Azure の Red Hat Enterprise Linux イメージについて説明します
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: e6109a87750e588b12bfc9836c5db3db55420ec2
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/11/2020
ms.locfileid: "77133796"
---
# <a name="red-hat-enterprise-linux-rhel-images-available-in-azure"></a>Azure で利用可能な Red Hat Enterprise Linux (RHEL) イメージ
Azure では、さまざまなユース ケースに対応する多様な RHEL イメージを提供しています。

> [!NOTE]
> すべての RHEL イメージは、Azure パブリック クラウドと Azure Government クラウドで利用できます。 これらは Azure China クラウドでは利用できません。

## <a name="list-of-rhel-images"></a>RHEL イメージの一覧
これは、Azure で利用可能な RHEL イメージの一覧です。 特に明記されていない限り、すべてのイメージは LVM パーティションに分割され、(EUS、E4S ではなく) 標準の RHEL リポジトリに接続されています。 現在、次のイメージが一般に利用できます。

プラン| SKU | [パーティション分割] | プロビジョニング | Notes
:----|:----|:-------------|:-------------|:-----
RHEL          | 6.7      | RAW    | Linux エージェント |
|             | 6.8      | RAW    | Linux エージェント |
|             | 6.9      | RAW    | Linux エージェント |
|             | 6.10     | RAW    | Linux エージェント |
|             | 7-RAW    | RAW    | Linux エージェント | RHEL 7.x イメージ ファミリ。 <br> 既定で (EUS ではなく) 標準リポジトリに接続されています。
|             | 7-LVM    | LVM    | Linux エージェント | RHEL 7.x イメージ ファミリ。 <br> 既定で (EUS ではなく) 標準リポジトリに接続されています。 デプロイする標準の RHEL イメージを探している場合は、このイメージのセット、またはそれに対応する第 2 世代のイメージを使用します。
|             | 7lvm-gen2| LVM    | Linux エージェント | 第 2 世代、RHEL 7.x イメージ ファミリ。 <br> 既定で (EUS ではなく) 標準リポジトリに接続されています。 デプロイする標準の RHEL イメージを探している場合は、このイメージのセット、またはそれに対応する第 1 世代のイメージを使用します。
|             | 7-RAW-CI | RAW-CI | cloud-init  | RHEL 7.x イメージ ファミリ。 <br> 既定で (EUS ではなく) 標準リポジトリに接続されています。
|             | 7.2      | RAW    | Linux エージェント |
|             | 7.3      | RAW    | Linux エージェント |
|             | 7.4      | RAW    | Linux エージェント | 2019 年 4 月現在、既定で EUS リポジトリに接続されています。
|             | 74-gen2  | RAW    | Linux エージェント | 既定では EUS リポジトリに接続されています。
|             | 7.5      | RAW    | Linux エージェント | 2019 年 6 月現在、既定で EUS リポジトリに接続されています。
|             | 75-gen2  | RAW    | Linux エージェント | 既定では EUS リポジトリに接続されています。
|             | 7.6      | RAW    | Linux エージェント | 2019 年 5 月現在、既定で EUS リポジトリに接続されています。
|             | 76-gen2  | RAW    | Linux エージェント | 既定では EUS リポジトリに接続されています。
|             | 7.7      | LVM    | Linux エージェント | 既定では EUS リポジトリに接続されています。
|             | 8        | LVM    | Linux エージェント | RHEL 8.x イメージ ファミリ。
|             | 8-gen2   | LVM    | Linux エージェント | Hyper-V Generation 2 - RHEL 8.x イメージ ファミリ。
RHEL-SAP      | 7.4      | LVM    | Linux エージェント | RHEL 7.4 for SAP HANA および Business Apps。 E4S リポジトリに接続されており、SAP および RHEL の割増料金と基本コンピューティング料金が請求されます。
|             | 74sap-gen2| LVM    | Linux エージェント | RHEL 7.4 for SAP HANA および Business Apps。 第 2 世代イメージ。 E4S リポジトリに接続されており、SAP および RHEL の割増料金と基本コンピューティング料金が請求されます。
|             | 7.5       | LVM    | Linux エージェント | RHEL 7.5 for SAP HANA および Business Apps。 E4S リポジトリに接続されており、SAP および RHEL の割増料金と基本コンピューティング料金が請求されます。
|             | 75sap-gen2| LVM    | Linux エージェント | RHEL 7.5 for SAP HANA および Business Apps。 第 2 世代イメージ。 E4S リポジトリに接続されており、SAP および RHEL の割増料金と基本コンピューティング料金が請求されます。
|             | 7.6       | LVM    | Linux エージェント | RHEL 7.6 for SAP HANA および Business Apps。 E4S リポジトリに接続されており、SAP および RHEL の割増料金と基本コンピューティング料金が請求されます。
|             | 76sap-gen2| LVM    | Linux エージェント | RHEL 7.6 for SAP HANA および Business Apps。 第 2 世代イメージ。 E4S リポジトリに接続されており、SAP および RHEL の割増料金と基本コンピューティング料金が請求されます。
|             | 7.7       | LVM    | Linux エージェント | RHEL 7.7 for SAP HANA および Business Apps。 E4S リポジトリに接続されており、SAP および RHEL の割増料金と基本コンピューティング料金が請求されます。
RHEL-SAP-HANA | 6.7       | RAW    | Linux エージェント | RHEL 6.7 for SAP HANA。 RHEL-SAP イメージを優先して古くなりました。
|             | 7.2       | LVM    | Linux エージェント | RHEL 7.2 for SAP HANA。 RHEL-SAP イメージを優先して古くなりました。
|             | 7.3       | LVM    | Linux エージェント | RHEL 7.3 for SAP HANA。 RHEL-SAP イメージを優先して古くなりました。
RHEL-SAP-APPS | 6.8       | RAW    | Linux エージェント | RHEL 6.8 for SAP Business Applications。 RHEL-SAP イメージを優先して古くなりました。
|             | 7.3       | LVM    | Linux エージェント | RHEL 7.3 for SAP Business Applications。 RHEL-SAP イメージを優先して古くなりました。
RHEL-HA       | 7.4       | LVM    | Linux エージェント | HA アドオンが追加された RHEL 7.4。 基本コンピューティング料金に加えて、HA および RHEL の割増料金が請求されます。
|             | 7.5       | LVM    | Linux エージェント | HA アドオンが追加された RHEL 7.5。 基本コンピューティング料金に加えて、HA および RHEL の割増料金が請求されます。
|             | 7.6       | LVM    | Linux エージェント | HA アドオンが追加された RHEL 7.6。 基本コンピューティング料金に加えて、HA および RHEL の割増料金が請求されます。
RHEL-SAP-HA   | 7.4          | LVM    | Linux エージェント | HA および更新サービスを備えた RHEL 7.4 for SAP。 E4S リポジトリに接続されています。 基本コンピューティング料金に加えて、SAP および HA リポジトリと RHEL の割増料金が請求されます。
|             | 74sapha-gen2 | LVM    | Linux エージェント | HA および更新サービスを備えた RHEL 7.4 for SAP。 第 2 世代イメージ。 E4S リポジトリに接続されています。 基本コンピューティング料金に加えて、SAP および HA リポジトリと RHEL の割増料金が請求されます。
|             | 7.5          | LVM    | Linux エージェント | HA および更新サービスを備えた RHEL 7.5 for SAP。 E4S リポジトリに接続されています。 基本コンピューティング料金に加えて、SAP および HA リポジトリと RHEL の割増料金が請求されます。
|             | 7.6          | LVM    | Linux エージェント | HA および更新サービスを備えた RHEL 7.6 for SAP。 E4S リポジトリに接続されています。 基本コンピューティング料金に加えて、SAP および HA リポジトリと RHEL の割増料金が請求されます。
|             | 76sapha-gen2 | LVM    | Linux エージェント | HA および更新サービスを備えた RHEL 7.6 for SAP。 第 2 世代イメージ。 E4S リポジトリに接続されています。 基本コンピューティング料金に加えて、SAP および HA リポジトリと RHEL の割増料金が請求されます。
|             | 7.7          | LVM    | Linux エージェント | HA および更新サービスを備えた RHEL 7.7 for SAP。 E4S リポジトリに接続されています。 基本コンピューティング料金に加えて、SAP および HA リポジトリと RHEL の割増料金が請求されます。
|             | 77sapha-gen2 | LVM    | Linux エージェント | HA および更新サービスを備えた RHEL 7.7 for SAP。 第 2 世代イメージ。 E4S リポジトリに接続されています。 基本コンピューティング料金に加えて、SAP および HA リポジトリと RHEL の割増料金が請求されます。
rhel-byos     |rhel-lvm74| LVM    | Linux エージェント | RHEL 7.4 BYOS イメージ。どの更新ソースにも接続されておらず、RHEL の割増料金は請求されません。
|             |rhel-lvm75| LVM    | Linux エージェント | RHEL 7.5 BYOS イメージ。どの更新ソースにも接続されておらず、RHEL の割増料金は請求されません。
|             |rhel-lvm76| LVM    | Linux エージェント | RHEL 7.6 BYOS イメージ。どの更新ソースにも接続されておらず、RHEL の割増料金は請求されません。
|             |rhel-lvm77| LVM    | Linux エージェント | RHEL 7.7 BYOS イメージ。どの更新ソースにも接続されておらず、RHEL の割増料金は請求されません。
|             |rhel-lvm8 | LVM    | Linux エージェント | RHEL 8 BYOS イメージ (イメージ バージョン値には、RHEL マイナー バージョンが示されます)。どの更新ソースにも接続されておらず、RHEL の割増料金は請求されません。

## <a name="next-steps"></a>次のステップ
* [Azure の Red Hat イメージ](./redhat-images.md)の詳細を確認します。
* [Red Hat Update Infrastructure](./redhat-rhui.md) の詳細を確認します。
* [RHEL BYOS プラン](./byos.md)の詳細を確認します。
* すべてのバージョンの RHEL に対する Red Hat のサポート ポリシーに関する情報は、「[Red Hat Enterprise Linux Life Cycle \(Red Hat Enterprise Linux のライフ サイクル\)](https://access.redhat.com/support/policy/updates/errata)」ページに記載されています。
