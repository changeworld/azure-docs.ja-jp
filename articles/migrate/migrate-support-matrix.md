---
title: Azure Migrate のサポート マトリックス
description: Azure Migrate サービスに対するサポートの設定および制限の概要について説明します。
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: af0b8a4d3dfbce32e412f5294fb19ade61fd7661
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105628171"
---
# <a name="azure-migrate-support-matrix"></a>Azure Migrate のサポート マトリックス

[Azure Migrate サービス](./migrate-services-overview.md)を使用すると、サーバーを評価したり、Microsoft Azure クラウドに移行したりできます。 この記事では、Azure Migrate のシナリオやデプロイに対する一般的なサポートの設定および制限を要約しています。

## <a name="supported-assessmentmigration-scenarios"></a>サポートされる評価/移行シナリオ

次の表は、サポートされる検出、評価、および移行のシナリオをまとめたものです。

**デプロイ** | **詳細** 
--- | --- 
**検出** | サーバーのメタデータと動的なパフォーマンス データを検出できます。
**アプリ検出** | VMware VM 上で実行されているアプリ、ロール、および機能を検出できます。 現在、この機能は検出のみに限定されています。 評価は現在サーバー レベルです。 アプリ、役割、または機能ベースの評価はまだ提供されていません。 
**評価** | VMware VM、Hyper-V VM、および物理サーバー上で実行されているオンプレミスのワークロードとデータを評価します。 Azure Migrate: Server Assessment、Microsoft Data Migration Assistant (DMA)、さらにその他のツールと ISV オファリングを使用して評価します。
**移行** | 物理サーバー、VMware VM、Hyper-V VM、物理サーバー、クラウドベースの VM で実行されているワークロードとデータを Azure に移行します。 Azure Migrate: Server Assessment と Azure Database Migration Service (DMS) や、その他のツールと ISV オファリングを使用して移行します。

> [!NOTE]
> 現在、ISV ツールは Azure Government で Azure Migrate にデータを送信できません。 統合された Microsoft ツールを使用するか、パートナー ツールを単独で使用できます。

## <a name="supported-tools"></a>サポートされているツール


次の表に、具体的なツールのサポートをまとめてあります。

