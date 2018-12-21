---
title: 専用 HSM とは  - Azure Dedicated HSM | Microsoft Docs
description: Azure Dedicated HSM の概要では、FIPS 140-2 レベル 3 認定を満たす Azure 内でのキーの保管機能について説明します
services: dedicated-hsm
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc, seodec18
ms.date: 12/07/2018
ms.author: barclayn
ms.openlocfilehash: 1eeafa33c8c1cdbcd7d0e55e3860dda1b8d451fe
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53080080"
---
# <a name="what-is-azure-dedicated-hsm"></a>Azure Dedicated HSM とは何か

Azure Dedicated HSM は、Azure 内に暗号化キーの保管場所を提供する Azure サービスです。 Dedicated HSM は、最も厳格なセキュリティ要件に適合しています。 それは、FIPS 140-2 レベル 3 検証済みデバイスを必要としていて、HSM アプライアンスを完全かつ独占的に制御する必要のあるお客様にとって、理想的なソリューションです。 HSM デバイスは、複数の Azure リージョン全体にグローバルにデプロイされ、高可用性を確保するために一対のデバイスとして容易にプロビジョニングして構成することができます。 HSM を複数のリージョン全体にプロビジョニングすることで、リージョン レベルのフェールオーバーに対応することもできます。 Microsoft では、Gemalto の [SafeNet Luna Network HSM 7 (Model A790)](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/) アプライアンスを使用して専用 HSM サービスを提供してきました。 このデバイスは、最高レベルのパフォーマンスと暗号統合オプションを備えています。 プロビジョニングした HSM は、お客様の仮想ネットワークに直接接続されます。また、ポイント対サイト VPN 接続またはサイト間 VPN 接続を構成することで、オンプレミスのアプリケーションや管理ツールからもアクセスすることができます。 お客様には、HSM デバイスを構成して管理するためのソフトウェアとドキュメントが Gemalto のサポート ポータルを通じて提供されます。

## <a name="why-use-azure-dedicated-hsm"></a>Azure の専用 HSM を使用する理由

### <a name="fips-140-2-level-3-compliance"></a>FIPS 140-2 レベル 3 準拠

