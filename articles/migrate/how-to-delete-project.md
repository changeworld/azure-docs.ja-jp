---
title: Azure Migrate プロジェクトの削除
description: この記事では、Azure portal を使用して Azure Migrate プロジェクトを作成する方法について説明します。
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: how-to
ms.date: 10/22/2019
ms.openlocfilehash: 8c94bb23f5d514fef5cdacb855657efdf5219631
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714742"
---
# <a name="delete-an-azure-migrate-project"></a>Azure Migrate プロジェクトの削除

この記事では、[Azure Migrate](./migrate-services-overview.md) プロジェクトを削除する方法について説明します。


## <a name="before-you-start"></a>開始する前に

プロジェクトを削除する前に:

- プロジェクトを削除すると、プロジェクトと、検出されたコンピューターのメタデータが削除されます。
- 依存関係分析のために Log Analytics ワークスペースをサーバー評価ツールに接続済みの場合、ワークスペースを削除するかどうかを決定します。 
    - ワークスペースは自動的には削除されません。 手動で削除してください。
    - ワークスペースを削除する前に、その使用目的を確認します。 同じ Log Analytics ワークスペースが、複数のシナリオで使用されている可能性があります。
    - プロジェクトを削除する前に、 **[Azure Migrate - Servers]\(Azure Migrate - サーバー\)**  >  **[Azure Migrate - Server Assessment]\(Azure Migrate - サーバー評価\)** の **[OMS ワークスペース]** でワークスペースへのリンクを確認できます。
    - プロジェクトを削除した後にワークスペースを削除するには、関連するリソース グループからワークスペースを探して、[ここに記載の手順](../azure-monitor/logs/delete-workspace.md)に従います。


## <a name="delete-a-project"></a>プロジェクトを削除する


1. Azure portal で、プロジェクトが作成されたリソース グループを開きます。
2. リソース グループ ページで **[非表示の型の表示]** を選択します。
3. 削除するプロジェクトと、関連付けられているリソースを選択します。
    - Azure Migrate プロジェクトのリソースの種類は **Microsoft.Migrate/migrateprojects** です。
    - 次のセクションでは、Azure Migrate プロジェクトで検出、評価、移行のために作成されたリソースを確認します。
    - リソース グループに Azure Migrate プロジェクトしか含まれていない場合は、リソース グループ全体を削除できます。
    - 以前のバージョンの Azure Migrate からプロジェクトを削除する場合、手順は同じです。 これらのプロジェクトのリソースの種類は、**移行プロジェクト** です。


## <a name="created-resources"></a>作成されたリソース

以下の表は、Azure Migrate プロジェクトで検出、評価、移行のために作成されたリソースの要約です。

> [!NOTE]
> キー コンテナーは、セキュリティ キーが含まれている可能性があるため、注意して削除してください。

### <a name="projects-with-public-endpoint-connectivity"></a>パブリック エンドポイント接続を使用するプロジェクト

#### <a name="vmwarephysical-server"></a>VMware および物理サーバー

**リソース** | **Type**
--- | ---
"Appliancename"kv | Key Vault
"Appliancename"site | Microsoft.OffAzure/VMwareSites
"ProjectName" | Microsoft.Migrate/migrateprojects
"ProjectName"project | Microsoft.Migrate/assessmentProjects
"ProjectName"rsvault | Recovery Services コンテナー
"ProjectName"-MigrateVault-* | Recovery Services コンテナー
migrateappligwsa* | ストレージ アカウント
migrateapplilsa* | ストレージ アカウント
migrateapplicsa* | ストレージ アカウント
migrateapplikv* | Key Vault
migrateapplisbns* | Service Bus 名前空間

#### <a name="hyper-v-vm"></a>Hyper-V VM

**リソース** | **Type**
--- | ---
"ProjectName" | Microsoft.Migrate/migrateprojects
"ProjectName"project | Microsoft.Migrate/assessmentProjects
HyperV*kv | Key Vault
HyperV*Site | Microsoft.OffAzure/HyperVSites
"ProjectName"-MigrateVault-* | Recovery Services コンテナー

<br/>
次の表は、[Azure Private Link](./how-to-use-azure-migrate-with-private-endpoints.md) を使用してプライベート ネットワーク経由でサーバーを検出、評価、移行するために Azure Migrate によって作成されるリソースをまとめたものです。

### <a name="projects-with-private-endpoint-connectivity"></a>プライベート エンドポイント接続を使用するプロジェクト

#### <a name="vmware-vms---agentless-migrations"></a>VMware VM - エージェントレス移行

**Type** | **リソース** | **プライベート エンドポイント<br/>** |
--- | --- | ---
Microsoft.Migrate/migrateprojects | "ProjectName" | "ProjectName"\*pe 
探索サイト (マスター サイト) | "ProjectName"*mastersite | "ProjectName"\*mastersite\*pe 
Microsoft.Migrate/assessmentProjects | "ApplianceName"*project | "ApplianceName"\*project\*pe 
Key Vault | "ProjectName"*kv | "ProjectName"\*kv\*pe
Microsoft.OffAzure/VMwareSites | "ApplianceName"*site | NA
Recovery Services コンテナー | "ApplianceName"*vault | NA
ストレージ アカウント | "ApplianceName"*usa | "ApplianceName"\*usa\*pe
Recovery Services コンテナー | "ProjectName"-MigrateVault-* | NA
ストレージ アカウント | migrateappligwsa* | NA
ストレージ アカウント | migrateapplilsa* | NA
Key Vault | migrateapplikv* | NA
Service Bus 名前空間 | migrateapplisbns* | NA

#### <a name="hyper-v-vms"></a>Hyper-V VM 

**Type** | **リソース** | **プライベート エンドポイント<br/>** |
--- | --- | ---
Microsoft.Migrate/migrateprojects | "ProjectName" | "ProjectName"\*pe 
探索サイト (マスター サイト) | "ProjectName"*mastersite | "ProjectName"\*mastersite\*pe 
Microsoft.Migrate/assessmentProjects | "ApplianceName"*project | "ApplianceName"\*project\*pe 
Key Vault | "ProjectName"*kv | "ProjectName"\*kv\*pe
Microsoft.OffAzure/HyperVSites | "ApplianceName"*site | NA
Recovery Services コンテナー | "ProjectName"-MigrateVault-* | "ProjectName"-MigrateVault-*pe

#### <a name="physical-servers--aws-vms--gcp-vms"></a>物理サーバー/AWS VM/GCP VM 

**Type** | **リソース** | **プライベート エンドポイント<br/>** |
--- | --- | ---
Microsoft.Migrate/migrateprojects | "ProjectName" | "ProjectName"\*pe 
探索サイト (マスター サイト) | "ProjectName"*mastersite | "ProjectName"\*mastersite\*pe 
Microsoft.Migrate/assessmentProjects | "ApplianceName"*project | "ApplianceName"\*project\*pe 
Key Vault | "ProjectName"*kv | "ProjectName"\*kv\*pe
Microsoft.OffAzure/serversites | "ApplianceName"*site | NA
Recovery Services コンテナー | "ProjectName"-MigrateVault-* | "ProjectName"-MigrateVault-*pe


## <a name="next-steps"></a>次のステップ

追加の[評価](how-to-assess.md)ツールと[移行](how-to-migrate.md)ツールを追加する方法を確認します。 
