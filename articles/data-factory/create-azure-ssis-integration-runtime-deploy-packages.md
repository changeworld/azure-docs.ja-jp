---
title: SSIS パッケージのデプロイ
description: Azure SSIS 統合ランタイムを使用して Azure Data Factory で SSIS パッケージをデプロイして実行する方法について説明します。
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/22/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d32025ab757698448f9d8a36467c09e9b501f206
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131842950"
---
# <a name="deploy-ssis-packages"></a>SSIS パッケージのデプロイ

Azure SSIS 統合ランタイムを構成した後は、Azure で直接パッケージをデプロイして実行することができます。

## <a name="using-ssisdb"></a>SSISDB の使用

SSISDB を使用する場合は、そこにパッケージをデプロイし、Azure 対応の SSDT または SSMS ツールを使用して、それらを Azure-SSIS IR 上で実行することができます。 これらのツールは、ご利用のデータベース サーバーに対し、そのサーバーのエンドポイントを介して接続します。 

- Azure SQL Database サーバーの場合、サーバー エンドポイントの形式は `<server name>.database.windows.net` です。
- プライベート エンドポイントを備えたマネージド インスタンスの場合、サーバー エンドポイントの形式は `<server name>.<dns prefix>.database.windows.net` です。
- パブリック エンドポイントを備えたマネージド インスタンスの場合、サーバー エンドポイントの形式は `<server name>.public.<dns prefix>.database.windows.net,3342` です。 

## <a name="using-file-system-azure-files-or-msdb"></a>ファイル システム、Azure Files、または MSDB の使用

SSISDB を使用しない場合は、Azure SQL Managed Instance にホストされた MSDB、ファイル システム、Azure Files のいずれかにパッケージをデプロイし、[dtutil](/sql/integration-services/dtutil-utility) および [AzureDTExec](./how-to-invoke-ssis-package-azure-enabled-dtexec.md) コマンド ライン ユーティリティを使用してそれらのパッケージを Azure-SSIS IR 上で実行できます。 

詳細については、[SSIS プロジェクトおよびパッケージのデプロイ](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages)に関する記事を参照してください。

どちらの場合も、Data Factory パイプラインの SSIS パッケージの実行アクティビティを使用して、デプロイしたパッケージを Azure-SSIS IR 上で実行することもできます。 詳細については、[ファーストクラスの Data Factory アクティビティとして SSIS パッケージの実行を呼び出す方法](./how-to-invoke-ssis-package-ssis-activity.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure portal を使用して Azure-SSIS IR をプロビジョニングする方法を学習する](create-azure-ssis-integration-runtime-portal.md)。
- [Azure PowerShell を使用して Azure-SSIS IR をプロビジョニングする方法を学習する](create-azure-ssis-integration-runtime-powershell.md)。
- [Azure Resource Manager テンプレートを使用して Azure-SSIS IR をプロビジョニングする方法を学習する](create-azure-ssis-integration-runtime-resource-manager-template.md)。

このドキュメントでは、Azure SSIS IR に関する、次のような他のトピックも参照してください。

- [Azure-SSIS 統合ランタイム](concepts-integration-runtime.md#azure-ssis-integration-runtime): この記事では、Azure-SSIS IR など、統合ランタイムに関する一般的な情報が説明されています。
- [Azure-SSIS IR を監視する](monitor-integration-runtime.md#azure-ssis-integration-runtime): この記事では、Azure-SSIS IR についての情報を取得して理解するための方法を示します。
- [Azure-SSIS IR を管理する](manage-azure-ssis-integration-runtime.md): この記事では、Azure-SSIS IR を停止、開始、または削除する方法を示しています。 また、ノードを追加することで Azure-SSIS IR をスケールアウトする方法も説明されています。
- [Azure での SSIS パッケージのデプロイ、実行、監視](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Azure での SSISDB への接続](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Windows 認証を使用したオンプレミス データ ソースへの接続](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 
- [Azure でのパッケージ実行のスケジュール設定](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
