---
title: Azure Analysis Services のサーバー管理者ロールにサービス プリンシパルを追加する | Microsoft Docs
description: サーバー管理者ロールに自動化サービス プリンシパルを追加する方法について説明します
services: analysis-services
documentationcenter: ''
author: minewiskan
manager: kfile
editor: ''
ms.assetid: ''
ms.service: analysis-services
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: owend
ms.openlocfilehash: 8e51b80e184b2b1ff24b1051b55088fbc54c271c
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/08/2018
---
# <a name="add-a-service-principle-to-the-server-administrator-role"></a>サーバー管理者ロールへのサービス プリンシパルの追加 

 無人の PowerShell タスクを自動化するには、管理対象の Analysis Services サーバー上で、サービス プリンシパルに**サーバー管理者**権限が付与されている必要があります。 この記事では、Azure AS サーバー上のサーバー管理者ロールにサービス プリンシパルを追加する方法について説明します。

## <a name="before-you-begin"></a>開始する前に
このタスクを完了するには、Azure Active Directory にサービス プリンシパルが登録されている必要があります。

[サービス プリンシパルの作成 - Azure Portal](../azure-resource-manager/resource-group-create-service-principal-portal.md)   
[サービス プリンシパルの作成 - PowerShell](../azure-resource-manager/resource-group-authenticate-service-principal.md)

## <a name="required-permissions"></a>必要なアクセス許可
このタスクを完了するには、Azure AS サーバーに対する[サーバー管理者](analysis-services-server-admins.md)権限が必要です。 

## <a name="add-service-principle-to-server-administrators-role"></a>サーバー管理者ロールへのサービス プリンシパルの追加

1. SSMS で、目的の Azure AS サーバーに接続します。
2. **[サーバーのプロパティ]** > **[セキュリティ]** で、**[追加]** をクリックします。
3. **[ユーザーまたはグループを選択します]** で、登録されているアプリを名前で検索し、選択した後、**[追加]** をクリックします。

    ![サービス プリンシパル アカウントの検索](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. サービス プリンシパルのアカウント ID を確認し、**[OK]** をクリックします。
    
    ![サービス プリンシパル アカウントの検索](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)


> [!NOTE]
> AzureRm コマンドレットを使用したサーバー操作の場合、スケジューラを実行するサービス プリンシパルが、[Azure のロールベースのアクセス制御 (RBAC)](../active-directory/role-based-access-control-what-is.md) でリソースの**所有者**ロールに属していることも必要になります。 

## <a name="related-information"></a>関連情報

* [SQL Server PowerShell モジュールのダウンロード](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [SQL Server Management Studio (SSMS) のダウンロード](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   


