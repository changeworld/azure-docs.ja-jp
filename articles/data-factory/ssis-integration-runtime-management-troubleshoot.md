---
title: Azure Data Factory で SSIS Integration Runtime 管理のトラブルシューティングを行う | Microsoft Docs
description: この記事では、SSIS Integration Runtime (SSIS IR) の管理に関する問題のトラブルシューティング ガイダンスを提供します
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/08/2019
author: chinadragon0515
ms.author: dashe
ms.reviewer: sawinark
manager: craigg
ms.openlocfilehash: 17fe8d9ed02156b8fe9aafd7adca946531895883
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68253015"
---
# <a name="troubleshoot-ssis-integration-runtime-management-in-azure-data-factory"></a>Azure Data Factory で SSIS Integration Runtime 管理のトラブルシューティングを行う

このドキュメントでは、SSIS Integration Runtime (SSIS IR) の管理に関する問題のトラブルシューティング ガイドを提供します。

## <a name="overview"></a>概要

SSIS IR のプロビジョニングまたはプロビジョニング解除に問題がある場合は、エラー メッセージが ADF ポータルに表示されるか、PowerShell コマンドレットから返されます。 エラーは常に、エラー コードと詳細なエラー メッセージが表示される形式になります。

エラーコードが InternalServerError の場合、サービスに一時的な問題があることを意味します。 後で操作をやり直すことができます。 再試行しても解決されない場合は、Azure Data Factory サポート チームにお問い合わせください。

エラー コードが InternalServerError でない場合、エラーの原因として、Azure SQL Database サーバーまたは Managed Instance、カスタム セットアップ スクリプト、および Virtual Network 構成の 3 つの主な外部依存関係が考えられます。

## <a name="azure-sql-database-server-or-managed-instance-issues"></a>Azure SQL Database サーバーまたは Managed Instance の問題

SSIS カタログ データベースを使用して SSIS IR をプロビジョニングする場合、Azure SQL Database サーバーまたは Managed Instance が必要です。 Azure SQL Database サーバーまたは Managed Instance は、SSIS IR によってアクセス可能である必要があります。 Azure SQL Database サーバーまたは Managed Instance のアカウントには、SSIS カタログ データベース (SSISDB) を作成するためのアクセス許可が必要です。 エラーが発生した場合は、エラー コードと詳細な SQL 例外メッセージが ADF ポータルに表示されます。 エラー コードのトラブルシューティングを行うには、次の手順に従います。

### <a name="azuresqlconnectionfailure"></a>AzureSqlConnectionFailure

この問題は、新しい SSIS IR をプロビジョニングしているか、IR の実行中に発生することがあります。

IR のプロビジョニング中にこのエラーが発生した場合は、次のような原因が考えられ、エラー メッセージに詳細な SqlException メッセージが表示されます。

* ネットワーク接続の問題。 SQL Server または Managed Instance ホスト名がアクセス可能であること、および SSIS IR がサーバーにアクセスするのをブロックしているファイアウォールまたは NSG がないことを確認してください。
* ログインに失敗し、SQL 認証が使用されている。 これは、指定されたアカウントが SQL Server にログインできないことを意味します。 正しいユーザー アカウントが指定されていることを確認します。
* ログインに失敗し、AAD 認証 (マネージド ID) が使用されている。 ファクトリのマネージド ID を AAD グループに追加し、マネージド ID にカタログ データベース サーバーへのアクセス許可を付与してください。
* 接続のタイムアウト。常にセキュリティ関連の構成が原因です。 新しい VM を作成し、IR が VNet 内にある場合は VM を IR と同じ VNet に参加させ、次に SSMS をインストールし、Azure SQL Database サーバーまたは Managed Instance の状態を確認することをお勧めします。

その他の問題については、SQL 例外の詳細なエラー メッセージを参照し、エラー メッセージに表示されている問題を修正してください。 まだ問題が発生している場合は、Azure SQL Database サーバーまたは Managed Instance のサポート チームにお問い合わせください。

IR の実行中にこのエラーが発生する場合は、ネットワーク セキュリティ グループまたはファイアウォールに何らかの変更があった可能性があります。それが原因で、SSIS IR ワーカー ノードが Azure SQL Database サーバーまたは Managed Instance にアクセスできなくなっています。 Azure SQL Database サーバーまたは Managed Instance にアクセスするために、SSIS IR ワーカー ノードをブロック解除してください。

### <a name="catalogcapacitylimiterror"></a>CatalogCapacityLimitError

エラー メッセージは次のようになります。"データベース 'SSISDB' のサイズ クォータに達しました。 データをパーティション分割するか、データを削除するか、インデックスを削除してください。その他の解決方法についてはドキュメントを参照してください。" 次のような解決策が考えられます。
* SSISDB のサイズ クォータを増やす。
* SSISDB の構成を変更して、次のようにサイズを小さくする。
   * 保有期間とプロジェクト バージョンの数を減らす。
   * ログの保有期間を短縮する。
   * ログの既定レベルの変更などを行う。

