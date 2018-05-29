---
title: Azure Stack での SQL データベースの使用 | Microsoft Docs
description: SQL データベースを Azure Stack にサービスとしてデプロイする方法と、SQL Server リソース プロバイダー アダプターの簡単なデプロイ手順について説明します。
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
ms.date: 05/01/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: b1cc1fad6b0831bcf0bab5ba4f37b753c3cf33ca
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2018
ms.locfileid: "33206172"
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>Microsoft Azure Stack で SQL データベースを使用する
SQL Server リソースプロバイダー アダプターを使用して、SQL データベースを [Azure Stack](azure-stack-poc.md) のサービスとして公開します。 リソースプロバイダーをインストールして 1 つまたは複数の SQL Server インスタンスに接続すると、御社および御社のユーザーは次のものを作成できます。
- クラウドネイティブ アプリ向けデータベース。
- SQL に基づいた Web サイト。
- SQL に基づいたワークロード。
SQL Server をホストする仮想マシン (VM) を毎回プロビジョニングする必要はありません。

リソース プロバイダーでは、[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) のすべてのデータベース管理機能がサポートされるわけではありません。 たとえば、エラスティック データベース プールと、データベースのパフォーマンスを自動的に増減する機能は使用できません。 ただし、リソース プロバイダーでは、同様の作成、読み取り、更新、および削除 (CRUD) 操作がサポートされます。 API は、SQL Database と互換性がありません。

## <a name="sql-resource-provider-adapter-architecture"></a>SQL リソースプロバイダー アダプターのアーキテクチャ
リソース プロバイダーは、次の 3 つのコンポーネントで構成されています。

- **SQL リソース プロバイダー アダプター VM**。これはプロバイダー サービスを実行する Windows 仮想マシンです。
- **リソース プロバイダー自体**。これはプロビジョニング要求を処理し、データベース リソースを公開します。
- **SQL Server をホストするサーバー**。これはデータベースに容量を提供し、ホスティング サーバーと呼ばれます。

1 つ (以上) の SQL Server インスタンスを作成する、または外部 SQL Server インスタンスへのアクセスを提供する、あるいはその両方が必要です。

> [!NOTE]
> Azure Stack 統合システムにインストールされたホスティング サーバーは、テナント サブスクリプションから作成する必要があります。 既定のプロバイダー サブスクリプションからは作成できません。 それらは、テナント ポータルから、または適切なサインインで PowerShell セッションから作成する必要があります。 すべてのホスティング サーバーは課金対象の仮想マシンであり、適切なライセンスを必要とします。 サービス管理者は、テナント サブスクリプションの所有者になることができます。


## <a name="next-steps"></a>次の手順

[SQL Server リソース プロバイダーのデプロイ](azure-stack-sql-resource-provider-deploy.md)
