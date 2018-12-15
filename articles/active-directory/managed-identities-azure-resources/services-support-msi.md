---
title: Azure リソースのマネージド ID をサポートする Azure サービス
description: Azure リソースのマネージド ID と Azure AD 認証をサポートするサービスの一覧
services: active-directory
author: daveba
ms.author: daveba
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: active-directory
ms.component: msi
manager: mtillman
ms.openlocfilehash: a75d3265e70efacc9e581abe32faacb6c5fb1b37
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2018
ms.locfileid: "52850518"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Azure リソースのマネージド ID をサポートするサービス

Azure リソースのマネージド ID は、Azure Active Directory で自動的に管理される ID を Azure サービスに提供します。 マネージド ID を使用すると、コードに資格情報が含まれていなくても、Azure AD 認証をサポートする任意のサービスに対して認証を行うことができます。 Azure リソースのマネージド ID と Azure AD 認証の Azure 全体への統合が進行中です。 更新プログラムがないかどうか、頻繁に確認してください。

> [!NOTE]
> Azure リソースのマネージド ID は、以前のマネージドサービス ID (MSI) の新しい名前です。

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Azure リソースのマネージド ID をサポートする Azure サービス

次の Azure サービスが、Azure リソースのマネージド ID をサポートしています。

| Service | システム割り当ての状態 | ユーザー割り当ての状態| 構成 | トークンを取得する |
| ------- | ------ | ---- | --------- | ----------- |
| Azure Virtual Machines | 使用可能 | プレビュー | [Azure Portal](qs-configure-portal-windows-vm.md)<br>[PowerShell](qs-configure-powershell-windows-vm.md)<br>[Azure CLI](qs-configure-cli-windows-vm.md)<br>[Azure リソース マネージャーのテンプレート](qs-configure-template-windows-vm.md)<br>[REST](qs-configure-rest-vm.md) | [REST](how-to-use-vm-token.md#get-a-token-using-http)<br>[.NET](how-to-use-vm-token.md#get-a-token-using-c)<br>[Bash/Curl](how-to-use-vm-token.md#get-a-token-using-curl)<br>[Go](how-to-use-vm-token.md#get-a-token-using-go)<br>[PowerShell](how-to-use-vm-token.md#get-a-token-using-azure-powershell) |
| Virtual Machine Scale Sets | 使用可能 | プレビュー | [Azure Portal](qs-configure-portal-windows-vmss.md)<br>[PowerShell](qs-configure-powershell-windows-vmss.md)<br>[Azure CLI](qs-configure-cli-windows-vmss.md)<br>[Azure リソース マネージャーのテンプレート](qs-configure-template-windows-vmss.md)<br>[REST](qs-configure-rest-vmss.md) | [REST](how-to-use-vm-token.md#get-a-token-using-http)<br>[.NET](how-to-use-vm-token.md#get-a-token-using-c)<br>[Bash/Curl](how-to-use-vm-token.md#get-a-token-using-curl)<br>[Go](how-to-use-vm-token.md#get-a-token-using-go)<br>[PowerShell](how-to-use-vm-token.md#get-a-token-using-azure-powershell)
| Azure App Service | Windows:使用可能 <br> Linux:プレビュー | プレビュー | [Azure Portal](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Azure CLI](/azure/app-service/app-service-managed-service-identity#using-the-azure-cli)<br>[Azure PowerShell](/azure/app-service/app-service-managed-service-identity#using-azure-powershell)<br>[Azure Resource Manager テンプレート](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol)<br>[.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[JavaScript](/azure/app-service/app-service-managed-service-identity#token-js)<br>[PowerShell](/azure/app-service/app-service-managed-service-identity#token-powershell)  |
| Azure Functions | 使用可能 | プレビュー | [Azure Portal](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Azure CLI](/azure/app-service/app-service-managed-service-identity#using-the-azure-cli)<br>[Azure PowerShell](/azure/app-service/app-service-managed-service-identity#using-azure-powershell)<br>[Azure Resource Manager テンプレート](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol)<br>[.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[JavaScript](/azure/app-service/app-service-managed-service-identity#token-js)<br>[PowerShell](/azure/app-service/app-service-managed-service-identity#token-powershell) |
| Azure Logic Apps | 使用可能 | 使用できません。 | [Azure Portal](/azure/logic-apps/create-managed-service-identity#azure-portal)<br>[Azure Resource Manager テンプレート](/azure/app-service/app-service-managed-service-identity#deployment-template) |  |
| Azure Data Factory V2 | 使用可能 | 使用できません。 | [Azure Portal](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity)<br>[PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-powershell)<br>[REST](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-rest-api)<br>[SDK](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-sdk) |
| Azure API Management | 使用可能 | 使用できません。 | [Azure Resource Manager テンプレート](/azure/api-management/api-management-howto-use-managed-service-identity) |
| Azure Container Instances | Linux:プレビュー<br>Windows:使用できません。 | Linux:プレビュー<br>Windows:使用できません。 | [Azure CLI](~/articles/container-instances/container-instances-managed-identity.md)<br>[Azure Resource Manager テンプレート](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)<br>[YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file) |  |


## <a name="azure-services-that-support-azure-ad-authentication"></a>Azure AD 認証をサポートしている Azure サービス

次のサービスは、Azure AD 認証をサポートしており、Azure リソースのマネージド ID を使用するクライアント サービスでテストされています。

| Service | Resource ID | Status | 日付 | アクセス権を割り当てる |
| ------- | ----------- | ------ | ---- | ------------- |
| Azure Resource Manager | `https://management.azure.com/` | 使用可能 | 2017 年 9 月 | [Azure Portal](howto-assign-access-portal.md) <br>[PowerShell](howto-assign-access-powershell.md) <br>[Azure CLI](howto-assign-access-CLI.md) <br>[Azure Resource Manager テンプレート](../../role-based-access-control/role-assignments-template.md) |
| Azure Key Vault | `https://vault.azure.net` | 使用可能 | 2017 年 9 月 | |
| Azure Data Lake | `https://datalake.azure.net/` | 使用可能 | 2017 年 9 月 | |
| Azure SQL | `https://database.windows.net/` | 使用可能 | 2017 年 10 月 | |
| Azure Event Hubs | `https://eventhubs.azure.net` | プレビュー | 2017 年 12 月 | |
| Azure Service Bus | `https://servicebus.azure.net` | プレビュー | 2017 年 12 月 | |
| Azure Storage | `https://storage.azure.com/` | プレビュー | 2018 年 5 月 | |