### <a name="catalogdbbelongstoanotherir"></a>CatalogDbBelongsToAnotherIR

このエラーは、Azure SQL Database サーバーまたは Managed Instance の持つ SSISDB が、別の IR によって作成および使用されていることを意味します。 別の Azure SQL Database サーバーまたは Managed Instance を指定するか、既存の SSISDB を削除して新しい IR を再起動する必要があります。

### <a name="catalogdbcreationfailure"></a>CatalogDbCreationFailure

このエラーは次の理由により発生する場合があります。

* SSIS IR 用に構成されているユーザー アカウントに、データベースを作成するためのアクセス許可がない。 データベースを作成する権限をユーザーに付与できます。
* 実行タイムアウトや DB 操作タイムアウトなどのデータベース タイムアウトを作成します。 後で再試行して、問題が解決されているかどうかを確認できます。 再試行しても解決できない場合は、Azure SQL Database サーバーまたは Managed Instance のサポート チームにお問い合わせください。

その他の問題については、SQL 例外のエラー メッセージを確認し、エラー メッセージに記載されている問題を修正してください。 まだ問題が発生している場合は、Azure SQL Database サーバーまたは Managed Instance のサポート チームにお問い合わせください。

### <a name="invalidcatalogdb"></a>InvalidCatalogDb

エラーメッセージは "オブジェクト名 'catalog.catalog_properties' は無効です" のようになります。これは、SSISDB という名前のデータベースが既に存在するが、SSIS IR によって作成されていないか、前回の SSIS IR プロビジョニングでエラーが発生したためにデータベースが無効な状態であることを意味します。 SSISDB という名前の既存のデータベースを削除したり、IR 用の新しい Azure SQL Database サーバーまたは Managed Instance を構成したりすることができます。

## <a name="custom-setup"></a>カスタム セットアップ

カスタム セットアップでは、お使いの SSIS IR のプロビジョニングまたは再構成の間に、独自のセットアップ手順を追加するためのインターフェイスを提供します。 詳細については、「[Azure-SSIS 統合ランタイムの設定のカスタマイズ](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)」をご覧ください。

コンテナー内のすべてのファイルが SSIS IR ワーカー ノードにダウンロードされるため、コンテナーには必要なカスタム セットアップ ファイルのみが含まれていることを確認してください。 SSIS IR でスクリプトを実行する前に、ローカル コンピューターでカスタム セットアップ スクリプトをテストして、スクリプトの実行に関する問題を修正することをお勧めします。

カスタム セットアップ スクリプト コンテナーは、IR の実行中にもチェックされます。これは、SSIS IR を定期的に更新するとき、コンテナーに再びアクセスしてカスタム セットアップ スクリプトをダウンロードし、再インストールする必要があるためです。 このチェックには、コンテナーがアクセス可能かどうか、および main.cmd ファイルが存在するかどうかが含まれます。

カスタム セットアップでエラーが発生した場合は、コード CustomSetupScriptFailure を持つエラーが表示されます。サブエラー コードを含むエラーメッセージを確認してください。  サブ エラー コードのトラブルシューティングを行うには、次の手順に従います。  

### <a name="customsetupscriptblobcontainerinaccessible"></a>CustomSetupScriptBlobContainerInaccessible

SSIS IR がカスタム セットアップのために Azure BLOB コンテナーにアクセスできないことを意味します。 コンテナーの SAS URI が到達可能で、期限切れになっていないことを確認します。

IR が実行中の状態の場合は、まず IR を停止し、新しいカスタム セットアップ コンテナーの SAS URI で IR を再構成してから、IR を再起動します。

### <a name="customsetupscriptnotfound"></a>CustomSetupScriptNotFound

SSIS IR が BLOB コンテナーでカスタム セットアップ スクリプト (main.cmd) を見つけることができないことを意味します。 main.cmd がコンテナーに存在することを確認します。これは、カスタム セットアップ インストールのためのエントリ ポイントです。

### <a name="customsetupscriptexecutionfailure"></a>CustomSetupScriptExecutionFailure

これは、カスタム セットアップ スクリプト (main.cmd) の実行に失敗したことを意味します。最初にローカル コンピューターでスクリプトを試すか、BLOB コンテナー内のカスタム セットアップ実行ログを確認してください。

### <a name="customsetupscripttimeout"></a>CustomSetupScriptTimeout

カスタム セットアップ スクリプトの実行タイムアウトを意味します。 BLOB コンテナーに必要なカスタム セットアップ ファイルのみが含まれていることを確認します。 また、BLOB コンテナー内のカスタム セットアップ実行ログを確認することもできます。 カスタム セットアップの最大期間は 45 分で設定されており、これを経過するとタイムアウトになります。この最大期間には、コンテナーからすべてのファイルをダウンロードし、SSIS IR のインストールにかかる時間が含まれます。 さらに長い期間が必要な場合は、サポート チケットを発行してください。

