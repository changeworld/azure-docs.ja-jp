---
title: Azure Blockchain Workbench SQL DB ファイアウォールを構成する
description: Azure Blockchain Workbench SQL DB ファイアウォールを構成する方法を説明します。
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/4/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: afeea143f73fa4f7d3e373535007846a668616ab
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2018
ms.locfileid: "34074951"
---
# <a name="configure-the-azure-blockchain-workbench-database-firewall"></a>Azure Blockchain Workbench データベース ファイアウォールを構成する

この記事では、Azure Portal を使用してファイアウォール規則を構成する方法を示します。 ファイアウォール規則は、外部クライアントやアプリケーションが Azure Blockchain Workbench データベースに接続できるようにします。

## <a name="connect-to-the-blockchain-workbench-database"></a>Blockchain Workbench データベースに接続する

ルールを構成するデータベースに接続する手順は、以下の通りです。

1. Azure Blockchain Workbench リソースの**所有者**権限を持つアカウントで Azure Portal にサインインします。
2. 左側のナビゲーション ペインで、**[リソース グループ]** を選択します。
3. Blockchain Workbench のデプロイにリソース グループの名前を選択します。
4. **[タイプ]** を選択してリソースの一覧を並べ替えて、使用する **SQL Server** を選択します。
5. 次の画面キャプチャのリソース リスト例は、*master* と *lsgn-sdk* の 2 つのデータベースを示しています。 ファイアウォール規則は *lsgn-sdk* で構成します。

![Blockchain Workbench リソースを一覧表示する](media/blockchain-workbench-database-firewall/list-database-resources.png)

## <a name="create-a-database-firewall-rule"></a>データベースのファイアウォール規則を作成する

ファイアウォール規則を作成する手順は、以下の通りです。

1. "lsgn-sdk" データベースへのリンクを選択します。
2. メニュー バーで、**サーバー ファイアウォールの設定**を選択します。

   ![サーバー ファイアウォールの設定](media/blockchain-workbench-database-firewall/configure-server-firewall.png)

3. 組織の規則を作成する手順は、以下の通りです。

   * **[規則名]** を入力します。
   * アドレス範囲の **[開始 IP]** の IP アドレスを入力します。
   * アドレス範囲の **[終了 IP]** の IP アドレスを入力します。

   ![ファイアウォール規則の作成](media/blockchain-workbench-database-firewall/create-firewall-rule.png)

    > [!NOTE]
    > お使いのコンピューターの IP アドレスのみを追加する場合は、**[+ クライアント IP の追加]** を選択します。
        
1. **[保存]** を選択して、ファイアウォールの構成を保存します。
2. アプリケーションやツールから接続して、データベース用に構成した IP アドレス範囲をテストします。 SQL Server Management Studio などから試してみるとよいでしょう。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench のデータベース ビュー](blockchain-workbench-database-views.md)