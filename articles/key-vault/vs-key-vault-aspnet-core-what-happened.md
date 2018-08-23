---
title: ユーザーが Azure Key Vault に接続すると ASP.NET Core プロジェクトに対して行われる変更 | Microsoft Docs
description: Visual Studio 接続済みサービスを使用して Key Vault に接続するときに ASP.NET Core プロジェクトで何が起こるかについて説明します。
services: key-vault
author: ghogen
manager: douge
tags: azure-resource-manager
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 04/15/2018
ms.author: ghogen
ms.openlocfilehash: b7cbe55fa3a524965e0ebc16c5ff350a60d6e440
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2018
ms.locfileid: "42144617"
---
# <a name="what-happened-to-my-aspnet-core-project-visual-studio-key-vault-connected-service"></a>ASP.NET Core プロジェクトの変更点 (Visual Studio Key Vault 接続済みサービス)

> [!div class="op_single_selector"]
> - [作業の開始](vs-key-vault-aspnet-core-get-started.md)
> - [変更内容](vs-key-vault-aspnet-core-what-happened.md)

この記事では、[Visual Studio を使用して Key Vault 接続済みサービス](vs-key-vault-add-connected-service.md)を追加したときに、ASP.NET プロジェクトに対して行われる正確な変更内容を説明します。

接続済みサービスを使用する方法については、[使用開始](vs-key-vault-aspnet-core-get-started.md)に関する記事をご覧ください。

## <a name="added-references"></a>追加された参照

プロジェクト ファイルの *.NET リファレンスと NuGet パッケージのリファレンスに影響します。

| type | リファレンス |
| --- | --- |
| NuGet | Microsoft.AspNetCore.AzureKeyVault.HostingStartup |

## <a name="added-files"></a>追加されるファイル

- ConnectedService.json が追加されます。このファイルには、接続済みサービス プロバイダー、バージョン、およびドキュメントへのリンクに関する情報が記録されます。

## <a name="project-file-changes"></a>プロジェクト ファイルの変更

- 接続済みサービスの ItemGroup と ConnectedServices.json ファイルが追加されます。

## <a name="launchsettingsjson-changes"></a>launchsettings.json の変更

- IIS Express プロファイルと、ご使用の Web プロジェクト名に一致するプロファイルの両方に、次の環境変数エントリが追加されます。

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

## <a name="changes-on-azure"></a>Azure での変更

- リソース グループを作成します (または、既存のものを使用します)。
- 指定したリソース グループにキー コンテナーが作成されます。