### <a name="customsetupscriptloguploadfailure"></a>CustomSetupScriptLogUploadFailure

カスタム セットアップ実行ログを BLOB コンテナーにアップロードできなかったことを意味します。SSIS IR に BLOB コンテナーに対する書き込みアクセス許可がないか、ストレージまたはネットワークに何らかの問題があったことが原因です。 カスタム セットアップに成功した場合、このエラーは SSIS 機能には影響しませんが、ログがありません。 他のエラーが発生してカスタム セットアップが失敗し、ログをアップロードできない場合は、まずこのエラーを先に報告してログを分析用にアップロードできるようにし、この問題が解決した後で、さらに具体的な問題を報告します。 再試行後にこの問題が解決されない場合は、Azure Data Factory サポート チームにお問い合わせください。

## <a name="virtual-network-configuration"></a>Virtual Network の構成

SSIS IR を Virtual Network (VNet) に参加させると、ユーザー サブスクリプションで VNet が使用されます。 詳細については、「[Azure-SSIS 統合ランタイムを仮想ネットワークに参加させる](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)」を参照してください。

VNet に関連する問題がある場合は、次のようなエラーが表示されます

### <a name="invalidvnetconfiguration"></a>InvalidVnetConfiguration

これは、バリアントの理由によって発生する可能性があります。 サブ エラー コードのトラブルシューティングを行うには、次の手順に従います。

### <a name="forbidden"></a>許可されていません

エラー メッセージは次のようになります。"現在のアカウントでは subnetId が有効になっていません。 Microsoft.Batch リソース プロバイダーが VNet の同じサブスクリプションに登録されていません。"

これは Azure Batch が VNet にアクセスできないことを意味します。 VNet の同じサブスクリプションに Microsoft Batch リソース プロバイダーを登録してください。

### <a name="invalidpropertyvalue"></a>InvalidPropertyValue

エラー メッセージは、"指定された VNet が存在しないか、Batch サービスがそれにアクセスできません" または "指定されたサブネット xxx は存在しません" のようになります。

これは VNet が存在しないか、Azure Batch サービスがアクセスできないか、指定されたサブネットが存在しないことを意味します。 VNet とサブネットが存在し、Azure Batch がこれらにアクセスできることを確認します。

### <a name="misconfigureddnsserverornsgsettings"></a>MisconfiguredDnsServerOrNsgSettings

メッセージは次のようなものになります。"Vnet 内の統合ランタイムのプロビジョニングに失敗しました。 DNS サーバーまたは NSG の設定が構成されている場合は、DNS サーバーにアクセスできること、NSG が正しく構成されていることを確認してください。"

DNS サーバーまたは NSG の設定の構成をいくらかカスタマイズしたことにより、SSIS IR に必要な Azure サーバー名を解決できないか、またはアクセスできなくなっている可能性があります。 詳細については、[SSIS IR VNet の構成](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)に関するドキュメントを参照してください。 問題が解決しない場合は、Azure Data Factory サポート チームにお問い合わせください。

### <a name="vnetresourcegrouplockedduringupgrade"></a>VNetResourceGroupLockedDuringUpgrade

SSIS IR は定期的に自動更新され、アップグレード時に新しい Azure Batch プールが作成され、古い Azure Batch プールが削除されます。古いプールの VNet 関連リソースが削除され、新しい VNet 関連リソースがサブスクリプションで作成されます。 このエラーは、サブスクリプションまたはリソース グループ レベルの削除ロックが原因で、古いプールの VNet 関連リソースを削除できなかったことを意味します。 削除ロックを削除してください。

### <a name="vnetresourcegrouplockedduringstart"></a>VNetResourceGroupLockedDuringStart

SSIS IR プロビジョニングは何らかの理由により失敗する可能性があり、障害が発生した場合は、作成されたすべてのリソースが削除されます。 ただし、サブスクリプションまたはリソース グループ レベルでリソース削除ロックがあるため VNet リソースを削除できませんでした。 削除ロックを解除し、IR を再起動してください。

### <a name="vnetresourcegrouplockedduringstop"></a>VNetResourceGroupLockedDuringStop

SSIS IR を停止すると、VNet に関連するすべてのリソースが削除されますが、サブスクリプションまたはリソース グループ レベルでリソース削除ロックがあるため削除できませんでした。 削除ロックを解除し、もう一度停止してみてください。

### <a name="nodeunavailable"></a>NodeUnavailable

このエラーは IR の実行中に発生し、以前正常だった IR が異常になったことを意味します。これは常に DNS サーバーまたは NSG 構成が変更されたことが原因で、SSIS IR が依存関係のあるサービスに接続できなくなります。DNS サーバーまたは NSG の構成の問題を修正してください。詳細については、[SSIS IR VNet の構成](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)に関する記事を参照してください。 問題が解決しない場合は、Azure Data Factory サポート チームにお問い合わせください。
