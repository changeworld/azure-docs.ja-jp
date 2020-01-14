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
ms.date: 12/18/2019
ms.author: alsin
ms.openlocfilehash: 61991e271a68c9160a34d0de99fe4c9259ca41cb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75476941"
---
# <a name="red-hat-enterprise-linux-rhel-images-available-in-azure"></a>Azure で利用可能な Red Hat Enterprise Linux (RHEL) イメージ
Azure では、さまざまなユース ケースに対応する多様な RHEL イメージを提供しています。

## <a name="list-of-rhel-images"></a>RHEL イメージの一覧
これは、Azure で利用可能な RHEL イメージの一覧です。 特に明記されていない限り、すべてのイメージは LVM パーティションに分割され、(EUS、E4S ではなく) 標準の RHEL リポジトリに接続されています。 現在、次のイメージが一般に利用できます。

プラン| SKU | [パーティション分割] | プロビジョニング | メモ
:----|:----|:-------------|:-------------|:-----
RHEL          | 6.7      | RAW    | Linux エージェント |
|             | 6.8      | RAW    | Linux エージェント |
|             | 6.9      | RAW    | Linux エージェント |
|             | 6.10     | RAW    | Linux エージェント |
|             | 7-RAW    | RAW    | Linux エージェント | RHEL 7.x イメージ ファミリ。 <br> 既定で (EUS ではなく) 標準リポジトリに接続されています。
|             | 7-LVM    | LVM    | Linux エージェント | RHEL 7.x イメージ ファミリ。 <br> 既定で (EUS ではなく) 標準リポジトリに接続されています。
|             | 7-RAW-CI | RAW-CI | cloud-init  | RHEL 7.x イメージ ファミリ。 <br> 既定で (EUS ではなく) 標準リポジトリに接続されています。
|             | 7.2      | RAW    | Linux エージェント |
|             | 7.3      | RAW    | Linux エージェント |
|             | 7.4      | RAW    | Linux エージェント | 2019 年 4 月現在、既定で EUS リポジトリに接続されています。
|             | 7.5      | RAW    | Linux エージェント | 2019 年 6 月現在、既定で EUS リポジトリに接続されています。
|             | 7.6      | RAW    | Linux エージェント | 2019 年 5 月現在、既定で EUS リポジトリに接続されています。
|             | 7.7      | LVM    | Linux エージェント | 既定では EUS リポジトリに接続されています。
|             | 8        | LVM    | Linux エージェント | RHEL 8.x イメージ ファミリ。
|             | 8-gen2   | LVM    | Linux エージェント | Hyper-V Generation 2 - RHEL 8.x イメージ ファミリ。
RHEL-SAP      | 7.4      | LVM    | Linux エージェント | RHEL 7.4 for SAP HANA および Business Apps。 E4S リポジトリに接続されており、SAP および RHEL の割増料金と基本コンピューティング料金が請求されます。
|             | 7.5      | LVM    | Linux エージェント | RHEL 7.5 for SAP HANA および Business Apps。 E4S リポジトリに接続されており、SAP および RHEL の割増料金と基本コンピューティング料金が請求されます。
|             | 7.6      | LVM    | Linux エージェント | RHEL 7.5 for SAP HANA および Business Apps。 E4S リポジトリに接続されており、SAP および RHEL の割増料金と基本コンピューティング料金が請求されます。
|             | 7.7      | LVM    | Linux エージェント | RHEL 7.5 for SAP HANA および Business Apps。 E4S リポジトリに接続されており、SAP および RHEL の割増料金と基本コンピューティング料金が請求されます。
RHEL-SAP-HANA | 6.7      | RAW    | Linux エージェント | RHEL 6.7 for SAP HANA。 RHEL-SAP イメージを優先して古くなりました。
|             | 7.2      | LVM    | Linux エージェント | RHEL 7.2 for SAP HANA。 RHEL-SAP イメージを優先して古くなりました。
|             | 7.3      | LVM    | Linux エージェント | RHEL 7.3 for SAP HANA。 RHEL-SAP イメージを優先して古くなりました。
RHEL-SAP-APPS | 6.8      | RAW    | Linux エージェント | RHEL 6.8 for SAP Business Applications。 RHEL-SAP イメージを優先して古くなりました。
|             | 7.3      | LVM    | Linux エージェント | RHEL 7.3 for SAP Business Applications。 RHEL-SAP イメージを優先して古くなりました。
RHEL-HA       | 7.4      | LVM    | Linux エージェント | HA アドオンが追加された RHEL 7.4。 基本コンピューティング料金に加えて、HA および RHEL の割増料金が請求されます。
|             | 7.5      | LVM    | Linux エージェント | HA アドオンが追加された RHEL 7.5。 基本コンピューティング料金に加えて、HA および RHEL の割増料金が請求されます。
|             | 7.6      | LVM    | Linux エージェント | HA アドオンが追加された RHEL 7.6。 基本コンピューティング料金に加えて、HA および RHEL の割増料金が請求されます。
RHEL-SAP-HA   | 7.4      | LVM    | Linux エージェント | HA アドオンが追加された RHEL 7.4 for SAP。 E4S リポジトリに接続されています。 基本コンピューティング料金に加えて、SAP および HA リポジトリと RHEL の割増料金が請求されます。
|             | 7.5      | LVM    | Linux エージェント | HA アドオンが追加された RHEL 7.5 for SAP。 E4S リポジトリに接続されています。 基本コンピューティング料金に加えて、SAP および HA リポジトリと RHEL の割増料金が請求されます。
|             | 7.6      | LVM    | Linux エージェント | HA アドオンが追加された RHEL 7.6 for SAP。 E4S リポジトリに接続されています。 基本コンピューティング料金に加えて、SAP および HA リポジトリと RHEL の割増料金が請求されます。
rhel-byos     |rhel-lvm74| LVM    | Linux エージェント | RHEL 7.4 BYOS イメージ。どの更新ソースにも接続されておらず、RHEL の割増料金は請求されません。
|             |rhel-lvm75| LVM    | Linux エージェント | RHEL 7.5 BYOS イメージ。どの更新ソースにも接続されておらず、RHEL の割増料金は請求されません。
|             |rhel-lvm76| LVM    | Linux エージェント | RHEL 7.6 BYOS イメージ。どの更新ソースにも接続されておらず、RHEL の割増料金は請求されません。
|             |rhel-lvm77| LVM    | Linux エージェント | RHEL 7.7 BYOS イメージ。どの更新ソースにも接続されておらず、RHEL の割増料金は請求されません。
|             |rhel-lvm8 | LVM    | Linux エージェント | RHEL 8 BYOS イメージ (イメージ バージョン値には、RHEL マイナー バージョンが示されます)。どの更新ソースにも接続されておらず、RHEL の割増料金は請求されません。

## <a name="next-steps"></a>次のステップ
* [Azure の Red Hat イメージ](./redhat-images.md)の詳細を確認します。
* [Red Hat Update Infrastructure](./redhat-rhui.md) の詳細を確認します。
* [RHEL BYOS プラン](./redhat-byos.md)の詳細を確認します。
* すべてのバージョンの RHEL に対する Red Hat のサポート ポリシーに関する情報は、「[Red Hat Enterprise Linux Life Cycle \(Red Hat Enterprise Linux のライフ サイクル\)](https://access.redhat.com/support/policy/updates/errata)」ページに記載されています。