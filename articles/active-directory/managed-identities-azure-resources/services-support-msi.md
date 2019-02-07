---
title: Azure リソースのマネージド ID をサポートする Azure サービス
description: Azure リソースのマネージド ID と Azure AD 認証をサポートするサービスの一覧
services: active-directory
author: priyamohanram
ms.author: priyamo
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: daveba
ms.openlocfilehash: 8274909b40d872309230f9c9f6c045b4283fd6eb
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/06/2019
ms.locfileid: "55752332"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Azure リソースのマネージド ID をサポートするサービス

Azure リソースのマネージド ID は、Azure Active Directory で自動的に管理される ID を Azure サービスに提供します。 マネージド ID を使用すると、コードに資格情報が含まれていなくても、Azure AD 認証をサポートする任意のサービスに対して認証を行うことができます。 Azure リソースのマネージド ID と Azure AD 認証の Azure 全体への統合が進行中です。 更新プログラムがないかどうか、頻繁に確認してください。

> [!NOTE]
> Azure リソースのマネージド ID は、以前のマネージドサービス ID (MSI) の新しい名前です。

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Azure リソースのマネージド ID をサポートする Azure サービス

次の Azure サービスが、Azure リソースのマネージド ID をサポートしています。

### <a name="azure-virtual-machines"></a>Azure Virtual Machines

|マネージド ID の種類 |  すべて一般公開<br>グローバル Azure リージョン | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| システム割り当て済み | 使用可能 | プレビュー | プレビュー | プレビュー | プレビュー |
| ユーザー割り当て済み | プレビュー | プレビュー | プレビュー | プレビュー | プレビュー

Azure Virtual Machines のために (それが提供されているリージョンで) マネージド ID を 構成するには、次の一覧を参照してください。

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure リソース マネージャーのテンプレート](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-virtual-machine-scale-sets"></a>Azure 仮想マシン スケール セット

|マネージド ID の種類 |  すべて一般公開<br>グローバル Azure リージョン | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| システム割り当て済み | 使用可能 | プレビュー | プレビュー | プレビュー |
| ユーザー割り当て済み | プレビュー | プレビュー | プレビュー | プレビュー

Azure Virtual Machine Scale Sets のために (それが提供されているリージョンで) マネージド ID を 構成するには、次の一覧を参照してください。

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure リソース マネージャーのテンプレート](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-app-service"></a>Azure App Service

|マネージド ID の種類 |  すべて一般公開<br>グローバル Azure リージョン | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| システム割り当て済み | 使用可能 | 使用可能 | 使用可能 | 使用可能 |
| ユーザー割り当て済み | プレビュー | 使用できません。 | 使用できません。 | 使用できません。

Azure App Service のために (それが提供されているリージョンで) マネージド ID を 構成するには、次の一覧を参照してください。

- [Azure Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Azure Resource Manager テンプレート](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-functions"></a>Azure Functions

マネージド ID の種類 |  すべて一般公開<br>グローバル Azure リージョン | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| システム割り当て済み | 使用可能 | 使用可能 | 使用可能 | 使用可能 |
| ユーザー割り当て済み | プレビュー | 使用できません。 | 使用できません。 | 使用できません。

Azure Functions のために (それが提供されているリージョンで) マネージド ID を 構成するには、次の一覧を参照してください。

- [Azure Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Azure Resource Manager テンプレート](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-logic-apps"></a>Azure Logic Apps

マネージド ID の種類 |  すべて一般公開<br>グローバル Azure リージョン | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| システム割り当て済み | 使用可能 | 使用可能 | 使用可能 | 使用可能 |
| ユーザー割り当て済み | 使用できません。 | 使用できません。 | 使用できません。 | 使用できません。

Azure Logic Apps のために (それが提供されているリージョンで) マネージド ID を 構成するには、次の一覧を参照してください。

- [Azure Portal](/azure/logic-apps/create-managed-service-identity#azure-portal)
- [Azure Resource Manager テンプレート](/azure/app-service/overview-managed-identity)

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

マネージド ID の種類 |  すべて一般公開<br>グローバル Azure リージョン | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| システム割り当て済み | 使用可能 | 使用できません。 | 使用できません。 | 使用できません。 |
| ユーザー割り当て済み | 使用できません。 | 使用できません。 | 使用できません。 | 使用できません。

Azure Data Factory V2 のために (それが提供されているリージョンで) マネージド ID を 構成するには、次の一覧を参照してください。

- [Azure Portal](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-rest-api)
- [SDK](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-sdk)

### <a name="azure-api-management"></a>Azure API Management

マネージド ID の種類 |  すべて一般公開<br>グローバル Azure リージョン | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| システム割り当て済み | 使用可能 | 使用可能 | 使用できません。 | 使用できません。 |
| ユーザー割り当て済み | 使用できません。 | 使用できません。 | 使用できません。 | 使用できません。

Azure API Management のために (それが提供されているリージョンで) マネージド ID を 構成するには、次の一覧を参照してください。

- [Azure Resource Manager テンプレート](/azure/api-management/api-management-howto-use-managed-service-identity)

### <a name="azure-container-instances"></a>Azure Container Instances

マネージド ID の種類 |  すべて一般公開<br>グローバル Azure リージョン | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| システム割り当て済み | Linux:プレビュー<br>Windows:使用できません。 | 使用できません。 | 使用できません。 | 使用できません。 |
| ユーザー割り当て済み | Linux:プレビュー<br>Windows:使用できません。 | 使用できません。 | 使用できません。 | 使用できません。

Azure Container Instances のために (それが提供されているリージョンで) マネージド ID を 構成するには、次の一覧を参照してください。

- [Azure CLI](~/articles/container-instances/container-instances-managed-identity.md)
- [Azure Resource Manager テンプレート](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)


## <a name="azure-services-that-support-azure-ad-authentication"></a>Azure AD 認証をサポートしている Azure サービス

次のサービスは、Azure AD 認証をサポートしており、Azure リソースのマネージド ID を使用するクライアント サービスでテストされています。

| Service | Resource ID | Status | アクセス権を割り当てる |
| ------- | ----------- | ------ | ---- | ------------- |
| Azure Resource Manager | `https://management.azure.com/` | 使用可能 | [Azure Portal](howto-assign-access-portal.md) <br>[PowerShell](howto-assign-access-powershell.md) <br>[Azure CLI](howto-assign-access-CLI.md) <br>[Azure Resource Manager テンプレート](../../role-based-access-control/role-assignments-template.md) |
| Azure Key Vault | `https://vault.azure.net` | 使用可能 |  
| Azure Data Lake | `https://datalake.azure.net/` | 使用可能 |
| Azure SQL | `https://database.windows.net/` | 使用可能 |
| Azure Event Hubs | `https://eventhubs.azure.net` | プレビュー |
| Azure Service Bus | `https://servicebus.azure.net` | プレビュー |
| Azure Storage | `https://storage.azure.com/` | プレビュー |
