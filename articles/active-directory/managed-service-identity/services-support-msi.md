---
title: マネージド サービス ID をサポートする Azure サービス
description: マネージド サービス ID と Azure AD 認証をサポートするサービスの一覧を示します
services: active-directory
author: daveba
ms.author: daveba
ms.date: 06/27/2018
ms.topic: reference
ms.service: active-directory
ms.component: msi
manager: mtillman
ms.openlocfilehash: b39d5144eacfe6d621dae54da66b1306db516476
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2018
ms.locfileid: "37060156"
---
# <a name="services-that-support-managed-service-identity"></a>マネージド サービス ID をサポートするサービス 

管理対象サービス ID は、Azure Active Directory で自動管理対象 ID を使用する Azure サービスを提供します。 マネージド ID を使用すると、コードに資格情報が含まれていなくても、Azure AD 認証をサポートする任意のサービスに対して認証を行うことができます。 Azure 全体で MSI と Azure AD 認証を統合する処理を行っています。 更新プログラムがないかどうか、頻繁に確認してください。

## <a name="azure-services-that-support-managed-service-identity"></a>管理対象サービス ID をサポートする Azure サービス

次の Azure サービスは管理対象サービス ID をサポートします。

| サービス | 状態 | 日付 | 構成 | トークンを取得する |
| ------- | ------ | ---- | --------- | ----------- |
| Azure Virtual Machines | プレビュー | 2017 年 9 月 | [Azure Portal](qs-configure-portal-windows-vm.md)<br>[PowerShell](qs-configure-powershell-windows-vm.md)<br>[Azure CLI](qs-configure-cli-windows-vm.md)<br>[Azure リソース マネージャーのテンプレート](qs-configure-template-windows-vm.md) | [REST](how-to-use-vm-token.md#get-a-token-using-http)<br>[.NET](how-to-use-vm-token.md#get-a-token-using-c)<br>[Bash/Curl](how-to-use-vm-token.md#get-a-token-using-curl)<br>[Go](how-to-use-vm-token.md#get-a-token-using-go)<br>[PowerShell](how-to-use-vm-token.md#get-a-token-using-azure-powershell) |
| Azure App Service | 使用可能 | 2018 年 6 月 | [Azure Portal](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Azure CLI](/azure/app-service/app-service-managed-service-identity#using-the-azure-cli)<br>[Azure PowerShell](/azure/app-service/app-service-managed-service-identity#using-azure-powershell)<br>[Azure Resource Manager テンプレート](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol)<br>[.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[JavaScript](/azure/app-service/app-service-managed-service-identity#token-js)<br>[PowerShell](/azure/app-service/app-service-managed-service-identity#token-powershell)  |
| Azure Functions | 使用可能 | 2018 年 6 月 | [Azure Portal](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Azure CLI](/azure/app-service/app-service-managed-service-identity#using-the-azure-cli)<br>[Azure PowerShell](/azure/app-service/app-service-managed-service-identity#using-azure-powershell)<br>[Azure Resource Manager テンプレート](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol)<br>[.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[JavaScript](/azure/app-service/app-service-managed-service-identity#token-js)<br>[PowerShell](/azure/app-service/app-service-managed-service-identity#token-powershell) |
| Azure Data Factory V2 | プレビュー | 2017 年 11 月 | [Azure Portal](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity)<br>[PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-powershell)<br>[REST](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-rest-api)<br>[SDK](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-sdk) |


## <a name="azure-services-that-support-azure-ad-authentication"></a>Azure AD 認証をサポートしている Azure サービス

次のサービスは、Azure AD 認証をサポートしており、管理対象サービス ID を使用するクライアント サービスでテストされています。

| サービス | Resource ID | 状態 | 日付 | アクセス権を割り当てる |
| ------- | ----------- | ------ | ---- | ------------- |
| Azure Resource Manager | https://management.azure.com/ | 使用可能 | 2017 年 9 月 | [Azure Portal](howto-assign-access-portal.md) <br>[PowerShell](howto-assign-access-powershell.md) <br>[Azure CLI](howto-assign-access-CLI.md) |
| Azure Key Vault | https://vault.azure.net | 使用可能 | 2017 年 9 月 | |
| Azure Data Lake | https://datalake.azure.net/ | 使用可能 | 2017 年 9 月 | |
| Azure SQL | https://database.windows.net/ | 使用可能 | 2017 年 10 月 | |
| Azure Event Hubs | https://eventhubs.azure.net | 使用可能 | 2017 年 12 月 | |
| Azure Service Bus | https://servicebus.azure.net | 使用可能 | 2017 年 12 月 | |
| Azure Storage | https://storage.azure.com/ | プレビュー | 2018 年 5 月 | |
