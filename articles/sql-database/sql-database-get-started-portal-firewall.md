---
title: 'Azure Portal: SQL データベース ファイアウォール規則の作成 | Microsoft Docs'
description: SQL Database サーバーレベルのファイアウォール規則を作成する
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 12/01/2018
ms.openlocfilehash: cab92539b5019d4807ddefb2b84279c844f53016
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2018
ms.locfileid: "52721903"
---
# <a name="quickstart-create-a-server-level-firewall-rule-for-your-sql-database-using-the-azure-portal"></a>クイック スタート: Azure portal を使用して SQL データベースのサーバーレベルのファイアウォール規則を作成する

このクイック スタートでは、Azure SQL データベースのサーバーレベルのファイアウォール規則を作成し、オンプレミスのリソースから接続できるようにする方法について説明します。

## <a name="prerequisites"></a>前提条件

このクイック スタートでは、「[Azure portal で Azure SQL データベースを作成する](sql-database-get-started-portal.md)」で作成したリソースを出発点として使用します。

## <a name="sign-in-to-the-azure-portal"></a>Azure ポータルにサインインします。

[Azure Portal](https://portal.azure.com/) にサインインします。

## <a name="create-a-server-level-firewall-rule"></a>サーバーレベルのファイアウォール規則を作成する

SQL Database サービスは、サーバーレベルでファイアウォールを作成します。 このファイアウォールは、ファイアウォールを開くファイアウォール規則が作成されていない場合、アプリケーションやツールからサーバーまたは任意のサーバー データベースへの接続を禁止します。 Azure の外部 IP アドレスから接続する場合は、特定の IP アドレスまたはアドレスの範囲を対象とするファイアウォール規則を作成します。 ファイアウォール規則の詳細については、[SQL Database サーバーレベルのファイアウォール規則](sql-database-firewall-configure.md)に関するページを参照してください。

> [!NOTE]
> SQL Database の通信は、ポート 1433 上で行われます。 企業ネットワーク内から接続しようとしても、ポート 1433 での送信トラフィックがネットワークのファイアウォールで禁止されている場合があります。 その場合、会社の IT 部門によってポート 1433 が開放されない限り、Azure SQL Database サーバーに接続することはできません。
>

以下の手順に従い、クライアントの IP アドレスに対してサーバーレベルのファイアウォール規則を作成し、その IP アドレスのみに SQL Database ファイアウォールを介して外部接続できるようにします。

1. [前提条件の Azure SQL データベース](#prerequisites)のデプロイが完了したら、左側のメニューから **[SQL データベース]** を選択し、**[SQL データベース]** ページで **mySampleDatabase** を選択します。 このデータベースの概要ページが開くと、完全修飾サーバー名 (**mynewserver-20170824.database.windows.net** など) や追加の構成オプションが表示されます。

2. この完全修飾サーバー名をコピーします。これは他のクイック スタートでサーバーとそのデータベースに接続するときに使用します。

   ![サーバー名](./media/sql-database-get-started-portal/server-name.png)

3. ツール バーで **[サーバー ファイアウォールの設定]** を選択します。 SQL Database サーバーの **[ファイアウォール設定]** ページが開きます。

   ![サーバーのファイアウォール規則](./media/sql-database-get-started-portal/server-firewall-rule.png)

4. ツール バーの **[クライアント IP の追加]** を選択し、現在の IP アドレスをファイアウォール規則に追加します。 ファイアウォール規則は、単一の IP アドレスまたは IP アドレスの範囲に対して、ポート 1433 を開くことができます。

   > [!IMPORTANT]
   > 既定では、すべての Azure サービスで、SQL Database ファイアウォール経由のアクセスが有効になります。 すべての Azure サービスに対して無効にするには、このページの **[オフ]** を選択します。
   >

5. **[保存]** を選択します。 論理サーバーでポート 1433 を開いている現在の IP アドレスに対して、サーバーレベルのファイアウォール規則が作成されます。

6. **[ファイアウォール設定]** ページを閉じます。

SQL Server Management Studio やその他の任意のツールを使用して、SQL Database サーバーとそのデータベースに、前に作成したサーバー管理者アカウントを使用してこの IP アドレスから接続できるようになりました。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

データベースに接続してクエリを実行するためのさまざまな方法を紹介した「[次のステップ](#next-steps)」に進む場合は、これらのリソースを保存しておいてください。 一方、このクイック スタートで作成したリソースを削除する場合は、次の手順に従います。


1. Azure portal の左側のメニューで、**[リソース グループ]**、**[myResourceGroup]** の順に選択します。
2. リソース グループのページで **[削除]** を選択し、テキスト ボックスに「**myResourceGroup**」と入力してから **[削除]** を選択します。

## <a name="next-steps"></a>次の手順

- これで、データベースが作成されたので、任意のツールまたは言語を使用して[接続し、クエリを実行](sql-database-connect-query.md)できます。たとえば、以下が可能です。
  - [SQL Server Management Studio を使用して接続およびクエリを実行する](sql-database-connect-query-ssms.md)
  - [Azure Data Studio を使用して接続およびクエリを実行する](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- 初めてのデータベースを設計し、テーブルを作成して、データを挿入する方法については、次のいずれかのチュートリアルを参照してください。
  - [SSMS を使用した最初の Azure SQL データベースの設計](sql-database-design-first-database.md)
  - [C# と ADO.NET で Azure SQL データベースを設計し、接続する](sql-database-design-first-database-csharp.md)
