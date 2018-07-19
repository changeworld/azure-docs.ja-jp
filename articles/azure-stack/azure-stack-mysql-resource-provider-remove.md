---
title: Azure Stack 上の MySQL リソース プロバイダーを削除する | Microsoft Docs
description: Azure Stack のデプロイから MySQL リソース プロバイダーを削除する方法について説明します。
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
ms.date: 06/27/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: d3a615e3b92a62709a787d0463dfa3148f14d07e
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2018
ms.locfileid: "37088374"
---
# <a name="remove-the-mysql-resource-provider"></a>MySQL リソースプロバイダーを削除する

MySQL リソース プロバイダーを削除する前に、プロバイダーの依存関係をすべて削除する必要があります。 また、リソース プロバイダーのインストールに使用したデプロイ パッケージのコピーも必要になります。

## <a name="dependency-cleanup"></a>依存関係のクリーンアップ

DeployMySqlProvider.ps1 スクリプトを実行してリソース プロバイダーを削除する前に、いくつかのクリーンアップ タスクを実行する必要があります。

次のクリーンアップ タスクは、テナントが担当します。

* リソース プロバイダーからすべてのデータベースを削除する。 (テナント データベースを削除してもデータは削除されません。)
* プロバイダーの名前空間からの登録解除を行う。

次のクリーンアップ タスクは、管理者が担当します。

* MySQL アダプターからホスティング サーバーを削除する。
* MySQL アダプターを参照するすべてのプランを削除する。
* MySQL アダプターに関連付けられているすべてのクォータを削除する。

## <a name="to-remove-the-mysql-resource-provider"></a>MySQL リソースプロバイダーを削除するには

1. 既存の MySQL リソース プロバイダーの依存関係がすべて削除されていることを確認します。

   >[!NOTE]
   >依存リソースがリソース プロバイダーを現在使用している場合でも、MySQL リソース プロバイダーのアンインストールは続行されます。
  
2. MySQL リソース プロバイダー バイナリのコピーを入手し、自己展開形式ファイルを実行してコンテンツを一時ディレクトリに展開します。
3. SQL リソース プロバイダー バイナリのコピーを入手し、自己展開形式ファイルを実行してコンテンツを一時ディレクトリに展開します。
4. 新しい管理者特権の PowerShell コンソール ウィンドウを開き、MySQL リソース プロバイダー バイナリ ファイルを抽出したディレクトリに変更します。
5. 次のパラメーターを使用して、DeployMySqlProvider.ps1 スクリプトを実行します。
    - **Uninstall**。 リソース プロバイダーと関連付けられているすべてのリソースを削除します。
    - **PrivilegedEndpoint**。 特権エンドポイントの IP アドレスまたは DNS 名。
    - **CloudAdminCredential**。 特権エンドポイントへのアクセスに必要な、クラウド管理者の資格情報。
    - **DirectoryTenantID**
    - **AzCredential**。 Azure Stack サービス管理者アカウントの資格情報。 Azure Stack のデプロイに使用したのと同じ資格情報を使用します。

## <a name="next-steps"></a>次の手順

[App Services を PaaS として提供する](azure-stack-app-service-overview.md)
