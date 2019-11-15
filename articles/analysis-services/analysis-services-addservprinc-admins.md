---
title: Azure Analysis Services の管理者ロールにサービス プリンシパルを追加する | Microsoft Docs
description: Azure Analysis Services のサーバー管理者ロールに自動化サービス プリンシパルを追加する方法について説明します
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 0a3a86283c8ec9876fbec049a2a1a110eb1a80f3
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73573609"
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>サーバー管理者ロールへのサービス プリンシパルの追加 

 無人の PowerShell タスクを自動化するには、管理対象の Analysis Services サーバー上で、サービス プリンシパルに**サーバー管理者**権限が付与されている必要があります。 この記事では、Azure AS サーバー上のサーバー管理者ロールにサービス プリンシパルを追加する方法について説明します。

## <a name="before-you-begin"></a>開始する前に
このタスクを完了するには、Azure Active Directory にサービス プリンシパルが登録されている必要があります。

[サービス プリンシパルを作成する - Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md)   
[サービス プリンシパルを作成する - PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="required-permissions"></a>必要なアクセス許可
このタスクを完了するには、Azure AS サーバーに対する[サーバー管理者](analysis-services-server-admins.md)権限が必要です。 

## <a name="add-service-principal-to-server-administrators-role"></a>サーバー管理者ロールへのサービス プリンシパルの追加

1. SSMS で、目的の Azure AS サーバーに接続します。
2. **[サーバーのプロパティ]**  >  **[セキュリティ]** で、 **[追加]** をクリックします。
3. **[ユーザーまたはグループを選択します]** で、登録されているアプリを名前で検索し、選択した後、 **[追加]** をクリックします。

    ![サービス プリンシパル アカウントの検索](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. サービス プリンシパルのアカウント ID を確認し、 **[OK]** をクリックします。
    
    ![サービス プリンシパル アカウントの検索](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)


> [!NOTE]
> Azure PowerShell コマンドレットを使用したサーバー操作の場合、スケジューラを実行するサービス プリンシパルが、[Azure のロールベースのアクセス制御 (RBAC)](../role-based-access-control/overview.md) でリソースの**所有者**ロールに属していることも必要になります。 

## <a name="related-information"></a>関連情報

* [SQL Server PowerShell モジュールのダウンロード](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [SQL Server Management Studio (SSMS) のダウンロード](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   


