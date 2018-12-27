---
title: Azure Stack 上の SQL リソース プロバイダーを削除する | Microsoft Docs
description: Azure Stack のデプロイから SQL リソース プロバイダーを削除する方法について説明します。
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
ms.date: 11/20/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.openlocfilehash: 8b6257519c3b1db2180335523047e8dc55558d57
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52954594"
---
# <a name="remove-the-sql-resource-provider"></a>SQL リソース プロバイダーの削除

SQL リソース プロバイダーを削除する前に、プロバイダーの依存関係をすべて削除する必要があります。 また、リソース プロバイダーのインストールに使用したデプロイ パッケージのコピーも必要になります。

> [!NOTE]
> リソース プロバイダーのインストーラーのダウンロード リンクは、[リソース プロバイダーを展開するための前提条件](./azure-stack-sql-resource-provider-deploy.md#prerequisites)に関するページにあります。

SQL リソース プロバイダーを削除しても、ホスティング サーバーからテナント データベースが削除されることはありません。

## <a name="dependency-cleanup"></a>依存関係のクリーンアップ

DeploySqlProvider.ps1 スクリプトを実行してリソース プロバイダーを削除する前に、いくつかのクリーンアップ タスクを実行する必要があります。

次のクリーンアップ タスクは、Azure Stack オペレーターが担当します。

* SQL アダプターを参照しているすべてのプランを削除する。
* SQL アダプターに関連付けられているすべてのクォータを削除する。

## <a name="to-remove-the-sql-resource-provider"></a>SQL リソース プロバイダーを削除するには

1. 既存の SQL リソース プロバイダーの依存関係がすべて削除されていることを確認します。

   > [!NOTE]
   > 依存リソースがリソース プロバイダーを現在使用している場合でも、SQL リソース プロバイダーのアンインストールは続行されます。
  
2. SQL リソース プロバイダーのインストール パッケージのコピーを入手し、自己展開形式ファイルを実行してコンテンツを一時ディレクトリに展開します。

3. 新しい管理者特権の PowerShell コンソール ウィンドウを開き、SQL リソース プロバイダーのインストール ファイルを抽出したディレクトリに変更します。

4. 次のパラメーターを使用して、DeploySqlProvider.ps1 スクリプトを実行します。

    * **Uninstall**。 リソース プロバイダーと関連付けられているすべてのリソースを削除します。
    * **PrivilegedEndpoint**。 特権エンドポイントの IP アドレスまたは DNS 名。
    * **AzureEnvironment**。 Azure Stack のデプロイに使用する Azure 環境。 Azure AD のデプロイでのみ必須です。
    * **CloudAdminCredential**。 特権エンドポイントへのアクセスに必要な、クラウド管理者の資格情報。
    * **AzCredential**。 Azure Stack サービス管理者アカウントの資格情報。 Azure Stack のデプロイに使用したのと同じ資格情報を使用します。

## <a name="next-steps"></a>次の手順

[App Services を PaaS として提供する](azure-stack-app-service-overview.md)
