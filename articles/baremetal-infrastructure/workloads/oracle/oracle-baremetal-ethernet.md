---
title: Oracle 用の BareMetal のイーサネット構成
description: Oracle ワークロード用の BareMetal インスタンスでのイーサネット インターフェイスの構成について説明します。
ms.topic: reference
ms.subservice: baremetal-oracle
ms.date: 04/14/2021
ms.openlocfilehash: b3183e660b8775e8f3c9137f27220e9db97deec6
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2021
ms.locfileid: "110578604"
---
# <a name="ethernet-configuration-of-baremetal-for-oracle"></a>Oracle 用の BareMetal のイーサネット構成

この記事では、Oracle ワークロード用の BareMetal インスタンスでのイーサネット インターフェイスの構成について説明します。

Oracle 用にプロビジョニングされた各 BareMetal インスタンスには、一連のイーサネット インターフェイスがあらかじめ構成されています。 イーサネット インターフェイスは、次の 4 種類に分類されます。

- クライアント アクセスで使用されます。
- ノード間通信に使用されます。 このインターフェイスは、要求されるトポロジには関係なくすべてのサーバー上に構成されます。 スケールアウト シナリオでのみ使用されます。
- ノードからストレージへの接続に使用されます。
- ディザスター リカバリー (DR) のセットアップと、リージョン間接続のためのグローバル アクセスへの接続に使用されます。

## <a name="architecture"></a>Architecture

次の図は、BareMetal インフラストラクチャの事前に構成されたイーサネット インターフェイスのアーキテクチャを示しています。 

[![Oracle ワークロード用に事前に構成されたイーサネット インターフェイスのアーキテクチャを示す図。](media/oracle-baremetal-ethernet/architecture-ethernet.png)](media/oracle-baremetal-ethernet/architecture-ethernet.png#lightbox)

既定の構成には、1 つのクライアント IP インターフェイス (eth1) が付属しています。これは、Azure Virtual Network (VNET) から接続されており、Secure Shell (SSH) を使用して BareMetal インスタンスにアクセスできるようになります。

> [!NOTE]
> 別の Azure VNET からの別のクライアント インターフェイス (eth10) については、Microsoft CSA に連絡してサービス リクエストを送信してください。 たとえば、運用または DR 環境だけでなく、開発またはテストが必要な場合などです。

| **NIC 論理インターフェイス** | **RHEL OS の場合の名前** | **ユース ケース** |
| --- | --- | --- |
| A | net1.tenant | クライアントから BareMetal インスタンス |
| C | net2.tenant | ノードからストレージ。ストレージ環境を管理するためのストレージ コントローラーへの調整とアクセスがサポートされています。 |
| B | net3.tenant | ノード間 (プライベート相互接続) |
| C | net4.tenant | 予約済み/iSCSI |
| C | net5.tenant | 予約済み/ログ バックアップ |
| C | net6.tenant | ノードからストレージへのデータ バックアップ (RMAN、スナップショット) |
| C | net7.tenant | ノードからストレージ (dNFS-Pri)。NetApp ストレージ アレイとの接続を提供します。 |
| C | net8.tenant | ノードからストレージ (dNFS-Sec)。NetApp ストレージ アレイとの接続を提供します。 |
| D | net9.tenant | 別のリージョンの BMI にアクセスするためのグローバル アクセス設定用の DR 接続。 |
| A | \*net10.tenant | \* クライアントから BareMetal インスタンス
 |

必要に応じて、追加のネットワーク インターフェイス コントローラー (NIC) カードを独自に定義できます。 ただし、既存の NIC の構成を変更することは *できません*。

## <a name="usage-rules"></a>使い方の規則

BareMetal インスタンスの場合、既定では 4 つの論理 NIC に 9 個の IP アドレスが割り当てられています。 次の使用上の規則が適用されます。

- イーサネット "A" には、お客様が Microsoft に提出したサーバー IP プールのアドレス範囲外の IP アドレスが割り当てられている必要があります。 この IP アドレスは、OS の etc/hosts ディレクトリに保持してはなりません。
- イーサネット "B" のみを、さまざまなインスタンス間の通信用に etc/hosts ディレクトリで保持する必要があります。 これらの IP アドレスは、ノード間の構成に使用される IP アドレスとして、スケールアウト Oracle Real Application Clusters (RAC) 構成で管理します。
- イーサネット "C" には、NFS ストレージへの通信に使用される IP アドレスが割り当てられている必要があります。 この種類のアドレスは、/etc/hosts ディレクトリに保持しないでください。
- イーサネット "D" は、DR リージョンの BareMetal インスタンスにアクセスするためのグローバル アクセス設定専用に使用する必要があります。

## <a name="next-steps"></a>次のステップ

Oracle アーキテクチャ用の BareMetal インフラストラクチャについて学びます。

> [!div class="nextstepaction"]
> [Oracle 用の BareMetal インフラストラクチャのアーキテクチャ](oracle-baremetal-architecture.md)
