---
title: Oracle 用 BareMetal インフラストラクチャの概要
description: BareMetal インフラストラクチャによって Oracle ワークロード向けに提供される機能について説明します。
ms.topic: conceptual
ms.subservice: baremetal-oracle
ms.date: 09/03/2021
ms.openlocfilehash: e74d1a3e3b6cb1b2cd0b99927a57a88117715e7b
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/07/2021
ms.locfileid: "123542442"
---
# <a name="what-is-baremetal-infrastructure-for-oracle"></a>Oracle 用 BareMetal インフラストラクチャの概要

この記事では、BareMetal インフラストラクチャによって Oracle ワークロード向けに提供される機能の概要を示します。

Oracle 用 BareMetal インフラストラクチャは、Oracle 認定の Unified Computing System (UCS) と FLexPod に基づいています。 FlexPod プラットフォームでは、事前検証済みのストレージ、ネットワーク、サーバー テクノロジが提供されます。 これにより、DirectNFS プロトコルを使用した統合を提供する、NetApp Network File System (NFS) ストレージが提供されます。 BareMetal サーバーは、個々のユーザー専用であり、BareMetal インスタンスにハイパーバイザーはありません。 

これらのインスタンスは、Oracle ワークロードを必要とするミッション クリティカルなアプリケーションを実行するためのものです。 BareMetal インスタンスにより、Azure 仮想マシン (VM) で実行されるアプリケーションに低待機時間 (0.35 ミリ秒) が提供されます。 BareMetal は共有ストレージを提供します。 また、専用のプライベート相互接続ネットワークを使用したノード間通信に必要なマルチキャストがサポートされます。 

Oracle 用 BareMetal インフラストラクチャのその他の機能は次のとおりです。

- Oracle 認定 UCS ブレード - UCSB200-M5、UCSB460-M4、UCSB480-M5
- プライベート仮想 LAN (VLAN) を使用した Oracle Real Application Clusters (RAC) ノード間 (マルチキャスト) 通信 - 40 Gb。
- Microsoft が管理するハードウェア
  - 冗長ストレージ、ネットワーク、電源、管理
  - インフラストラクチャ、修復、交換の監視
  - お客様のドメイン コントローラーへの Azure ExpressRoute の組み込み
  - 安全な物理的セキュリティおよびネットワーク セキュリティにより、すべての Azure クラウド サービスにアクセス可能

### <a name="supported-protocols"></a>サポートされるプロトコル

次のプロトコルが、Oracle ワークロード用 BareMetal サーバー内のさまざまなマウント ポイントに対して使用されます。

- OS マウント – iSCSI
- データ/ログ – NFSv3
- バックアップ/アーカイブ – NFSv4

### <a name="licensing"></a>ライセンス

- オンプレミスのオペレーティング システムと Oracle の独自ライセンスを使用します。

### <a name="operating-system"></a>オペレーティング システム

サーバーは、オペレーティング システム RHEL 7.6 を使用して事前に読み込まれます。

## <a name="next-steps"></a>次のステップ

Oracle BareMetal ワークロードの SKU について確認します。

> [!div class="nextstepaction"]
> [Oracle ワークロード向けの BareMetal SKU](oracle-baremetal-skus.md)
