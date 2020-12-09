---
title: Azure Migrate で評価ツールを追加する
description: Azure Migrate で評価ツールを追加する方法について説明します。
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: 9a4ed5542945b8d281ec750d9bbd3a8f444e44d2
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/07/2020
ms.locfileid: "96752262"
---
# <a name="add-assessment-tools"></a>評価ツールの追加

この記事では、[Azure Migrate](./migrate-services-overview.md) で評価ツールを追加する方法について説明します。 

- 評価ツールを追加したいが、まだ Azure Migrate プロジェクトを用意していない場合は、この[記事](create-manage-projects.md)に従ってください。
- 評価のために ISV ツール (Movere) を追加した場合は、[この手順に従って](prepare-isv-movere.md)、ツールを使用するための準備を行います。

## <a name="select-an-assessment-scenario"></a>評価シナリオを選択する

1. Azure Migrate プロジェクトで、 **[概要]** をクリックします。
2. 評価シナリオを選択します。

    - Azure に移行するコンピューターやワークロードを検出して評価するには、 **[サーバーの評価と移行]** を選択します。
    - オンプレミスの SQL Server データベースを評価するには、 **[Assess and migrate databases] (データベースの評価と移行)** を選択します。
    - オンプレミスの Web アプリを評価または移行するには、 **[さらに探索]**  >  **[Web Apps]** の順に選択します。
    - 仮想デスクトップ インフラストラクチャを評価するには、 **[さらに探索]**  >  **[仮想デスクトップ インフラストラクチャ]** の順に選択します。

    ![評価シナリオを選択するためのオプション](./media/how-to-assess/assess-scenario.png)

## <a name="select-a-server-assessment-tool"></a>サーバー評価ツールを選択する 


1. 次のようにツールを追加します。

    - ポータルの **[サーバーの評価と移行]** オプションを使用して Azure Migrate プロジェクトを作成してある場合は、Azure Migrate Server Assessment ツールがプロジェクトに自動的に追加されます。 評価ツールをさらに追加するには、 **[サーバー]** 内の **[評価ツール]** の横にある **[ツールをさらに追加する]** を選択します。
    
         ![さらに評価ツールを追加するためのボタン](./media/how-to-assess/add-assessment-tool.png)

    - 別のオプションを使用してプロジェクトを作成していて、評価ツールをまだ用意していない場合は、 **[サーバー]**  >  **[評価ツール]** で、 **[ここをクリックしてください]** を選択します。

        ![最初の評価ツールを追加するためのボタン](./media/how-to-assess/no-assessment-tool.png)

2. **[Azure Migrate]**  >  **[ツールの追加]** で、追加するツールを選択します。 次に、 **[ツールの追加]** を選択します。

    ![一覧から評価ツールを選択する](./media/how-to-assess/select-assessment-tool.png)



## <a name="select-a-database-assessment-tool"></a>データベース評価ツールを選択する

1. 次のようにツールを追加します。

    - ポータルの **[データベースの評価と移行]** オプションを使用して Azure Migrate プロジェクトを作成してある場合は、Database Assessment ツールがプロジェクトに自動的に追加されます。 評価ツールをさらに追加するには、 **[データベース]** 内の **[評価ツール]** の横にある **[ツールをさらに追加する]** を選択します。

    - 別のオプションを使用してプロジェクトを作成していて、データベース評価ツールをまだ用意していない場合は、 **[データベース]**  >  **[評価ツール]** で、 **[ここをクリックしてください]** を選択します。

2. **[Azure Migrate]**  >  **[ツールの追加]** で、追加するツールを選択します。 次に、 **[ツールの追加]** を選択します。

    ![一覧からデータベース評価ツールを選択する](./media/how-to-assess/select-database-assessment-tool.png)


## <a name="select-a-web-app-assessment-tool"></a>Web アプリ評価ツールを選択する

ポータルの **[さらに探索]**  >  **[Web Apps]** オプションを使用して Azure Migrate プロジェクトを作成してある場合は、Web App Assessment ツールがプロジェクトに自動的に追加されます。 


1. Web App Assessment ツールがプロジェクトに含まれていない場合は、 **[Web Apps]**  >  **[評価ツール]** で、 **[ここをクリックしてください]** を選択します。
    
    ![Web アプリ評価ツールを追加する](./media/how-to-assess/no-web-app-assessment-tool.png)


2. **[Azure Migrate]**  >  **[ツールの追加]** で、Web アプリ評価ツールを選択します。 次に、 **[ツールの追加]** を選択します。

    ![一覧からデータベースの移行ツールを選択する](./media/how-to-assess/select-web-app-assessment-tool.png)

 


## <a name="next-steps"></a>次のステップ

[VMware](./tutorial-discover-vmware.md) VM、[Hyper-V](./tutorial-discover-hyper-v.md)、または[物理サーバー](./tutorial-discover-physical.md)用の Azure Migrate Server Assessment ツールを使用して、評価のためにオンプレミスのマシンを検出します