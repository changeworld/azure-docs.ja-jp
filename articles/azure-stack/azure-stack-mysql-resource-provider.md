---
title: Azure Stack で MySQL データベースを PaaS として使用する | Microsoft Docs
description: Azure Stack で MySQL リソース プロバイダーをデプロイし、MySQL データベースをサービスとして提供する方法を説明します。
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 24ba595413cde07c420a94de234d7926e0eb0e7f
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/21/2018
ms.locfileid: "36311061"
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>Microsoft Azure Stack で MySQL データベースを使用する

MySQL リソース プロバイダー API をデプロイして、Azure Stack にデプロイされている MySQL データベースを使うことができます。 リソース プロバイダー API の詳細については、「[Windows Azure Pack MySQL Resource Provider REST API Reference](https://msdn.microsoft.com/library/dn528442.aspx)」(Windows Azure Pack MySQL リソース プロバイダー REST API リファレンス) をご覧ください。

MySQL データベースは、Web サイトで一般的であり、多くの Web サイト プラットフォームをサポートしています。 たとえば、Web Apps プラットフォームをサービス (PaaS) アドオンとして使って、WordPress Web サイトを作成できます。

リソース プロバイダーをデプロイした後は、次のことができます。

* Azure Resource Manager デプロイ テンプレートを使って、MySQL サーバーとデータベースを作成します。
* MySQL データベースをサービスとして提供します。  

## <a name="mysql-resource-provider-adapter-architecture"></a>MySQL リソースプロバイダー アダプターのアーキテクチャ

リソース プロバイダーには次のコンポーネントがあります。

* **MySQL リソース プロバイダー アダプター仮想マシン (VM)**。これはプロバイダー サービスを実行する Windows Server VM です。
* **リソース プロバイダー**。要求を処理し、データベース リソースにアクセスします。
* **MySQL サーバーをホストするサーバー**。これはデータベースに容量を提供し、ホスティング サーバーと呼ばれます。 MySQL インスタンスを自分で作成することも、外部の MySQL インスタンスへのアクセスを提供することもできます。 [Azure Stack Quickstart Gallery](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows) にあるテンプレートの例を使って、次のことができます。

  * 自力で MySQL サーバーを作成する。
  * Azure Marketplace から MySQL Server をダウンロードしてデプロイする。

> [!NOTE]
> Azure Stack 統合システムにインストールされたホスティング サーバーは、テナント サブスクリプションから作成する必要があります。 既定のプロバイダー サブスクリプションからは作成できません。 それらは、テナント ポータルから、または適切なサインインで PowerShell セッションから作成する必要があります。 すべてのホスティング サーバーは課金対象の VM であり、ライセンスが必要です。 サービス管理者は、テナント サブスクリプションの所有者になることができます。

### <a name="required-privileges"></a>必要な特権

システム アカウントには、次の特権が必要です。

* **データベース:** 作成、ドロップ
* **ログイン:** 作成、設定、ドロップ、許可、取り消し  

## <a name="next-steps"></a>次の手順

[MySQL リソースプロバイダーをデプロイする](azure-stack-mysql-resource-provider-deploy.md)
