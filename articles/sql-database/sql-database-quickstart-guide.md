---
title: クイック スタート - Azure SQL Database の単一データベース | Microsoft Docs
description: Azure SQL Database の単一データベースの基本的な使用方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 72ffdb357b5469b71da7655104add1135bf114fb
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2019
ms.locfileid: "55464814"
---
# <a name="getting-started-with-single-databases-in-azure-sql-database"></a>Azure SQL Database の単一データベースの概要

Azure SQL Database の[単一データベース](sql-database-single-index.yml)は、フル マネージド PaaS サービスとしてのデータベース (DbaaS) であり、最新のクラウド生まれのアプリケーションに最適なストレージ エンジンです。 このセクションでは、SQL Database の構成と作成を簡単に行う方法について説明します。

## <a name="quickstart-overview"></a>クイック スタートの概要

このセクションでは、単一データベースの基本的な使用方法を理解できる記事の概要を紹介します。 初めて SQL データベースを作成する場合、最も簡単な方法は [Azure portal](sql-database-get-started-portal.md) を使用することです。Azure portal では必要なパラメーターを構成できます。
作成後は、[ファイアウォール規則を構成してデータベースをセキュリティで保護する](sql-database-get-started-portal-firewall.md)必要があります。 

Azure に移行したい既存のデータベースが SQL Server 上にある場合は、[Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) をインストールして SQL Server 上のデータベースを分析し、単一データベースへの移行を妨げる可能性のある問題を探します。 問題がない場合は、データベースを `.bacpac` ファイルとしてエクスポートし、[Azure portal または SqlPackage を使用してインポートします](sql-database-import.md)。

このようなクイック スタートを使用すると、Azure クラウドでデータベースを簡単に構成、作成、およびインポートできます。

## <a name="automating-management-operations"></a>管理操作の自動化

Azure portal を使用すると、単一データベースを簡単に作成および変更できます。 別の方法として、[PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) または [Azure CLI](scripts/sql-database-create-and-configure-database-cli.md) を使用してデータベースを作成することができます。
また、[PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md) または [Azure CLI](scripts/sql-database-monitor-and-scale-database-cli.md) を使用して単一データベースを更新し、リソースをスケールすることもできます。

## <a name="migrating-to-a-single-database-with-minimal-downtime"></a>最小限のダウンタイムで単一データベースに移行する

このようなクイック スタートの記事を使用すると、`.bacpac` でデータベースを簡単に作成し、Azure にインポートできます。 ただし、`.bacpac` および `.dacpack` ファイルは、異なるバージョンの SQL Server と Azure SQL Database (単一データベース、エラスティック プール、またはマネージド インスタンス) 間でデータベースをすばやく移動するため、または DevOps パイプラインで継続的インテグレーションを実装するために設計されています。 ただし、この方法は、ソース データベースを `.bacpac` ファイルとしてエクスポートし、Azure SQL Database にインポートする処理を待つ必要があるため、運用データベースを最小限のダウンタイムで移行するためには設計されていません。特にデータベースが大きい場合は、ある程度のアプリケーションのダウンタイムが発生します。 運用データベースを移行する場合は、最小限のダウンタイムが保証される優れた移行方法が必要になるでしょう。 [データ移行サービス](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql?toc=/azure/sql-database/toc.json)は、最小限のダウンタイムでデータベースを移行できるサービスです。 この方法であれば、最小限のダウンタイムでアプリケーションをソース データベースからターゲット データベースにすばやく切り替えることができます。

## <a name="next-steps"></a>次の手順

* [Azure SQL Database でサポートされている機能の概要の一覧](sql-database-features.md)を確認します。 
* [データベースの安全性を高める](sql-database-security-tutorial.md)方法を学びます。 
* [ハウ ツー セクション](sql-database-howto-single-database.md)でさらに高度なチュートリアルを確認します。 
* [PowerShell](sql-database-powershell-samples.md) と [Azure CLI](sql-database-cli-samples.md) で記述されたその他のサンプル スクリプトを確認します。 
* データベースの構成に使用できる[管理 API](sql-database-single-databases-manage.md) について詳しく学びます。 
