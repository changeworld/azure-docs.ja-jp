---
title: Azure Migrate のサポート マトリックス
description: Azure Migrate サービスに対するサポートの設定および制限の概要について説明します。
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: raynew
ms.openlocfilehash: fa6ea1ec1992c94d44531cda9802290edf8db301
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/02/2019
ms.locfileid: "74669150"
---
# <a name="azure-migrate-support-matrix"></a>Azure Migrate のサポート マトリックス

[Azure Migrate サービス](migrate-overview.md)を使用すると、コンピューターを評価したり、Microsoft Azure クラウドに移行したりできます。 この記事では、Azure Migrate のシナリオやデプロイに対する一般的なサポートの設定および制限を要約しています。


## <a name="azure-migrate-versions"></a>Azure Migrate のバージョン

Azure Migrate サービスのバージョンには、次の 2 つがあります。

- **現在のバージョン**: このバージョンを使用すると、新しい Azure Migrate プロジェクトを作成したり、オンプレミスの評価を検出したり、評価と移行を調整したりできます。 [詳細情報](whats-new.md#release-version-july-2019)。
- **以前のバージョン**: 以前のバージョンの Azure Migrate (オンプレミスの VMware VM の評価のみがサポートされていました) を使用している顧客の場合は、現在のバージョンを使用するようにしてください。 以前のバージョンでは、新しい Azure Migrate プロジェクトを作成したり、新しい検出を実行したりできません。

## <a name="supported-assessmentmigration-scenarios"></a>サポートされる評価/移行シナリオ

次の表は、サポートされる検出、評価、および移行のシナリオをまとめたものです。

**Deployment** | **詳細** 
--- | --- 
**アプリ固有の検出** | VMware VM 上で実行されているアプリ、ロール、および機能を検出できます。 現在、この機能は検出のみに限定されています。 評価は現在マシン レベルです。 アプリ、役割、または機能に固有の評価はまだ提供されていません。 
**オンプレミスの評価** | VMware VM、Hyper-V VM、および物理サーバー上で実行されているオンプレミスのワークロードとデータを評価します。 Azure Migrate Server Assessment と Microsoft Data Migration Assistant (DMA) や、その他のツールと ISV オファリングを使用して評価します。
**Azure へのオンプレミスの移行** | 物理サーバー、VMware VM、Hyper-V VM、物理サーバー、クラウドベースの VM で実行されているワークロードとデータを Azure に移行します。 Azure Migrate Server Assessment と Azure Database Migration Service (DMS) や、その他のツールと ISV オファリングを使用して移行します。


## <a name="supported-tools"></a>サポートされているツール

次の表に、具体的なツールのサポートをまとめてあります。

**ツール** | **評価** | **移行** 
--- | --- | ---
Azure Migrate Server Assessment | [VMware VM](tutorial-prepare-vmware.md)、[Hyper-V VM](tutorial-prepare-hyper-v.md)、および[物理サーバー](tutorial-prepare-physical.md)を評価します。 |  利用できません (NA)
Azure Migrate Server Migration | NA | [VMware VM](tutorial-migrate-vmware.md)、[Hyper-V VM](tutorial-migrate-hyper-v.md)、および[物理サーバー](tutorial-migrate-physical-virtual-machines.md)を移行します。
[Carbonite](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure) | NA | VMware VM、Hyper-V VM、物理サーバー、パブリック クラウド ワークロードを移行します。 
[Cloudamize](https://www.cloudamize.com/platform#tab-0)| VMware VM、Hyper-V VM、物理サーバー、パブリック クラウド ワークロードを評価します。 | NA
[Corent Technology](https://go.microsoft.com/fwlink/?linkid=2084928) | VMware VM、Hyper-V VM、物理サーバー、パブリック クラウド ワークロードを評価および移行します。 |  VMware VM、Hyper-V VM、物理サーバー、パブリック クラウド ワークロードを移行します。
[Device 42](https://go.microsoft.com/fwlink/?linkid=2097158) | VMware VM、Hyper-V VM、物理サーバー、パブリック クラウド ワークロードを評価します。| NA
[DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | オンプレミスの SQL Server データベースを評価します。 | NA
[DMS](https://docs.microsoft.com/azure/dms/dms-overview) | NA | SQL Server、Oracle、MySQL、PostgreSQL、MongoDB を移行します。 
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | 仮想デスクトップ インフラストラクチャ (VDI) を評価します | NA
[Movere](https://go.microsoft.com/fwlink/?linkid=2109528) | VMWare VM、Hyper-V VM、Xen VM、物理マシン、ワークステーション (VDI を含む)、パブリック クラウド ワークロードを評価します | NA
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | NA | VMWare VM、Hyper-V VM、Xen VM、KVM VM、物理マシン、パブリック クラウド ワークロードを移行します 
[Turbonomic](https://go.microsoft.com/fwlink/?linkid=2094295)  | VMware VM、Hyper-V VM、物理サーバー、パブリック クラウド ワークロードを評価します。 | NA
[UnifyCloud](https://go.microsoft.com/fwlink/?linkid=2097195) | VMware VM、Hyper-V VM、物理サーバー、パブリック クラウド ワークロード、および SQL Server データベースを評価します。 | NA
[Webapp Migration Assistant](https://appmigration.microsoft.com/) | Web アプリを評価します | Web アプリを移行します。


## <a name="azure-migrate-projects"></a>Azure Migrate プロジェクト

**サポート** | **詳細**
--- | ---
Subscription | 1 つのサブスクリプションに複数の Azure Migrate プロジェクトを含めることができます。
Azure のアクセス許可 | Azure Migrate プロジェクトを作成するには、サブスクリプションに共同作成者または所有者アクセス許可が必要です。
VMware VM  | 1 つのプロジェクトで最大 35,000 の VMware VM を評価します。
Hyper-V VM | 1 つのプロジェクトで最大 35,000 の Hyper-V VM を評価します。

評価の上限に達するまでは、1 つのプロジェクトに VMware VM と Hyper-V VM の両方を含めることができます。

## <a name="supported-geographies"></a>サポートされている地域

Azure Migrate プロジェクトは、いくつかの地域で作成できます。 プロジェクトを作成できるのはこれらの地域に限られますが、ターゲットの場所がそれ以外であるマシンを評価または移行することは可能です。 プロジェクトの地域は、検出されたメタデータを格納するためにのみ使用されます。

**地理的な場所** | **メタデータ ストレージの場所**
--- | ---
Azure Government | 米国政府バージニア州
アジア太平洋 | 東アジアまたは東南アジア
オーストラリア | オーストラリア東部またはオーストラリア南東部
ブラジル | ブラジル南部
カナダ | カナダ中部またはカナダ東部
ヨーロッパ | 北ヨーロッパまたは西ヨーロッパ
フランス | フランス中部
インド | インド中部またはインド南部
日本 |  東日本または西日本
韓国 | 韓国中部または韓国南部
イギリス | 英国南部または英国西部
米国 | 米国中部または米国西部 2


 > [!NOTE]
 > Azure Government は現在、[古いバージョン](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions)の Azure Migrate でのみサポートされます。



## <a name="vmware-assessment-and-migration"></a>VMware の評価と移行

VMware VM に対する Azure Migrate Server Assessment と Server Migration のサポート マトリックスを[確認](migrate-support-matrix-vmware.md)してください。

## <a name="hyper-v-assessment-and-migration"></a>Hyper-V の評価と移行

Hyper-V VM に対する Azure Migrate Server Assessment と Server Migration のサポート マトリックスを[確認](migrate-support-matrix-hyper-v.md)してください。


## <a name="next-steps"></a>次の手順

- 移行のために [VMware VM を評価](tutorial-assess-vmware.md)します。
- 移行のために [Hyper-V VM を評価](tutorial-assess-hyper-v.md)します。

