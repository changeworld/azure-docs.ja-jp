---
title: 'PowerShell:BYOK TDE の有効化 - Azure SQL Database Managed Instance '
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
ms.date: 11/05/2019
ms.openlocfilehash: ddffda5229c9c0d33c563e3ae7b4a884f0f92dff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "73691393"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>Azure Key Vault の独自のキーを使用して Managed Instance で Transparent Data Encryption を管理する

この PowerShell スクリプトの例では、Azure Key Vault のキーを使用し、Azure SQL Managed Instance のユーザー管理キーで Transparent Data Encryption (TDE) が構成されます。 よく "TDE の Bring Your Own Key" と呼ばれるシナリオです。 ユーザー管理キーを使用した TDE の詳細については、[Azure SQL の TDE Bring Your Own Key](../transparent-data-encryption-byok-azure-sql.md) に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

- 既存のマネージド インスタンス。 「[PowerShell を使用して Azure SQL Database マネージド インスタンスを作成する](sql-database-create-configure-managed-instance-powershell.md)」を参照してください。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

PowerShell をローカルで使用する場合も、Azure Cloud Shell を使用する場合も、AZ PowerShell 2.3.2 以降のバージョンが必要です。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照するか、または次のサンプル スクリプトを使用して、現在のユーザーを対象にモジュールをインストールします。

`Install-Module -Name Az -AllowClobber -Scope CurrentUser`

PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。

## <a name="sample-scripts"></a>サンプルのスクリプト

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>次のステップ

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他の SQL Database 用の PowerShell サンプル スクリプトは、[Azure SQL Database 用の PowerShell スクリプト](../sql-database-powershell-samples.md)のページにあります。