多くの組織には、暗号化キーの保管に関して [FIPS 140-2 レベル 3](https://csrc.nist.gov/publications/detail/fips/140/2/final) 要件への準拠を規定した、厳しい業界規制が存在します。 Microsoft のマルチテナント Azure Key Vault サービスが現在対応しているのは、FIPS 140-2 レベル 2 認定のみです。 Azure の専用 HSM は、金融サービス業や行政機関など、FIPS 140-2 レベル 3 要件への準拠が求められる組織の実際のニーズを満たします。

### <a name="single-tenant-devices"></a>シングル テナントのデバイス

Microsoft のお客様の多くは、暗号の保管デバイスのシングル テナントに関する要件を抱えています。 Azure の専用 HSM サービスでは、世界中に分散されている Microsoft のいずれかのデータセンターから物理デバイスをプロビジョニングすることができます。 お客様へのプロビジョニング後、そのデバイスにアクセスできるのは、そのお客様だけです。

### <a name="full-administrative-control"></a>完全な管理制御

多くのお客様は、シングル テナントのデバイスに加え、完全な管理制御や、管理目的での単独アクセスを求めています。 プロビジョニング後、デバイスに管理レベルまたはアプリケーション レベルでアクセスできるのはそのお客様だけです。 お客様による初回アクセスでパスワード変更が要求され、その後は、Microsoft が管理制御を行うことはできなくなります。 その時点からお客様は、完全な管理制御とアプリケーション管理能力を有する真のシングルテナントとなります。 ただし、温度、電源の正常性、ファンの正常性などのテレメトリについては、ハードウェア モニターをカバーするシリアル ポート接続を通じて、(管理者ロールではなく) モニター レベルのアクセス権を Microsoft が保持します。 これは必要であればお客様が自由に無効化できますが、その場合、Microsoft からプロアクティブな正常性アラートは通知されません。

### <a name="high-performance"></a>高性能

このサービスに Gemalto デバイスが選ばれた理由は、その幅広い暗号アルゴリズムのサポート、さまざまなオペレーティング システムのサポート、広範な API のサポートです。 デプロイされた特定のモデルは、RSA-2048 の場合に毎秒 10,000 操作という優れたパフォーマンスを発揮します。 一意のアプリケーションのインスタンスについて、10 個のパーティションを使用することができます。 短い待ち時間、大容量、高スループットのデバイスです。

### <a name="unique-cloud-based-offering"></a>他にはないクラウドベースのオファリング

Microsoft は、一部のお客様に固有のニーズを把握しています。FIPS 140-2 レベル 3 検証済みの専用 HSM サービスを新しいお客様に提供し、クラウドベースとオンプレミスのアプリケーション統合をこれほど広範に実現するクラウド プロバイダーは他にありません。

## <a name="is-azure-dedicated-hsm-right-for-you"></a>Azure の専用 HSM が適しているお客様

Azure の専用 HSM は、特定の種類の大規模な組織に固有の要件に対応する特殊なサービスです。 そのため、大部分の Azure のお客様には、このサービスの使用プロファイルが適さないと考えられます。 多くのお客様が、Azure Key Vault サービスの方が適しており、コスト効果に優れていると感じるものと思われます。 ご自分の要件に適しているかどうかを判断しやすいように、次の基準を特定しました。

### <a name="best-fit"></a>最適

HSM デバイスに直接かつ単独でアクセスすることが求められる "リフトアンドシフト" のシナリオには最適です。 たとえば、次のようになります。

- オンプレミスから Azure Virtual Machines にアプリケーションを移行する
- AWS Cloud HSM Classic サービスを使用するアプリケーションを Amazon AWS EC2 から Azure Virtual Machines に移行する (Amazon は、このサービスを新規顧客に提供していません)
- Azure Virtual Machines で市販のソフトウェアを実行している (Apache/Ngnix SSL Offload、Oracle TDE、ADCS など)

### <a name="not-a-fit"></a>不適

お客様によって管理されるキーを使った暗号化をサポートする Microsoft クラウド サービス (Azure Information Protection、Azure Disk Encryption、Azure Data Lake Store、Azure Storage、Azure SQL、Office 365 カスタマー キーなど) は、Azure の専用 HSM とは統合されません。

### <a name="it-depends"></a>場合による

多くのシナリオは、発生し得る複雑な要件の組み合わせや、妥協できる点とできない点によって左右されます。 その例が FIPS 140-2 レベル 3 要件です。これは多くの場合、義務付けられているため、現在は専用 HSM が唯一の選択肢となります。  こうした義務付けられている要件が該当しないようであれば、たいていは、組み合わせた要件の評価に基づいて Azure Key Vault か専用 HSM のどちらかを選ぶことになると思われます。 たとえば、次のようになります。

- お客様の Azure 仮想マシンで実行中の新しいコード
- Azure 仮想マシンにおける SQL Server TDE
- Azure Storage のクライアント側暗号化
- SQL Server と Azure SQL DB の Always Encrypted

## <a name="next-steps"></a>次の手順

このサービスのきわめて特殊な性質を考慮すると、このドキュメント一式に記載されている主要概念の一部のほか、価格、サポート、サービス レベル アグリーメントについて十分に理解しておくことをお勧めします。さらに、既存の仮想ネットワーク環境への HSM のプロビジョニングを支援するチュートリアルも用意されています。 デプロイ アーキテクチャを決める際には、[Gemalto 統合ガイド](https://safenet.gemalto.com/partners/microsoft/)と攻略ガイドも非常に参考になります。

* [高可用性](high-availability.md)
* [物理的なセキュリティ](physical-security.md)
* [ネットワーク](networking.md)
* [サポート可能性](supportability.md)
* [監視](monitoring.md)
