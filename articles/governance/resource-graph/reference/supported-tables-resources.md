---
title: サポートされている Azure Resource Manager のリソースの種類
description: Azure Resource Graph でサポートされている Azure Resource Manager のリソースの種類と変更履歴の一覧を示します。
ms.date: 10/12/2021
ms.topic: reference
ms.custom: generated
ms.openlocfilehash: 9b3ce54d9554164b375ed1a08fdbcce8429dbb21
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132054811"
---
# <a name="azure-resource-graph-table-and-resource-type-reference"></a>Azure Resource Graph のテーブルとリソースの種類のリファレンス

Azure Resource Graph では、[Azure Resource Manager](../../../azure-resource-manager/management/overview.md) の次の **リソースの種類** がサポートされています。 各 **リソースの種類** は、Resource Graph の **テーブル** に含まれます。

## <a name="advisorresources"></a>advisorresources

このテーブルのサンプル クエリについては、[advisorresources の Resource Graph サンプル クエリ](../samples/samples-by-table.md#advisorresources)に関するページを参照してください。

- microsoft.advisor/configurations
- microsoft.advisor/recommendations
  - サンプル クエリ: [Azure Advisor からコスト削減の概要を取得する](../samples/samples-by-category.md#get-cost-savings-summary-from-azure-advisor)
  - サンプル クエリ: [最新リリース バージョンのエージェントを実行していない Arc 対応サーバーを一覧表示する](../samples/samples-by-category.md#list-arc-enabled-servers-not-running-latest-released-agent-version)
- microsoft.advisor/recommendations/suppressions
- microsoft.advisor/suppressions

## <a name="alertsmanagementresources"></a>alertsmanagementresources

- microsoft.alertsmanagement/alerts

## <a name="desktopvirtualizationresources"></a>desktopvirtualizationresources

- microsoft.chaos/experiments/statuses
- microsoft.chaos/targets
- microsoft.chaos/targets/capabilities
- microsoft.desktopvirtualization/hostpools/sessionhosts

## <a name="extendedlocationresources"></a>extendedlocationresources

このテーブルのサンプル クエリについては、[extendedlocationresources の Resource Graph サンプル クエリ](../samples/samples-by-table.md#extendedlocationresources)に関するページを参照してください。

- microsoft.extendedlocation/customlocations/enabledresourcetypes
  - サンプル クエリ: [Azure Arc 対応のカスタムの場所に対して有効になっているリソースの種類を取得する](../samples/samples-by-category.md#get-enabled-resource-types-for-azure-arc-enabled-custom-locations)
  - サンプル クエリ: [VMware または SCVMM が有効になっている Azure Arc 対応のカスタムの場所を一覧表示する](../samples/samples-by-category.md#list-azure-arc-enabled-custom-locations-with-vmware-or-scvmm-enabled)

## <a name="guestconfigurationresources"></a>guestconfigurationresources

このテーブルのサンプル クエリについては、[guestconfigurationresources の Resource Graph サンプル クエリ](../samples/samples-by-table.md#guestconfigurationresources)に関するページを参照してください。

- microsoft.guestconfiguration/guestconfigurationassignments
  - サンプル クエリ: [ゲスト構成ポリシーのスコープにあるコンピューターを数える](../samples/samples-by-category.md#count-machines-in-scope-of-guest-configuration-policies)
  - サンプル クエリ: [非準拠ゲスト構成割り当ての数](../samples/samples-by-category.md#count-of-non-compliant-guest-configuration-assignments)
  - サンプル クエリ: [コンピューターがゲスト構成割り当てに準拠していない理由をすべて見つける](../samples/samples-by-category.md#find-all-reasons-a-machine-is-non-compliant-for-guest-configuration-assignments)

## <a name="healthresources"></a>healthresources

このテーブルのサンプル クエリについては、[healthresources の Resource Graph サンプル クエリ](../samples/samples-by-table.md#healthresources)に関するページを参照してください。

- microsoft.resourcehealth/availabilitystatuses
  - サンプル クエリ: [可用性の状態とサブスクリプション ID 別の仮想マシンの数](../samples/samples-by-category.md#count-of-virtual-machines-by-availability-state-and-subscription-id)
  - サンプル クエリ: [リソース ID 別の仮想マシンおよび関連する可用性状態の一覧](../samples/samples-by-category.md#list-of-virtual-machines-and-associated-availability-states-by-resource-ids)
  - サンプル クエリ: [可用性状態と電源状態別の仮想マシンの一覧と、そのリソース ID およびリソース グループ](../samples/samples-by-category.md#list-of-virtual-machines-by-availability-state-and-power-state-with-resource-ids-and-resource-groups)
  - サンプル クエリ: [リソース ID で使用できない仮想マシンの一覧](../samples/samples-by-category.md#list-of-virtual-machines-that-are-not-available-by-resource-ids)

## <a name="iotsecurityresources"></a>iotsecurityresources

- microsoft.iotsecurity/locations/devicegroups/alerts
  - サンプル クエリ: [過去 30 日間の新しいアラートをすべて取得する](../samples/samples-by-category.md#get-all-new-alerts-from-the-past-30-days)
- microsoft.iotsecurity/locations/devicegroups/devices
  - サンプル クエリ: [ネットワーク内に存在する IoT デバイスの数をオペレーション システム別にカウントする](../samples/samples-by-category.md#count-how-many-iot-devices-there-are-in-your-network-by-operation-system)
- microsoft.iotsecurity/locations/devicegroups/recommendations
  - サンプル クエリ: [重要度が高い推奨事項をすべて取得する](../samples/samples-by-category.md#get-all-high-severity-recommendations)
- microsoft.iotsecurity/onpremisesensors
- microsoft.iotsecurity/sensors
  - サンプル クエリ: [すべてのセンサーを種類別にカウントする](../samples/samples-by-category.md#count-all-sensors-by-type)
- microsoft.iotsecurity/sites
  - サンプル クエリ: [特定のタグ値を含むサイトを一覧表示する](../samples/samples-by-category.md#list-sites-with-a-specific-tag-value)

## <a name="kubernetesconfigurationresources"></a>kubernetesconfigurationresources

このテーブルのサンプル クエリについては、[kubernetesconfigurationresources の Resource Graph サンプル クエリ](../samples/samples-by-table.md#kubernetesconfigurationresources)に関するページを参照してください。

- microsoft.kubernetesconfiguration/extensions
  - サンプル クエリ: [Azure Monitor 拡張機能のあるすべての Azure Arc 対応 Kubernetes クラスターを一覧表示する](../samples/samples-by-category.md#list-all-azure-arc-enabled-kubernetes-clusters-with-azure-monitor-extension)
  - サンプル クエリ: [Azure Monitor 拡張機能がないすべての Azure Arc 対応 Kubernetes クラスターを一覧表示する](../samples/samples-by-category.md#list-all-azure-arc-enabled-kubernetes-clusters-without-azure-monitor-extension)
- microsoft.kubernetesconfiguration/fluxconfigurations
- microsoft.kubernetesconfiguration/sourcecontrolconfigurations

## <a name="maintenanceresources"></a>maintenanceresources

- microsoft.maintenance/applyupdates
- microsoft.maintenance/configurationassignments
- microsoft.maintenance/updates

## <a name="patchassessmentresources"></a>patchassessmentresources

このテーブルのサンプル クエリについては、[patchassessmentresourcess の Resource Graph サンプル クエリ](../samples/samples-by-table.md#patchassessmentresources)に関するページを参照してください。

- microsoft.compute/virtualmachines/patchassessmentresults
- microsoft.compute/virtualmachines/patchassessmentresults/softwarepatches
- microsoft.hybridcompute/machines/patchassessmentresults
- microsoft.hybridcompute/machines/patchassessmentresults/softwarepatches

## <a name="patchinstallationresources"></a>patchinstallationresources

- microsoft.compute/virtualmachines/patchinstallationresults
- microsoft.compute/virtualmachines/patchinstallationresults/softwarepatches
- microsoft.hybridcompute/machines/patchinstallationresults
- microsoft.hybridcompute/machines/patchinstallationresults/softwarepatches

## <a name="policyresources"></a>policyresources

このテーブルのサンプル クエリについては、[policyresources の Resource Graph サンプル クエリ](../samples/samples-by-table.md#policyresources)に関するページを参照してください。

- microsoft.policyinsights/policystates
  - サンプル クエリ: [ポリシーの割り当て別のコンプライアンス](../samples/samples-by-category.md#compliance-by-policy-assignment)
  - サンプル クエリ: [リソースの種類別のコンプライアンス](../samples/samples-by-category.md#compliance-by-resource-type)
  - サンプル クエリ: [準拠していないリソースを一覧表示する](../samples/samples-by-category.md#list-all-non-compliant-resources)
  - サンプル クエリ: [状態別にリソースのコンプライアンスの概要を表示する](../samples/samples-by-category.md#summarize-resource-compliance-by-state)
  - サンプル クエリ: [場所ごとに状態別でリソースのコンプライアンスの概要を表示する](../samples/samples-by-category.md#summarize-resource-compliance-by-state-per-location)

## <a name="recoveryservicesresources"></a>recoveryservicesresources

- microsoft.dataprotection/backupvaults/backupinstances
- microsoft.dataprotection/backupvaults/backupjobs
- microsoft.dataprotection/backupvaults/backuppolicies
- microsoft.recoveryservices/vaults/alerts
- Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems (バックアップ項目)
- microsoft.recoveryservices/vaults/backupjobs
- microsoft.recoveryservices/vaults/backuppolicies

## <a name="resourcecontainers"></a>resourcecontainers

このテーブルのサンプル クエリについては、[resourcecontainers の Resource Graph サンプル クエリ](../samples/samples-by-table.md#resourcecontainers)に関するページを参照してください。

- microsoft.management/managementgroups
  - サンプル クエリ: [管理グループあたりのサブスクリプションの数](../samples/samples-by-category.md#count-of-subscriptions-per-management-group)
  - サンプル クエリ: [指定された管理グループのすべての管理グループの先祖を一覧表示する](../samples/samples-by-category.md#list-all-management-group-ancestors-for-a-specified-management-group)
- Microsoft.Resources/subscriptions/resourceGroups (リソース グループ)
  - サンプル クエリ: [2 つのクエリの結果を結合して 1 つの結果にする](../samples/samples-by-category.md)
  - サンプル クエリ: [リソース グループ上の特定の大文字と小文字が区別されないタグを含んだストレージ アカウントを検索する](../samples/samples-by-category.md#find-storage-accounts-with-a-specific-case-insensitive-tag-on-the-resource-group)
  - サンプル クエリ: [リソース グループ上の特定の大文字と小文字が区別されたタグを含んだストレージ アカウントを検索する](../samples/samples-by-category.md#find-storage-accounts-with-a-specific-case-sensitive-tag-on-the-resource-group)

## <a name="resources"></a>resources

このテーブルのサンプル クエリについては、[resources の Resource Graph サンプル クエリ](../samples/samples-by-table.md#resources)に関するページを参照してください。

- 84codes.CloudAMQP/servers (CloudAMQP)
- Citrix.Services/XenAppEssentials (Citrix Virtual Apps Essentials)
- Citrix.Services/XenDesktopEssentials (Citrix Virtual Desktops Essentials)
- conexlink.mycloudit/accounts
- crypteron.datasecurity/apps
- Dynatrace.Observability/monitors (Dynatrace)
- GitHub.Enterprise/accounts (GitHub AE)
- gridpro.evops/accounts
- gridpro.evops/accounts/eventrules
- gridpro.evops/accounts/requesttemplates
- gridpro.evops/accounts/views
- hive.streaming/services
- incapsula.waf/accounts
- LiveArena.Broadcast/services (LiveArena ブロードキャスト)
- Mailjet.Email/services (Mailjet メール サービス)
- micorosft.web/kubeenvironments
- Microsoft.AAD/domainServices (Azure AD Domain Services)
- microsoft.aadiam/azureadmetrics
- microsoft.aadiam/privateLinkForAzureAD (Azure AD 用プライベート リンク)
- microsoft.aadiam/tenants
- Microsoft.AgFoodPlatform/farmBeats (Azure FarmBeats)
- microsoft.aisupercomputer/accounts
- microsoft.aisupercomputer/accounts/jobgroups
- microsoft.aisupercomputer/accounts/jobgroups/jobs
- microsoft.alertsmanagement/actionrules
- microsoft.alertsmanagement/resourcehealthalertrules
- microsoft.alertsmanagement/smartdetectoralertrules
- Microsoft.AnalysisServices/servers (Analysis Services)
- Microsoft.AnyBuild/clusters (AnyBuild クラスター)
- Microsoft.ApiManagement/service (API Management サービス)
- microsoft.appassessment/migrateprojects
- Microsoft.AppConfiguration/configurationStores (App Configuration)
- Microsoft.AppPlatform/Spring (Azure Spring Cloud)
- microsoft.archive/collections
- Microsoft.Attestation/attestationProviders (構成証明プロバイダー)
- microsoft.authorization/elevateaccessroleassignment
- Microsoft.Authorization/resourceManagementPrivateLinks (リソース管理のプライベート リンク)
- microsoft.automanage/accounts
- microsoft.automanage/configurationprofilepreferences
- microsoft.automanage/configurationprofiles
- Microsoft.Automation/AutomationAccounts (Automation アカウント)
- microsoft.automation/automationaccounts/configurations
- Microsoft.Automation/automationAccounts/runbooks (Runbook)
- microsoft.autonomousdevelopmentplatform/accounts
- Microsoft.AutonomousSystems/workspaces (Bonsai)
- Microsoft.AVS/privateClouds (AVS プライベート クラウド)
- microsoft.azconfig/configurationstores
- Microsoft.AzureActiveDirectory/b2cDirectories (B2C テナント)
- Microsoft.AzureActiveDirectory/guestUsages (ゲストの使用状況)
- Microsoft.AzureArcData/dataControllers (Azure Arc データ コントローラー)
- Microsoft.AzureArcData/postgresInstances (Azure Arc 対応 PostgreSQL Hyperscale サーバー グループ)
- Microsoft.AzureArcData/sqlManagedInstances (SQL マネージド インスタンス - Azure Arc)
- Microsoft.AzureArcData/sqlServerInstances (SQL Server - Azure Arc)
- microsoft.azurecis/autopilotenvironments
- microsoft.azurecis/dstsserviceaccounts
- microsoft.azurecis/dstsserviceclientidentities
- microsoft.azuredata/datacontrollers
- microsoft.azuredata/hybriddatamanagers
- microsoft.azuredata/postgresinstances
- microsoft.azuredata/sqlbigdataclusters
- microsoft.azuredata/sqlinstances
- microsoft.azuredata/sqlmanagedinstances
- microsoft.azuredata/sqlserverinstances
- Microsoft.AzureData/sqlServerRegistrations (SQL Server レジストリ)
- Microsoft.AzurePercept/accounts (Azure Percept アカウント)
- microsoft.azuresphere/catalogs
- microsoft.azuresphere/catalogs/products
- microsoft.azuresphere/catalogs/products/devicegroups
- microsoft.azurestack/edgesubscriptions
- microsoft.azurestack/linkedsubscriptions
- Microsoft.Azurestack/registrations (Azure Stack Hub)
- Microsoft.AzureStackHCI/clusters (Azure Stack HCI)
- microsoft.azurestackhci/galleryimages
- microsoft.azurestackhci/networkinterfaces
- microsoft.azurestackhci/virtualharddisks
- Microsoft.AzureStackHCI/virtualmachines (Azure Stack HCI 仮想マシン - Azure Arc)
- microsoft.azurestackhci/virtualmachines/extensions
- microsoft.azurestackhci/virtualnetworks
- microsoft.backupsolutions/vmwareapplications
- microsoft.baremetal/consoleconnections
- Microsoft.BareMetal/crayServers (Cray サーバー)
- Microsoft.BareMetal/monitoringServers (監視サーバー)
- Microsoft.BareMetalInfrastructure/bareMetalInstances (BareMetal インスタンス)
- Microsoft.Batch/batchAccounts (Batch アカウント)
- microsoft.batchai/clusters
- microsoft.batchai/fileservers
- microsoft.batchai/jobs
- microsoft.batchai/workspaces
- Microsoft.Bing/accounts (Bing リソース)
- microsoft.bingmaps/mapapis
- microsoft.biztalkservices/biztalk
- Microsoft.Blockchain/blockchainMembers (Azure Blockchain Service)
- Microsoft.Blockchain/cordaMembers (Corda)
- Microsoft.Blockchain/watchers (Blockchain Data Manager)
- Microsoft.BotService/botServices (Bot Service)
- Microsoft.Cache/Redis (Azure Cache for Redis)
- Microsoft.Cache/RedisEnterprise (Redis Enterprise)
- microsoft.cascade/sites
- Microsoft.Cdn/CdnWebApplicationFirewallPolicies (Web アプリケーション ファイアウォール ポリシー (WAF))
- microsoft.cdn/profiles (Front Doors Standard/Premium (プレビュー))
- Microsoft.Cdn/Profiles/AfdEndpoints (エンドポイント)
- microsoft.cdn/profiles/endpoints (エンドポイント)
- Microsoft.CertificateRegistration/certificateOrders (App Service 証明書)
- microsoft.chaos/chaosexperiments (カオス実験 (クラシック))
- microsoft.chaos/experiments (カオス実験)
- microsoft.classicCompute/domainNames (クラウド サービス (クラシック))
- Microsoft.ClassicCompute/VirtualMachines (仮想マシン (クラシック))
- Microsoft.ClassicNetwork/networkSecurityGroups (ネットワーク セキュリティ グループ (クラシック))
- Microsoft.ClassicNetwork/reservedIps (予約済み IP アドレス (クラシック))
- Microsoft.ClassicNetwork/virtualNetworks (仮想ネットワーク (クラシック))
- Microsoft.ClassicStorage/StorageAccounts (ストレージ アカウント (クラシック))
- microsoft.cloudes/accounts
- microsoft.cloudsearch/indexes
- Microsoft.CloudTest/accounts (CloudTest アカウント)
- Microsoft.CloudTest/hostedpools (1ES ホステッド プール)
- Microsoft.CloudTest/images (CloudTest イメージ)
- Microsoft.CloudTest/pools (CloudTest プール)
- Microsoft.ClusterStor/nodes (ClusterStor)
- microsoft.codesigning/codesigningaccounts
- microsoft.codespaces/plans
- Microsoft.Cognition/syntheticsAccounts (Synthetics アカウント)
- Microsoft.CognitiveServices/accounts (Cognitive Services)
- Microsoft.Compute/availabilitySets (可用性セット)
- Microsoft.Compute/capacityReservationGroups (容量予約グループ)
- microsoft.compute/capacityreservationgroups/capacityreservations
- microsoft.compute/capacityreservations
- Microsoft.Compute/cloudServices (クラウド サービス (延長サポート))
- Microsoft.Compute/diskAccesses (ディスク アクセス)
- Microsoft.Compute/diskEncryptionSets (ディスク暗号化セット)
- Microsoft.Compute/disks (ディスク)
- Microsoft.Compute/galleries (共有イメージ ギャラリー)
- Microsoft.Compute/galleries/applications (VM アプリケーション)
- Microsoft.Compute/galleries/applications/versions (VM アプリケーション バージョン)
- Microsoft.Compute/galleries/images (イメージ定義)
- Microsoft.Compute/galleries/images/versions (イメージ バージョン)
- Microsoft.Compute/hostgroups (ホスト グループ)
- Microsoft.Compute/hostgroups/hosts (ホスト)
- Microsoft.Compute/images (イメージ)
- Microsoft.Compute/ProximityPlacementGroups (近接配置グループ)
- Microsoft.Compute/restorePointCollections (復元ポイント コレクション)
- microsoft.compute/sharedvmextensions
- microsoft.compute/sharedvmextensions/versions
- microsoft.compute/sharedvmimages
- microsoft.compute/sharedvmimages/versions
- Microsoft.Compute/snapshots (スナップショット)
- Microsoft.Compute/sshPublicKeys (SSH キー)
- microsoft.compute/swiftlets
- Microsoft.Compute/VirtualMachines (仮想マシン)
  - サンプル クエリ: [電源状態別の仮想マシンの数](../samples/samples-by-category.md#count-of-virtual-machines-by-power-state)
  - サンプル クエリ: [OS の種類別の仮想マシンの数](../samples/samples-by-category.md#count-virtual-machines-by-os-type)
  - サンプル クエリ: [extend を使用して仮想マシン数を OS の種類別にカウントする](../samples/samples-by-category.md#count-virtual-machines-by-os-type-with-extend)
  - サンプル クエリ: [仮想マシンにインストールされているすべての拡張機能を一覧表示する](../samples/samples-by-category.md#list-all-extensions-installed-on-a-virtual-machine)
  - サンプル クエリ: [実行されていないマシンと最後のコンプライアンス状態を一覧表示する](../samples/samples-by-category.md#list-machines-that-are-not-running-and-the-last-compliance-status)
  - サンプル クエリ: [可用性状態と電源状態別の仮想マシンの一覧と、そのリソース ID およびリソース グループ](../samples/samples-by-category.md#list-of-virtual-machines-by-availability-state-and-power-state-with-resource-ids-and-resource-groups)
  - サンプル クエリ: [仮想マシンとそのネットワーク インターフェイスおよびパブリック IP を一覧表示する](../samples/samples-by-category.md#list-virtual-machines-with-their-network-interface-and-public-ip)
  - サンプル クエリ: [降順の名前で順序付けられたすべての仮想マシンを表示する](../samples/samples-by-category.md#show-all-virtual-machines-ordered-by-name-in-descending-order)
  - サンプル クエリ: [名前とその OS の種類順に最初の 5 つの仮想マシンを表示する](../samples/samples-by-category.md#show-first-five-virtual-machines-by-name-and-their-os-type)
  - サンプル クエリ: [電源状態の拡張プロパティ別に仮想マシンを集計する](../samples/samples-by-category.md#summarize-virtual-machine-by-the-power-states-extended-property)
  - サンプル クエリ: [正規表現に一致する仮想マシン](../samples/samples-by-category.md#virtual-machines-matched-by-regex)
- microsoft.compute/virtualmachines/extensions
  - サンプル クエリ: [仮想マシンにインストールされているすべての拡張機能を一覧表示する](../samples/samples-by-category.md#list-all-extensions-installed-on-a-virtual-machine)
- microsoft.compute/virtualmachines/runcommands
- Microsoft.Compute/virtualMachineScaleSets (仮想マシン スケール セット)
  - サンプル クエリ: [仮想マシン スケール セットの容量とサイズを取得する](../samples/samples-by-category.md#get-virtual-machine-scale-set-capacity-and-size)
- Microsoft.ConfidentialLedger/ledgers (Confidential Ledgers)
- Microsoft.Confluent/organizations (Confluent 組織)
- Microsoft.ConnectedCache/cacheNodes (接続キャッシュ リソース)
- Microsoft.ConnectedVehicle/platformAccounts (接続された車両プラットフォーム)
- microsoft.connectedvmwarevsphere/clusters
- microsoft.connectedvmwarevsphere/datastores
- microsoft.connectedvmwarevsphere/hosts
- microsoft.connectedvmwarevsphere/resourcepools
- Microsoft.connectedVMwareVSphere/vCenters (VMware vCenters)
- Microsoft.ConnectedVMwarevSphere/VirtualMachines (VMware + AVS 仮想マシン)
- microsoft.connectedvmwarevsphere/virtualmachines/extensions
- microsoft.connectedvmwarevsphere/virtualmachinetemplates
- microsoft.connectedvmwarevsphere/virtualnetworks
- Microsoft.ContainerInstance/containerGroups (コンテナー インスタンス)
- Microsoft.ContainerRegistry/registries (コンテナー レジストリ)
- microsoft.containerregistry/registries/agentpools
- microsoft.containerregistry/registries/buildtasks
- Microsoft.ContainerRegistry/registries/replications (コンテナー レジストリ レプリケーション)
- microsoft.containerregistry/registries/taskruns
- microsoft.containerregistry/registries/tasks
- Microsoft.ContainerRegistry/registries/webhooks (コンテナー レジストリ Webhook)
- microsoft.containerservice/containerservices
- Microsoft.ContainerService/managedClusters (Kubernetes サービス)
  - サンプル クエリ: [Azure サブスクリプションの譲渡時に影響を受けるリソースの一覧](../samples/samples-by-category.md#list-impacted-resources-when-transferring-an-azure-subscription)
- microsoft.containerservice/openshiftmanagedclusters
- microsoft.contoso/clusters
- microsoft.contoso/employees
- microsoft.contoso/installations
- microsoft.contoso/towers
- microsoft.costmanagement/connectors
- microsoft.customproviders/resourceproviders
- microsoft.d365customerinsights/instances
- Microsoft.Dashboard/grafana (Grafana ワークスペース)
- Microsoft.DataBox/jobs (Azure Data Box)
- Microsoft.DataBoxEdge/dataBoxEdgeDevices (Azure Stack Edge / Data Box Gateway)
- Microsoft.Databricks/workspaces (Azure Databricks サービス)
- Microsoft.DataCatalog/catalogs (Data Catalog)
- microsoft.datacatalog/datacatalogs
- Microsoft.DataCollaboration/workspaces (Project CI)
- Microsoft.Datadog/monitors (Datadog)
- Microsoft.DataFactory/dataFactories (データ ファクトリ)
- Microsoft.DataFactory/factories (データ ファクトリ (V2))
- Microsoft.DataLakeAnalytics/accounts (Data Lake Analytics)
- Microsoft.DataLakeStore/accounts (Data Lake Storage Gen1)
  - サンプル クエリ: [Azure サブスクリプションの譲渡時に影響を受けるリソースの一覧](../samples/samples-by-category.md#list-impacted-resources-when-transferring-an-azure-subscription)
- microsoft.datamigration/controllers
- Microsoft.DataMigration/services (Azure Database Migration Service)
- Microsoft.DataMigration/services/projects (Azure Database Migration プロジェクト)
- microsoft.datamigration/slots
- microsoft.datamigration/sqlmigrationservices
- Microsoft.DataProtection/BackupVaults (バックアップ コンテナー)
- Microsoft.DataProtection/resourceGuards (リソース ガード (プレビュー))
- microsoft.dataprotection/resourceoperationgatekeepers
- microsoft.datareplication/replicationfabrics
- Microsoft.DataReplication/replicationVaults (Site Recovery コンテナー)
- Microsoft.DataShare/accounts (データ共有)
- Microsoft.DBforMariaDB/servers (Azure Database for MariaDB サーバー)
- Microsoft.DBforMySQL/flexibleServers (Azure Database for MySQL フレキシブル サーバー)
- Microsoft.DBforMySQL/servers (Azure Database for MySQL サーバー)
- Microsoft.DBforPostgreSQL/flexibleServers (Azure Database for PostgreSQL フレキシブル サーバー)
- Microsoft.DBforPostgreSQL/serverGroups (Azure Database for PostgreSQL サーバー グループ)
- Microsoft.DBforPostgreSQL/serverGroupsv2 (Azure Database for PostgreSQL サーバー グループ)
- Microsoft.DBforPostgreSQL/servers (Azure Database for PostgreSQL サーバー)
- Microsoft.DBforPostgreSQL/serversv2 (Azure Database for PostgreSQL サーバー v2)
- microsoft.dbforpostgresql/singleservers
- microsoft.delegatednetwork/controller
- microsoft.delegatednetwork/delegatedsubnets
- microsoft.delegatednetwork/orchestratorinstances
- microsoft.delegatednetwork/orchestrators
- microsoft.deploymentmanager/artifactsources
- Microsoft.DeploymentManager/Rollouts (ロールアウト)
- microsoft.deploymentmanager/servicetopologies
- microsoft.deploymentmanager/servicetopologies/services
- microsoft.deploymentmanager/servicetopologies/services/serviceunits
- microsoft.deploymentmanager/steps
- Microsoft.DesktopVirtualization/ApplicationGroups (アプリケーション グループ)
- Microsoft.DesktopVirtualization/HostPools (ホスト プール)
- Microsoft.DesktopVirtualization/ScalingPlans (スケーリング プラン)
- Microsoft.DesktopVirtualization/Workspaces (ワークスペース)
- microsoft.devices/elasticpools
- microsoft.devices/elasticpools/iothubtenants
- Microsoft.Devices/IotHubs (IoT Hub)
- Microsoft.Devices/ProvisioningServices (デバイス プロビジョニング サービス)
- Microsoft.DeviceUpdate/Accounts (IoT ハブ用のデバイス更新)
- microsoft.deviceupdate/accounts/instances
- microsoft.devops/pipelines (DevOps Starter)
- microsoft.devspaces/controllers
- microsoft.devtestlab/labcenters
- Microsoft.DevTestLab/labs (DevTest Labs)
- microsoft.devtestlab/labs/servicerunners
- Microsoft.DevTestLab/labs/virtualMachines (仮想マシン)
- microsoft.devtestlab/schedules
- Microsoft.DigitalTwins/digitalTwinsInstances (Azure Digital Twins)
- Microsoft.DocumentDB/cassandraClusters (Azure Managed Instance for Apache Cassandra)
- Microsoft.DocumentDb/databaseAccounts (Azure Cosmos DB アカウント)
  - サンプル クエリ: [特定の書き込み場所を含む Azure Cosmos DB を一覧表示する](../samples/samples-by-category.md#list-azure-cosmos-db-with-specific-write-locations)
- Microsoft.DomainRegistration/domains (App Service ドメイン)
- microsoft.dynamics365fraudprotection/instances
- Microsoft.EdgeOrder/addresses (Azure Edge Hardware Center アドレス)
- microsoft.edgeorder/ordercollections
- Microsoft.EdgeOrder/orderItems (Azure Edge Hardware Center)
- microsoft.edgeorder/orders
- Microsoft.Elastic/monitors (Elasticsearch (エラスティック クラウド))
- microsoft.enterpriseknowledgegraph/services
- Microsoft.EventGrid/domains (Event Grid ドメイン)
- Microsoft.EventGrid/partnerNamespaces (Event Grid パートナー名前空間)
- Microsoft.EventGrid/partnerRegistrations (Event Grid パートナー登録)
- Microsoft.EventGrid/partnerTopics (Event Grid パートナー トピック)
- Microsoft.EventGrid/systemTopics (Event Grid システム トピック)
- Microsoft.EventGrid/topics (Event Grid トピック)
- Microsoft.EventHub/clusters (Event Hubs クラスター)
- Microsoft.EventHub/namespaces (Event Hubs 名前空間)
- Microsoft.Experimentation/experimentWorkspaces (実験ワークスペース)
- Microsoft.ExtendedLocation/CustomLocations (カスタムの場所)
  - サンプル クエリ: [VMware または SCVMM が有効になっている Azure Arc 対応のカスタムの場所を一覧表示する](../samples/samples-by-category.md#list-azure-arc-enabled-custom-locations-with-vmware-or-scvmm-enabled)
- microsoft.falcon/namespaces
- Microsoft.Fidalgo/devcenters (Fidalgo DevCenters)
- microsoft.fidalgo/machinedefinitions
- microsoft.fidalgo/networksettings
- Microsoft.Fidalgo/projects (Fidalgo プロジェクト)
- Microsoft.Fidalgo/projects/environments (Fidalgo 環境)
- Microsoft.FluidRelay/fluidRelayServers (Fluid Relay)
- microsoft.footprintmonitoring/profiles
- microsoft.gaming/titles
- Microsoft.Genomics/accounts (Genomics アカウント)
- microsoft.guestconfiguration/automanagedaccounts
- Microsoft.HanaOnAzure/hanaInstances (SAP HANA on Azure)
- Microsoft.HanaOnAzure/sapMonitors (SAP ソリューション向け Azure Monitor)
- microsoft.hardwaresecuritymodules/dedicatedhsms
- Microsoft.HDInsight/clusterpools (HDInsight クラスター プール)
- Microsoft.HDInsight/clusterpools/clusters (HDInsight gen2 クラスター)
- microsoft.hdinsight/clusterpools/clusters/sessionclusters
- Microsoft.HDInsight/clusters (HDInsight クラスター)
- Microsoft.HealthBot/healthBots (Azure Health Bot)
- Microsoft.HealthcareApis/services (Azure API for FHIR)
- microsoft.healthcareapis/services/privateendpointconnections
- Microsoft.HealthcareApis/workspaces (Healthcare API ワークスペース)
- Microsoft.HealthcareApis/workspaces/dicomservices (DICOM サービス)
- Microsoft.HealthcareApis/workspaces/fhirservices (FHIR サービス)
- Microsoft.HealthcareApis/workspaces/iotconnectors (IoT コネクタ)
- Microsoft.HpcWorkbench/instances (HPC Workbenches (プレビュー))
- Microsoft.HybridCompute/machines (サーバー - Azure Arc)
  - サンプルクエリ: [ドメイン別の Arc 対応サーバーの数と割合を取得する](../samples/samples-by-category.md#get-count-and-percentage-of-arc-enabled-servers-by-domain)
  - サンプルクエリ: [Azure Arc 対応サーバーにインストールされているすべての拡張機能を一覧表示する](../samples/samples-by-category.md#list-all-extensions-installed-on-an-azure-arc-enabled-server)
  - サンプル クエリ: [最新リリース バージョンのエージェントを実行していない Arc 対応サーバーを一覧表示する](../samples/samples-by-category.md#list-arc-enabled-servers-not-running-latest-released-agent-version)
- microsoft.hybridcompute/machines/extensions
  - サンプルクエリ: [Azure Arc 対応サーバーにインストールされているすべての拡張機能を一覧表示する](../samples/samples-by-category.md#list-all-extensions-installed-on-an-azure-arc-enabled-server)
- Microsoft.HybridCompute/privateLinkScopes (Azure Arc プライベート リンク スコープ)
- microsoft.hybridcontainerservice/provisionedclusters
- Microsoft.HybridData/dataManagers (StorSimple Data Managers)
- Microsoft.HybridNetwork/devices (Azure ネットワーク機能マネージャー – デバイス (プレビュー))
- Microsoft.HybridNetwork/networkFunctions (Azure ネットワーク機能マネージャー – ネットワーク機能 (プレビュー))
- microsoft.hybridnetwork/virtualnetworkfunctions
- Microsoft.ImportExport/jobs (インポート/エクスポート ジョブ)
- microsoft.industrydatalifecycle/basemodels
- microsoft.industrydatalifecycle/custodiancollaboratives
- microsoft.industrydatalifecycle/dataconsumercollaboratives
- microsoft.industrydatalifecycle/derivedmodels
- microsoft.industrydatalifecycle/membercollaboratives
- microsoft.industrydatalifecycle/modelmappings
- microsoft.industrydatalifecycle/pipelinesets
- microsoft.insights/actiongroups
- microsoft.insights/activitylogalerts
- microsoft.insights/alertrules
- microsoft.insights/autoscalesettings
- microsoft.insights/components (Application Insights)
- microsoft.insights/datacollectionendpoints (データ収集エンドポイント)
- microsoft.insights/datacollectionrules (データ収集ルール)
- microsoft.insights/guestdiagnosticsettings
- microsoft.insights/metricalerts
- microsoft.insights/notificationgroups
- microsoft.insights/notificationrules
- Microsoft.Insights/privateLinkScopes (Azure Monitor プライベート リンク スコープ)
- microsoft.insights/querypacks
- microsoft.insights/scheduledqueryrules
- microsoft.insights/webtests (可用性テスト)
- microsoft.insights/workbooks (Azure ブック)
- microsoft.insights/workbooktemplates (Azure ブック テンプレート)
- Microsoft.IntelligentITDigitalTwin/digitalTwins (Minervas)
- Microsoft.IntelligentITDigitalTwin/digitalTwins/assets (アセット)
- Microsoft.IntelligentITDigitalTwin/digitalTwins/executionPlans (デプロイ)
- Microsoft.IntelligentITDigitalTwin/digitalTwins/testPlans (スイート)
- Microsoft.IntelligentITDigitalTwin/digitalTwins/tests (スクリプト)
- Microsoft.IoTCentral/IoTApps (IoT Central アプリケーション)
- microsoft.iotspaces/graph
- microsoft.keyvault/hsmpools
- microsoft.keyvault/managedhsms
- Microsoft.KeyVault/vaults (キー コンテナー)
  - サンプル クエリ: [Key Vault リソース数をカウントする](../samples/samples-by-category.md#count-key-vault-resources)
  - サンプル クエリ: [サブスクリプション名を含むキー コンテナー](../samples/samples-by-category.md#key-vaults-with-subscription-name)
  - サンプル クエリ: [Azure サブスクリプションの譲渡時に影響を受けるリソースの一覧](../samples/samples-by-category.md#list-impacted-resources-when-transferring-an-azure-subscription)
- Microsoft.Kubernetes/connectedClusters (Kubernetes - Azure Arc)
  - サンプル クエリ: [Azure Monitor 拡張機能がないすべての Azure Arc 対応 Kubernetes クラスターを一覧表示する](../samples/samples-by-category.md#list-all-azure-arc-enabled-kubernetes-clusters-without-azure-monitor-extension)
  - サンプル クエリ: [Azure Arc 対応 Kubernetes リソースをすべて一覧表示する](../samples/samples-by-category.md#list-all-azure-arc-enabled-kubernetes-resources)
- Microsoft.Kusto/clusters (Azure Data Explorer クラスター)
- Microsoft.Kusto/clusters/databases (Azure Data Explorer データベース)
- Microsoft.LabServices/labAccounts (ラボ サービス)
- microsoft.labservices/labplans
- microsoft.labservices/labs
- Microsoft.LoadTestService/LoadTests (クラウド ネイティブ ロード テスト)
- Microsoft.Logic/integrationAccounts (統合アカウント)
- Microsoft.Logic/integrationServiceEnvironments (統合サービス環境)
- Microsoft.Logic/integrationServiceEnvironments/managedApis (マネージド コネクタ)
- Microsoft.Logic/workflows (ロジック アプリ)
- Microsoft.Logz/monitors (Logz メイン アカウント)
- Microsoft.Logz/monitors/accounts (Logz サブ アカウント)
- Microsoft.MachineLearning/commitmentPlans (Machine Learning スタジオ (クラシック) Web サービス プラン)
- Microsoft.MachineLearning/webServices (Machine Learning スタジオ (クラシック) Web サービス)
- Microsoft.MachineLearning/workspaces (Machine Learning スタジオ (クラシック) ワークスペース)
- microsoft.machinelearningcompute/operationalizationclusters
- microsoft.machinelearningexperimentation/accounts/workspaces
- microsoft.machinelearningservices/aisysteminventories
- microsoft.machinelearningservices/modelinventories
- microsoft.machinelearningservices/modelinventory
- microsoft.machinelearningservices/virtualclusters
- Microsoft.MachineLearningServices/workspaces (機械学習)
- microsoft.machinelearningservices/workspaces/batchendpoints
- microsoft.machinelearningservices/workspaces/batchendpoints/deployments
- microsoft.machinelearningservices/workspaces/inferenceendpoints
- microsoft.machinelearningservices/workspaces/inferenceendpoints/deployments
- Microsoft.MachineLearningServices/workspaces/onlineEndpoints (機械学習オンライン エンドポイント)
- Microsoft.MachineLearningServices/workspaces/onlineEndpoints/deployments (機械学習オンライン デプロイ)
- Microsoft.Maintenance/maintenanceConfigurations (メンテナンス構成)
- microsoft.maintenance/maintenancepolicies
- microsoft.managedidentity/groups
- Microsoft.ManagedIdentity/userAssignedIdentities (マネージド ID)
  - サンプル クエリ: [Azure サブスクリプションの譲渡時に影響を受けるリソースの一覧](../samples/samples-by-category.md#list-impacted-resources-when-transferring-an-azure-subscription)
- microsoft.managednetwork/managednetworkgroups
- microsoft.managednetwork/managednetworkpeeringpolicies
- microsoft.managednetwork/managednetworks
- microsoft.managednetwork/managednetworks/managednetworkgroups
- microsoft.managednetwork/managednetworks/managednetworkpeeringpolicies
- Microsoft.Maps/accounts (Azure Maps アカウント)
- Microsoft.Maps/accounts/creators (Azure Maps Creator リソース)
- microsoft.maps/accounts/privateatlases
- Microsoft.MarketplaceApps/classicDevServices (従来の開発サービス)
- microsoft.media/mediaservices (Media Services)
- microsoft.media/mediaservices/liveevents (ライブ イベント)
- microsoft.media/mediaservices/streamingendpoints (ストリーミング エンドポイント)
- microsoft.media/mediaservices/transforms
- microsoft media/videoanalyzers (Video Analyzer)
- microsoft.microservices4spring/appclusters
- microsoft.migrate/assessmentprojects
- microsoft.migrate/migrateprojects
- microsoft.migrate/movecollections
- Microsoft.Migrate/projects (移行プロジェクト)
- Microsoft.MixedReality/holographicsBroadcastAccounts (Holographics Broadcast アカウント)
- Microsoft.MixedReality/objectAnchorsAccounts (Object Anchors アカウント)
- Microsoft.MixedReality/objectUnderstandingAccounts (Object Understanding アカウント)
- Microsoft.MixedReality/remoteRenderingAccounts (Remote Rendering アカウント)
- Microsoft.MixedReality/spatialAnchorsAccounts (Spatial Anchors アカウント)
- microsoft.mixedreality/surfacereconstructionaccounts
- Microsoft.MobileNetwork/mobileNetworks (モバイル ネットワーク)
- microsoft.mobilenetwork/mobilenetworks/datanetworks
- microsoft.mobilenetwork/mobilenetworks/services
- microsoft.mobilenetwork/mobilenetworks/simpolicies
- Microsoft.MobileNetwork/mobileNetworks/sites (モバイル ネットワーク サイト)
- microsoft.mobilenetwork/mobilenetworks/slices
- microsoft.mobilenetwork/networks
- microsoft.mobilenetwork/networks/sites
- Microsoft.MobileNetwork/packetCoreControlPlanes (ネットワーク関数用 Arc – パケット コア)
- microsoft.mobilenetwork/packetcorecontrolplanes/packetcoredataplanes
- microsoft.mobilenetwork/packetcorecontrolplanes/packetcoredataplanes/attacheddatanetworks
- Microsoft.MobileNetwork/sims (Sims)
- microsoft.mobilenetwork/sims/simprofiles
- microsoft.monitor/accounts
- Microsoft.NetApp/netAppAccounts (NetApp アカウント)
- microsoft.netapp/netappaccounts/backuppolicies
- Microsoft.NetApp/netAppAccounts/capacityPools (容量プール)
- Microsoft.NetApp/netAppAccounts/capacityPools/Volumes (ボリューム)
- microsoft.netapp/netappaccounts/capacitypools/volumes/mounttargets
- Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots (スナップショット)
- microsoft.netapp/netappaccounts/capacitypools/volumes/subvolumes
- Microsoft.NetApp/netAppAccounts/snapshotPolicies (スナップショット ポリシー)
- Microsoft.Network/applicationGateways (アプリケーション ゲートウェイ)
- Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies (Web アプリケーション ファイアウォール ポリシー (WAF))
- Microsoft.Network/applicationSecurityGroups (アプリケーションのセキュリティ グループ)
- Microsoft.Network/azureFirewalls (ファイアウォール)
- Microsoft.Network/bastionHosts (Bastions)
- Microsoft.Network/connections (接続)
- Microsoft.Network/customIpPrefixes (カスタム IP プレフィックス)
- microsoft.network/ddoscustompolicies
- Microsoft.Network/ddosProtectionPlans (DDoS 保護プラン)
- microsoft.network/dnsforwardingrulesets
- microsoft.network/dnsresolvers
- Microsoft.Network/dnsZones (DNS ゾーン)
- microsoft.network/dscpconfigurations
- Microsoft.Network/expressRouteCircuits (ExpressRoute 回線)
- microsoft.network/expressroutecrossconnections
- microsoft.network/expressroutegateways
- Microsoft.Network/expressRoutePorts (ExpressRoute Direct)
- Microsoft.Network/firewallPolicies (ファイアウォール ポリシー)
- Microsoft.Network/frontdoors (フロント ドア)
- Microsoft.Network/FrontDoorWebApplicationFirewallPolicies (Web アプリケーション ファイアウォール ポリシー (WAF))
- microsoft.network/ipallocations
- Microsoft.Network/ipGroups (IP グループ)
- Microsoft.Network/LoadBalancers (ロード バランサー)
- Microsoft.Network/localnetworkgateways (ローカル ネットワーク ゲートウェイ)
- microsoft.network/mastercustomipprefixes
- Microsoft.Network/natGateways (NAT ゲートウェイ)
- Microsoft.Network/NetworkExperimentProfiles (Internet Analyzer プロファイル)
- microsoft.network/networkintentpolicies
- Microsoft.Network/networkinterfaces (ネットワーク インターフェイス)
  - サンプル クエリ: [ネットワーク インターフェイスの仮想ネットワークとサブネットを取得する](../samples/samples-by-category.md#get-virtual-networks-and-subnets-of-network-interfaces)
  - サンプル クエリ: [仮想マシンとそのネットワーク インターフェイスおよびパブリック IP を一覧表示する](../samples/samples-by-category.md#list-virtual-machines-with-their-network-interface-and-public-ip)
- Microsoft.Network/networkManagers (ネットワーク マネージャー)
- microsoft.network/networkprofiles
- Microsoft.Network/NetworkSecurityGroups (ネットワーク セキュリティ グループ)
  - サンプル クエリ: [関連付けられていないネットワーク セキュリティ グループを表示する](../samples/samples-by-category.md#show-unassociated-network-security-groups)
- microsoft.network/networksecurityperimeters
- microsoft.network/networkvirtualappliances
- microsoft.network/networkwatchers (ネットワーク ウォッチャー)
- microsoft.network/networkwatchers/connectionmonitors
- microsoft.network/networkwatchers/flowlogs (NSG フロー ログ)
- microsoft.network/networkwatchers/lenses
- microsoft.network/networkwatchers/pingmeshes
- microsoft.network/p2svpngateways
- Microsoft.Network/privateDnsZones (プライベート DNS ゾーン)
- microsoft.network/privatednszones/virtualnetworklinks
- microsoft.network/privateendpointredirectmaps
- Microsoft.Network/privateEndpoints (プライベート エンドポイント)
- Microsoft.Network/privateLinkServices (プライベート リンク サービス)
- Microsoft.Network/PublicIpAddresses (パブリック IP アドレス)
  - サンプル クエリ: [仮想マシンとそのネットワーク インターフェイスおよびパブリック IP を一覧表示する](../samples/samples-by-category.md#list-virtual-machines-with-their-network-interface-and-public-ip)
- Microsoft.Network/publicIpPrefixes (パブリック IP プレフィックス)
- Microsoft.Network/routeFilters (ルート フィルター)
- Microsoft.Network/routeTables (ルート テーブル)
- microsoft.network/sampleresources
- microsoft.network/securitypartnerproviders
- Microsoft.Network/serviceEndpointPolicies (サービス エンドポイント ポリシー)
- Microsoft.Network/trafficmanagerprofiles (Traffic Manager プロファイル)
- microsoft.network/virtualhubs
- microsoft.network/virtualhubs/bgpconnections
- microsoft.network/virtualhubs/ipconfigurations
- Microsoft.Network/virtualNetworkGateways (仮想ネットワーク ゲートウェイ)
- Microsoft.Network/virtualNetworks (仮想ネットワーク)
- microsoft.network/virtualnetworktaps
- microsoft.network/virtualrouters
- Microsoft.Network/virtualWans (仮想 WAN)
- microsoft.network/vpngateways
- microsoft.network/vpnserverconfigurations
- microsoft.network/vpnsites
- microsoft.networkfunction/azuretrafficcollectors
- Microsoft.NotificationHubs/namespaces (通知ハブの名前空間)
- Microsoft.NotificationHubs/namespaces/notificationHubs (通知ハブ)
- microsoft.nutanix/interfaces
- microsoft.nutanix/nodes
- microsoft.objectstore/osnamespaces
- microsoft.offazure/hypervsites
- microsoft.offazure/importsites
- microsoft.offazure/mastersites
- microsoft.offazure/serversites
- microsoft.offazure/vmwaresites
- microsoft.openlogisticsplatform/applicationworkspaces
- Microsoft.OpenLogisticsPlatform/workspaces (オープン サプライ チェーン プラットフォーム)
- microsoft.operationalinsights/clusters
- Microsoft.OperationalInsights/querypacks (Log Analytics クエリ パック)
- Microsoft.OperationalInsights/workspaces (Log Analytics ワークスペース)
- Microsoft.OperationsManagement/solutions (ソリューション)
- microsoft.operationsmanagement/views
- Microsoft.Orbital/contactProfiles (ContactProfiles)
- Microsoft.Orbital/GroundStations (GroundStations)
- microsoft.orbital/orbitalendpoints
- microsoft.orbital/orbitalgateways
- microsoft.orbital/orbitalgateways/orbitall2connections
- microsoft.orbital/orbitalgateways/orbitall3connections
- Microsoft.Orbital/spacecrafts (スペ-スクラフト)
- Microsoft.Peering/peerings (ピアリング)
- Microsoft.Peering/peeringServices (ピアリング サービス)
- Microsoft.Portal/dashboards (共有ダッシュボード)
- microsoft.portalsdk/rootresources
- microsoft.powerbi/privatelinkservicesforpowerbi
- microsoft.powerbi/tenants
- microsoft.powerbi/workspacecollections
- microsoft.powerbidedicated/autoscalevcores
- Microsoft.PowerBIDedicated/capacities (Power BI Embedded)
- microsoft.powerplatform/accounts
- microsoft.powerplatform/enterprisepolicies
- microsoft.projectbabylon/accounts
- microsoft.providerhubdevtest/regionalstresstests
- Microsoft.Purview/Accounts (Purview アカウント)
- Microsoft.Quantum/Workspaces (Quantum ワークスペース)
- Microsoft.RecommendationsService/accounts (Intelligent Recommendations アカウント)
- Microsoft.RecommendationsService/accounts/modeling (モデリング)
- Microsoft.RecommendationsService/accounts/serviceEndpoints (サービス エンドポイント)
- Microsoft.RecoveryServices/vaults (Recovery Services コンテナー)
- Microsoft.RedHatOpenShift/openShiftClusters (OpenShift クラスター)
- Microsoft.Relay/namespaces (リレー)
- microsoft.remoteapp/collections
- microsoft.resiliency/chaosexperiments
- Microsoft.ResourceConnector/Appliances (リソース ブリッジ)
- Microsoft.resourcegraph/queries (Resource Graph クエリ)
- Microsoft.Resources/deploymentScripts (デプロイ スクリプト)
- Microsoft.Resources/templateSpecs (テンプレート スペック)
- microsoft.resources/templatespecs/versions
- Microsoft.SaaS/applications (サービスとしてのソフトウェア (クラシック))
- Microsoft.SaaS/resources (SaaS)
- Microsoft.Scheduler/jobCollections (スケジューラ ジョブ コレクション)
- Microsoft.Scom/managedInstances (Aquila インスタンス)
- microsoft.scvmm/clouds
- Microsoft.scvmm/virtualMachines (SCVMM 仮想マシン - Azure Arc)
- microsoft.scvmm/virtualmachinetemplates
- microsoft.scvmm/virtualnetworks
- microsoft.scvmm/vmmservers
- Microsoft.Search/searchServices (Search Service)
- microsoft.security/assignments
- microsoft.security/automations
- microsoft.security/customentitystoreassignments
- microsoft.security/iotsecuritysolutions
- microsoft.security/securityconnectors
- Microsoft.SecurityDetonation/chambers (セキュリティ デトネーション チャンバー)
- Microsoft.ServiceBus/namespaces (Service Bus 名前空間)
- Microsoft.ServiceFabric/clusters (Service Fabric クラスター)
- microsoft.servicefabric/containergroupsets
- Microsoft.ServiceFabric/managedclusters (Service Fabric 管理対象クラスター)
- microsoft.servicefabricmesh/applications
- microsoft.servicefabricmesh/gateways
- microsoft.servicefabricmesh/networks
- microsoft.servicefabricmesh/secrets
- microsoft.servicefabricmesh/volumes
- Microsoft.ServicesHub/connectors (サービス ハブ コネクタ)
- Microsoft.SignalRService/SignalR (SignalR)
- Microsoft.SignalRService/WebPubSub (Web PubSub サービス)
- microsoft.singularity/accounts
- microsoft.skytap/nodes
- microsoft.solutions/appliancedefinitions
- microsoft.solutions/appliances
- Microsoft.Solutions/applicationDefinitions (サービス カタログのマネージド アプリケーションの定義)
- Microsoft.Solutions/applications (マネージド アプリケーション)
- microsoft.solutions/jitrequests
- microsoft.spoolservice/spools
- Microsoft.Sql/instancePools (インスタンス プール)
- Microsoft.Sql/managedInstances (SQL マネージド インスタンス)
- Microsoft.Sql/managedInstances/databases (マネージド データベース)
- Microsoft.Sql/servers (SQL サーバー)
- Microsoft.Sql/servers/databases (SQL データベース)
  - サンプル クエリ: [Azure サブスクリプションの譲渡時に影響を受けるリソースの一覧](../samples/samples-by-category.md#list-impacted-resources-when-transferring-an-azure-subscription)
  - サンプル クエリ: [SQL データベースとそのエラスティック プールを一覧表示する](../samples/samples-by-category.md#list-sql-databases-and-their-elastic-pools)
- Microsoft.Sql/servers/elasticpools (SQL エラスティック プール)
  - サンプル クエリ: [SQL データベースとそのエラスティック プールを一覧表示する](../samples/samples-by-category.md#list-sql-databases-and-their-elastic-pools)
- microsoft.sql/servers/jobaccounts
- Microsoft.Sql/servers/jobAgents (エラスティック ジョブ エージェント)
- Microsoft.Sql/virtualClusters (仮想クラスター)
- microsoft.sqlvirtualmachine/sqlvirtualmachinegroups
- Microsoft.SqlVirtualMachine/SqlVirtualMachines (SQL 仮想マシン)
- microsoft.sqlvm/dwvm
- Microsoft.Storage/StorageAccounts (ストレージ アカウント)
  - サンプル クエリ: [リソース グループ上の特定の大文字と小文字が区別されないタグを含んだストレージ アカウントを検索する](../samples/samples-by-category.md#find-storage-accounts-with-a-specific-case-insensitive-tag-on-the-resource-group)
  - サンプル クエリ: [リソース グループ上の特定の大文字と小文字が区別されたタグを含んだストレージ アカウントを検索する](../samples/samples-by-category.md#find-storage-accounts-with-a-specific-case-sensitive-tag-on-the-resource-group)
  - サンプル クエリ: [特定のタグ値を持つすべてのストレージ アカウントを一覧表示する](../samples/samples-by-category.md#list-all-storage-accounts-with-specific-tag-value)
  - サンプル クエリ: [Azure サブスクリプションの譲渡時に影響を受けるリソースの一覧](../samples/samples-by-category.md#list-impacted-resources-when-transferring-an-azure-subscription)
- microsoft.storagecache/amlfilesystems
- Microsoft.StorageCache/caches (HPC キャッシュ)
- Microsoft.StoragePool/diskPools (Disk プール)
- Microsoft.StorageSync/storageSyncServices (ストレージ同期サービス)
- Microsoft.StorageSyncDev/storageSyncServices (ストレージ同期サービス)
- Microsoft.StorageSyncInt/storageSyncServices (ストレージ同期サービス)
- Microsoft.StorSimple/Managers (StorSimple デバイス マネージャー)
- Microsoft.StreamAnalytics/clusters (Stream Analytics クラスター)
- Microsoft.StreamAnalytics/StreamingJobs (Stream Analytics ジョブ)
- microsoft.swiftlet/virtualmachines
- microsoft.swiftlet/virtualmachinesnapshots
- Microsoft.Synapse/privateLinkHubs (Azure Synapse Analytics (プライベート リンク ハブ))
- Microsoft.Synapse/workspaces (Azure Synapse Analytics)
- Microsoft.Synapse/workspaces/bigDataPools (Apache Spark プール)
- microsoft.synapse/workspaces/eventstreams
- Microsoft.Synapse/workspaces/kustopools (データ エクスプローラー プール (プレビュー))
- microsoft.synapse/workspaces/sqldatabases
- Microsoft.Synapse/workspaces/sqlPools (専用 SQL プール)
- microsoft.terraformoss/providerregistrations
- Microsoft.TestBase/testBaseAccounts (テスト ベース アカウント)
- microsoft.testbase/testbaseaccounts/packages
- microsoft.testbase/testbases
- Microsoft.TimeSeriesInsights/environments (Time Series Insights 環境)
- Microsoft.TimeSeriesInsights/environments/eventsources (Time Series Insights イベント ソース)
- Microsoft.TimeSeriesInsights/environments/referenceDataSets (Time Series Insights 参照データ セット)
- microsoft.token/stores
- microsoft.tokenvault/vaults
- Microsoft.VideoIndexer/accounts (Video Analyzer for Media)
- Microsoft.VirtualMachineImages/imageTemplates (イメージ テンプレート)
- microsoft.visualstudio/account (Azure DevOps 組織)
- microsoft.visualstudio/account/extension
- microsoft.visualstudio/account/project (DevOps スターター)
- microsoft.vmware/arczones
- microsoft.vmware/resourcepools
- microsoft.vmware/vcenters
- microsoft.vmware/virtualmachines
- microsoft.vmware/virtualmachinetemplates
- microsoft.vmware/virtualnetworks
- Microsoft.VMwareCloudSimple/dedicatedCloudNodes (CloudSimple ノード)
- Microsoft.VMwareCloudSimple/dedicatedCloudServices (CloudSimple サービス)
- Microsoft.VMwareCloudSimple/virtualMachines (CloudSimple 仮想マシン)
- microsoft.vmwareonazure/privateclouds
- microsoft.vmwarevirtustream/privateclouds
- microsoft.vsonline/accounts
- Microsoft.VSOnline/Plans (Visual Studio Online プラン)
- microsoft.web/apimanagementaccounts
- microsoft.web/apimanagementaccounts/apis
- microsoft.web/certificates
- Microsoft.Web/connectionGateways (オンプレミス データ ゲートウェイ)
- Microsoft.Web/connections (API 接続)
- microsoft.web/containerapps
- Microsoft.Web/customApis (Logic Apps カスタム コネクタ)
- Microsoft.Web/HostingEnvironments (App Service 環境)
- Microsoft.Web/KubeEnvironments (App Service Kubernetes 環境)
- Microsoft.Web/serverFarms (App Service プラン)
- Microsoft.Web/sites (App Services)
- microsoft.web/sites/premieraddons
- Microsoft.Web/sites/slots (App Service (スロット))
- Microsoft.Web/StaticSites (Static Web Apps)
- Microsoft.Web/WorkerApps (コンテナー アプリ)
- Microsoft.WindowsESU/multipleActivationKeys (Windows マルチ ライセンス認証キー)
- Microsoft.WindowsIoT/DeviceServices (Windows 10 IoT Core Services)
- microsoft.workloadbuilder/migrationagents
- microsoft.workloadbuilder/workloads
- myget.packagemanagement/services
- NGINX.NGINXPLUS/nginxDeployments (NGINX デプロイ)
- Paraleap.CloudMonix/services (CloudMonix)
- Pokitdok.Platform/services (PokitDok プラットフォーム)
- private.contoso/employees
- Providers.Test/statefulIbizaEngines (マイ リソース)
- RavenHq.Db/databases (RavenHQ)
- Raygun.CrashReporting/apps (Raygun)
- Sendgrid.Email/accounts (SendGrid アカウント)
- sparkpost.basic/services
- stackify.retrace/services
- test.shoebox/testresources
- test.shoebox/testresources2
- TrendMicro.DeepSecurity/accounts (Deep Security SaaS)
- u2uconsult.theidentityhub/services
- Wandisco.Fusion/fusionGroups (LiveData プレーン)
- Wandisco.Fusion/fusionGroups/azureZones (Azure ゾーン)
- Wandisco.Fusion/fusionGroups/azureZones/plugins (プラグイン)
- Wandisco.Fusion/fusionGroups/hiveReplicationRules (Hive レプリケーション規則)
- Wandisco.Fusion/fusionGroups/managedOnPremZones (オンプレミス ゾーン)
- wandisco.fusion/fusiongroups/onpremzones
- Wandisco.Fusion/fusionGroups/replicationRules (レプリケーション規則)
- Wandisco.Fusion/migrators (LiveData Migrator)
- Wandisco.Fusion/migrators/exclusionTemplates (除外)
- Wandisco.Fusion/migrators/liveDataMigrations (移行)
- Wandisco.Fusion/migrators/metadataMigrations (メタデータ移行)
- Wandisco.Fusion/migrators/metadataTargets (メタデータ ターゲット)
- Wandisco.Fusion/migrators/pathMappings (パス マッピング)
- Wandisco.Fusion/migrators/targets (ターゲット)

## <a name="securityresources"></a>securityresources

このテーブルのサンプル クエリについては、[securityresources の Resource Graph サンプル クエリ](../samples/samples-by-table.md#securityresources)に関するページを参照してください。

- microsoft.security/assessments
  - サンプル クエリ: [推奨事項に基づき正常、異常、および非適用のリソースを数える](../samples/samples-by-category.md#count-healthy-unhealthy-and-not-applicable-resources-per-recommendation)
  - サンプル クエリ: [Azure Security Center 推奨事項を一覧表示する](../samples/samples-by-category.md#list-azure-security-center-recommendations)
  - サンプル クエリ: [Container Registry の脆弱性評価結果を一覧表示する](../samples/samples-by-category.md#list-container-registry-vulnerability-assessment-results)
  - サンプル クエリ: [Qualys の脆弱性評価結果を一覧表示する](../samples/samples-by-category.md#list-qualys-vulnerability-assessment-results)
- microsoft.security/assessments/subassessments
  - サンプル クエリ: [Container Registry の脆弱性評価結果を一覧表示する](../samples/samples-by-category.md#list-container-registry-vulnerability-assessment-results)
  - サンプル クエリ: [Qualys の脆弱性評価結果を一覧表示する](../samples/samples-by-category.md#list-qualys-vulnerability-assessment-results)
- microsoft.security/insights/classification (データの機密性セキュリティ分析情報 (プレビュー))
- microsoft.security/iotalerts
  - サンプル クエリ: [ハブ上のあらゆる IoT アラートを種類別にフィルター処理して取得する](../samples/samples-by-category.md#get-all-iot-alerts-on-hub-filtered-by-type)
  - サンプル クエリ: [特定の IoT アラートを取得する](../samples/samples-by-category.md#get-specific-iot-alert)
- microsoft.security/locations/alerts (セキュリティ アラート)
- microsoft.security/pricings
  - サンプル クエリ: [サブスクリプション別の Azure Defender 価格レベルを表示する](../samples/samples-by-category.md#show-azure-defender-pricing-tier-per-subscription)
- microsoft.security/regulatorycompliancestandards
  - サンプル クエリ: [コンプライアンス標準別の規制コンプライアンス状態](../samples/samples-by-category.md#regulatory-compliance-state-per-compliance-standard)
- microsoft.security/regulatorycompliancestandards/regulatorycompliancecontrols
- microsoft.security/regulatorycompliancestandards/regulatorycompliancecontrols/regulatorycomplianceassessments
  - サンプル クエリ: [規制コンプライアンスの評価状態](../samples/samples-by-category.md#regulatory-compliance-assessments-state)
- microsoft.security/securescores
  - サンプル クエリ: [管理グループ別のセキュリティ スコア](../samples/samples-by-category.md#secure-score-per-management-group)
  - サンプル クエリ: [サブスクリプション別のセキュリティ スコア](../samples/samples-by-category.md#secure-score-per-subscription)
- microsoft.security/securescores/securescorecontrols
  - サンプル クエリ: [サブスクリプション別のセキュリティ スコアを制御する](../samples/samples-by-category.md#controls-secure-score-per-subscription)
- microsoft.security/softwareinventories
- microsoft.security/softwareinventory

## <a name="servicehealthresources"></a>servicehealthresources

このテーブルのサンプル クエリについては、[servicehealthresources の Resource Graph サンプル クエリ](../samples/samples-by-table.md#servicehealthresources)に関するページを参照してください。

- microsoft.resourcehealth/events
  - サンプル クエリ: [アクティブな Service Health イベント サブスクリプションの影響](../samples/samples-by-category.md#active-service-health-event-subscription-impact)
  - サンプル クエリ: [アクティブなあらゆる正常性アドバイザリ イベント](../samples/samples-by-category.md#all-active-health-advisory-events)
  - サンプル クエリ: [アクティブなあらゆる計画メンテナンス イベント](../samples/samples-by-category.md#all-active-planned-maintenance-events)
  - サンプル クエリ: [アクティブなあらゆる Service Health イベント](../samples/samples-by-category.md#all-active-service-health-events)
  - サンプル クエリ: [アクティブなあらゆるサービス問題イベント](../samples/samples-by-category.md#all-active-service-issue-events)

## <a name="workloadmonitorresources"></a>workloadmonitorresources

- microsoft.workloadmonitor/monitors

## <a name="next-steps"></a>次のステップ

- [クエリ言語](../concepts/query-language.md)の詳細について学習します。
- [リソースを探索する](../concepts/explore-resources.md)方法について詳しく確認します。
- [初歩的なクエリ](../samples/starter.md)のサンプルを参照します。
