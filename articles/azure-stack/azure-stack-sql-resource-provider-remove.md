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
ms.openlocfilehash: c2686a2d5241af46e70263d1827028aa7e9b2138
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2018
ms.locfileid: "33206169"
---
# <a name="remove-the-sql-resource-provider"></a>SQL リソース プロバイダーの削除

SQL リソース プロバイダーを削除するには、最初にすべての依存関係を削除する必要があります。

1. このバージョンの SQL リソースプロバイダー アダプターに対してダウンロードした元のデプロイ パッケージがあることを確認します。

2. すべてのユーザー データベースをリソース プロバイダーから削除する必要があります  (ユーザー データベースを削除してもデータは削除されません。)このタスクは、ユーザー自身で実行する必要があります。

3. 管理者は、SQL リソース プロバイダー アダプターからホスティング サーバーを削除する必要があります。

4. 管理者は、SQL リソース プロバイダー アダプターを参照するプランをすべて削除する必要があります。

5. 管理者は、SQL リソース プロバイダー アダプターに関連付けられているすべての SKU とクォータを削除する必要があります。

6. 次の要素を使用してデプロイ スクリプトを再実行します。
    - -Uninstall パラメーター
    - Azure Resource Manager エンドポイント
    - DirectoryTenantID
    - サービス管理者アカウントの資格情報

