---
title: ユーザーが Azure Key Vault に接続すると ASP.NET プロジェクトに対して行われる変更 | Microsoft Docs
description: Visual Studio 接続済みサービスを使用して Key Vault に接続するときに ASP.NET プロジェクトで何が起こるかについて説明します。
services: key-vault
author: ghogen
manager: douge
tags: azure-resource-manager
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/15/2018
ms.author: ghogen
ms.openlocfilehash: a74e4e10681f6b91e028067d8985408b0745dcd2
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2018
ms.locfileid: "42142645"
---
# <a name="what-happened-to-my-aspnet-project-visual-studio-key-vault-connected-service"></a>ASP.NET プロジェクトの変更点 (Visual Studio Key Vault 接続済みサービス)

> [!div class="op_single_selector"]
> - [作業の開始](vs-key-vault-aspnet-get-started.md)
> - [変更内容](vs-key-vault-aspnet-what-happened.md)

この記事では、[Visual Studio を使用して Key Vault 接続済みサービス](vs-key-vault-add-connected-service.md)を追加したときに、ASP.NET プロジェクトに対して行われる正確な変更内容を説明します。

接続済みサービスを使用する方法については、[使用開始](vs-key-vault-aspnet-get-started.md)に関する記事をご覧ください。

## <a name="added-references"></a>追加された参照

プロジェクト ファイル (*.NET 参照) と `packages.config` (NuGet 参照) に影響します。

| type | リファレンス |
| --- | --- |
| .NET; NuGet | Microsoft.Azure.KeyVault |
| .NET; NuGet | Microsoft.Azure.KeyVault.WebKey |
| .NET; NuGet | Microsoft.Rest.ClientRuntime |
| .NET; NuGet | Microsoft.Rest.ClientRuntime.Azure |

## <a name="added-files"></a>追加されるファイル

- ConnectedService.json が追加されます。このファイルは、接続済みサービス プロバイダー、バージョン、およびドキュメントへのリンクに関する情報を記録します。

## <a name="project-file-changes"></a>プロジェクト ファイルの変更

- 接続済みサービスの ItemGroup と ConnectedServices.json ファイルが追加されます。
- 「[追加された参照](#added-references)」セクションで説明されている .NET アセンブリへの参照。

## <a name="webconfig-or-appconfig-changes"></a>web.config または app.config の変更

- 次の構成エントリが追加されます。

    ```xml
    <appSettings>
       <add key="vaultName" value="<your Key Vault name>" />
       <add key="vaultUri" value="<the URI to your Key Vault in Azure>" />
    </appSettings>
    ```

## <a name="changes-on-azure"></a>Azure での変更

- リソース グループを作成します (または、既存のものを使用します)。
- 指定したリソース グループにキー コンテナーが作成されます。

