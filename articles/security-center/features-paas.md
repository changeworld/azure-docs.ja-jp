---
title: サポートされている Azure PaaS リソース用の Microsoft Defender for Cloud の機能。
description: このページでは、サポートされている Azure PaaS リソース用の Microsoft Defender for Cloud の機能の可用性について説明します。
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 04/25/2021
ms.author: memildin
ms.openlocfilehash: c4ed4868cdaa8363419d1f4ca5e9267509df5ed4
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131437982"
---
# <a name="feature-coverage-for-azure-paas-services"></a><a name="paas-services">Azure PaaS サービスを対象とする機能</a>

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

下の表に、サポートされている Azure PaaS リソース用の Microsoft Defender for Cloud の機能の可用性を示します。

|サービス|推奨事項 (無料)|セキュリティのアラート |脆弱性評価|
|:----|:----:|:----:|:----:|
|Azure App Service|✔|✔|-|
|Azure Automation アカウント|✔|-|-|
|Azure Batch アカウント|✔|-|-|
|Azure Blob Storage|✔|✔|-|
|Azure Cache for Redis|✔|-|-|
|Azure クラウド サービス|✔|-|-|
|Azure Cognitive Search|✔|-|-|
|Azure Container Registry|✔|✔|✔|
|Azure Cosmos DB*|✔|✔|-|
|Azure Data Lake Analytics|✔|-|-|
|Azure Data Lake Storage|✔|✔|-|
|Azure Database for MySQL*|-|✔|-|
|Azure Database for PostgreSQL*|-|✔|-|
|Azure Event Hubs 名前空間|✔|-|-|
|Azure Functions アプリ|✔|-|-|
|Azure Key Vault|✔|✔|-|
|Azure Kubernetes Service|✔|✔|-|
|Azure Load Balancer|✔|-|-|
|Azure Logic Apps|✔|-|-|
|Azure SQL データベース|✔|✔|✔|
|Azure SQL Managed Instance|✔|✔|✔|
|Azure Service Bus 名前空間|✔|-|-|
|Azure Service Fabric アカウント|✔|-|-|
|Azure Storage アカウント|✔|✔|-|
|Azure Stream Analytics|✔|-|-|
|Azure サブスクリプション|✔ **|✔|-|
|Azure Virtual Network</br> (サブネット、NIC、ネットワーク セキュリティ グループを含む)|✔|-|-|

\* 現在、これらの機能はプレビューでサポートされています。

\*\* Azure Active Directory (Azure AD) の推奨事項は、[強化された保護機能が有効](enable-enhanced-security.md)になっているサブスクリプションでのみ使用できます。