---
title: PowerShell:BYOK TDE の有効化 - Azure SQL Database Managed Instance | Microsoft Docs
description: PowerShell を使用して、保存時の暗号化に BYOK Transparent Data Encryption (TDE) を使用するように Azure SQL Managed Instance を構成する方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: d428c74a486c4bcb6ea1568fbb8abd813960c708
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52976645"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>Azure Key Vault の独自のキーを使用して Managed Instance で Transparent Data Encryption を管理する

この PowerShell スクリプトの例では、Azure Key Vault のキーを使用し、Azure SQL Managed Instance の Bring Your Own Key シナリオで Transparent Data Encryption (TDE) が構成されます。 Bring Your Own Key (BYOK) をサポートする TDE の詳細については、[Azure SQL の TDE Bring Your Own Key](../transparent-data-encryption-byok-azure-sql.md) に関する記事をご覧ください。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

PowerShell をインストールしてローカルで使用する場合、このチュートリアルでは Azure PowerShell モジュール バージョン 5.7.0 以降が必要になります。 バージョンを確認するには、`Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzureRmAccount` を実行して Azure との接続を作成することも必要です。

PowerShell をローカルでインストールして使用する場合、このチュートリアルでは AzureRM.Sql PowerShell パッケージのプレビュー バージョン (*4.11.6-preview*) も必要です。 インストールするには次のコマンドを実行します。`Install-Module -Name AzureRM.Sql -RequiredVersion 4.11.6-preview -AllowPrerelease`

## <a name="sample-scripts"></a>サンプルのスクリプト

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>次の手順

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他の SQL Database 用の PowerShell サンプル スクリプトは、[Azure SQL Database 用の PowerShell スクリプト](../sql-database-powershell-samples.md)のページにあります。