**ツール** | **評価** | **移行** 
--- | --- | ---
Azure Migrate: Server Assessment | [VMware VM](./tutorial-discover-vmware.md)、[Hyper-V VM](./tutorial-discover-hyper-v.md)、および[物理サーバー](./tutorial-discover-physical.md)を評価します。 |  利用できません (N/A)
Azure Migrate: Server Migration | 該当なし | [VMware VM](tutorial-migrate-vmware.md)、[Hyper-V VM](tutorial-migrate-hyper-v.md)、および[物理サーバー](tutorial-migrate-physical-virtual-machines.md)を移行します。
[Carbonite](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure) | 該当なし | VMware VM、Hyper-V VM、物理サーバー、およびその他のクラウド ワークロードを移行します。 
[Cloudamize](https://www.cloudamize.com/platform#tab-0)| VMware VM、Hyper-V VM、物理サーバー、およびその他のクラウド ワークロードを評価します。 | 該当なし
[Corent Technology](https://go.microsoft.com/fwlink/?linkid=2084928) | VMware VM、Hyper-V VM、物理サーバー、およびその他のクラウド ワークロードを評価します。 |  VMware VM、Hyper-V VM、物理サーバー、パブリック クラウド ワークロードを移行します。
[Device 42](https://go.microsoft.com/fwlink/?linkid=2097158) | VMware VM、Hyper-V VM、物理サーバー、およびその他のクラウド ワークロードを評価します。| 該当なし
[DMA](/sql/dma/dma-overview) | SQL Server データベースを評価します。 | 該当なし
[DMS](../dms/dms-overview.md) | 該当なし | SQL Server、Oracle、MySQL、PostgreSQL、MongoDB を移行します。 
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | 仮想デスクトップ インフラストラクチャ (VDI) を評価します | 該当なし
[Movere](https://www.movere.io/) | VMware VM、Hyper-V VM、Xen VM、物理サーバー、ワークステーション (VDI を含む)、およびその他のクラウド ワークロードを評価します。 | 該当なし
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | 該当なし | VMWare VM、Hyper-V VM、Xen VM、KVM VM、物理サーバー、およびその他のクラウド ワークロードを移行します 
[Turbonomic](https://go.microsoft.com/fwlink/?linkid=2094295)  | VMware VM、Hyper-V VM、物理サーバー、およびその他のクラウド ワークロードを評価します。 | 該当なし
[UnifyCloud](https://go.microsoft.com/fwlink/?linkid=2097195) | VMware VM、Hyper-V VM、物理サーバー、およびその他のクラウド ワークロード、および SQL Server データベースを評価します。 | 該当なし
[Webapp Migration Assistant](https://appmigration.microsoft.com/) | Web アプリを評価します | Web アプリを移行します。
[Zerto](https://go.microsoft.com/fwlink/?linkid=2157322) | 該当なし |  VMware VM、Hyper-V VM、物理サーバー、およびその他のクラウド ワークロードを移行します。


## <a name="project"></a>Project

**サポート** | **詳細**
--- | ---
サブスクリプション | サブスクリプション内に複数のプロジェクトを含めることができます。
Azure のアクセス許可 | ユーザーがプロジェクトを作成するには、サブスクリプションに共同作成者または所有者アクセス許可が必要です。
VMware VM  | 1 つのプロジェクトで最大 35,000 台の VMware VM を評価できます。
Hyper-V VM    | 1 つのプロジェクトで最大 35,000 の Hyper-V VM を評価します。

評価の上限に達するまでは、1 つのプロジェクトに VMware VM と Hyper-V VM の両方を含めることができます。

## <a name="azure-permissions"></a>Azure のアクセス許可

Azure Migrate が Azure で機能するためには、サーバーの評価と移行を開始する前に、次のアクセス許可が必要です。

**タスク** | **アクセス許可** | **詳細**
--- | --- | ---
プロジェクトの作成 | Azure アカウントには、プロジェクトを作成するためのアクセス許可が必要です。 | [VMware](./tutorial-discover-vmware.md#prepare-an-azure-user-account)、[Hyper-V](./tutorial-discover-hyper-v.md#prepare-an-azure-user-account)、または[物理サーバー](./tutorial-discover-physical.md#prepare-an-azure-user-account)の設定。
Azure Migrate アプライアンスの登録| Azure Migrate では、軽量の [Azure Migrate アプライアンス](migrate-appliance.md)を使用して Azure Migrate Server Assessment でサーバーが評価され、Azure Migrate Server Migration で VMware VM の[エージェントレス移行](server-migrate-overview.md)が実行されます。 このアプライアンスによってサーバーが検出され、メタデータとパフォーマンス データが Azure Migrate に送信されます。<br/><br/> 登録時に登録プロバイダー (Microsoft.OffAzure、Microsoft.Migrate、および Microsoft.KeyVault) が、アプライアンスで選択されたサブスクリプションに登録され、その結果、サブスクリプションがリソース プロバイダーで動作するようになります。 登録するには、サブスクリプションに対する共同作成者または所有者のアクセス権が必要です。<br/><br/> **VMware** - オンボード中、Azure Migrate では 2 つの Azure Active Directory (Azure AD) アプリが作成されます。 最初のアプリによって、アプライアンス エージェントと Azure Migrate サービスとの間の通信が行われます。 このアプリには、Azure リソース管理を呼び出すためのアクセス許可も、リソースに対する Azure RBAC アクセス権もありません。 2 番目のアプリでは、エージェントレスの VMware 移行専用のユーザー サブスクリプションで作成された Azure Key Vault へのアクセスが行われます。 エージェントレスの移行では、ご利用のサブスクリプションのレプリケーション ストレージ アカウントへのアクセス キーを管理するためのキー コンテナーが Azure Migrate によって作成されます。 アプライアンスから検出が開始されると、(カスタマー テナント内の) Azure キー コンテナーで Azure RBAC アクセスが可能になります。<br/><br/> **Hyper-V** - オンボード中。 Azure Migrate によって Azure AD アプリが 1 つ作成されます。 このアプリによって、アプライアンス エージェントと Azure Migrate サービスとの間の通信が行われます。 このアプリには、Azure リソース管理を呼び出すためのアクセス許可も、リソースに対する Azure RBAC アクセス権もありません。 | [VMware](./tutorial-discover-vmware.md#prepare-an-azure-user-account)、[Hyper-V](./tutorial-discover-hyper-v.md#prepare-an-azure-user-account)、または[物理サーバー](./tutorial-discover-physical.md#prepare-an-azure-user-account)の設定。
VMware エージェントレス移行用のキー コンテナーの作成 | エージェントレスの Azure Migrate: Server Migration を使用して VMware VM を移行するために、ご利用のサブスクリプションのレプリケーション ストレージ アカウントへのアクセス キーを管理するためのキー コンテナーが Azure Migrate によって作成されます。 コンテナーを作成するには、プロジェクトが存在するリソース グループに、アクセス許可 (所有者、共同作成者、およびユーザー アクセス管理者) を設定する必要があります。 | アクセス許可の[設定](./tutorial-discover-vmware.md#prepare-an-azure-user-account)。

## <a name="supported-geographies-public-cloud"></a>サポートされている地域 (パブリック クラウド)

プロジェクトは、パブリック クラウドの複数の地域で作成できます。

- プロジェクトを作成できるのはこれらの地域に限られますが、ターゲットの場所がそれ以外であるサーバーを評価または移行することは可能です。
- プロジェクトの地域は、検出されたメタデータを格納するためにのみ使用されます。
- プロジェクトを作成するときに、地域を選択します。 プロジェクトと関連リソースは、地域のいずれかのリージョンに作成されます。 このリージョンは、Azure Migrate サービスによって割り当てられます。

**地理的な場所** | **メタデータ ストレージの場所**
--- | ---
アジア太平洋 | 東アジアまたは東南アジア
オーストラリア | オーストラリア東部またはオーストラリア南東部
ブラジル | ブラジル南部
カナダ | カナダ中部またはカナダ東部
ヨーロッパ | 北ヨーロッパまたは西ヨーロッパ
フランス | フランス中部
インド | インド中部またはインド南部
日本 |  東日本または西日本
韓国 | 韓国中部または韓国南部
スイス | スイス北部
イギリス | 英国南部または英国西部
アメリカ | 米国中部または米国西部 2

> [!NOTE]
> スイス地域については、REST API ユーザーに限りスイス西部が利用でき、承認済みのサブスクリプションが必要となります。

## <a name="supported-geographies-azure-government"></a>サポートされている地域 (Azure Government)

**タスク** | **地理的な場所** | **詳細**
--- | --- | ---
Create project | United States | メタデータは US Gov アリゾナ、US Gov バージニアに格納されます
ターゲットの評価 | United States | ターゲット リージョン: US Gov アリゾナ、US Gov バージニア、US Gov テキサス
ターゲットのレプリケーション | United States | ターゲット リージョン: US DoD 中部、US DoD 東部、US Gov アリゾナ、US Gov アイオワ、US Gov テキサス、US Gov バージニア


## <a name="vmware-assessment-and-migration"></a>VMware の評価と移行

[以下を確認します](migrate-support-matrix-vmware.md)。VMware VM に対する Azure Migrate: Server Assessment、Azure Migrate: Server Migration のサポート マトリックス。

## <a name="hyper-v-assessment-and-migration"></a>Hyper-V の評価と移行

[以下を確認します](migrate-support-matrix-hyper-v.md)。Hyper-V VM に対する Azure Migrate: Server Assessment、Azure Migrate: Server Migration のサポート マトリックス。



## <a name="azure-migrate-versions"></a>Azure Migrate のバージョン

Azure Migrate サービスのバージョンには、次の 2 つがあります。

- **現在のバージョン**: このバージョンを使用すると、新しいプロジェクトを作成したり、オンプレミスの評価を検出したり、評価と移行を調整したりできます。 [詳細については、こちらを参照してください](whats-new.md)。
- **以前のバージョン**: 以前のバージョンの Azure Migrate (オンプレミスの VMware VM の評価のみがサポートされていました) を使用している顧客の場合は、現在のバージョンを使用するようにしてください。 以前のバージョンでは、新しいプロジェクトを作成したり、新しい検出を実行したりできません。

## <a name="next-steps"></a>次のステップ

- 移行のために [VMware VM を評価](./tutorial-assess-vmware-azure-vm.md)します。
- 移行のために [Hyper-V VM を評価](tutorial-assess-hyper-v.md)します。
