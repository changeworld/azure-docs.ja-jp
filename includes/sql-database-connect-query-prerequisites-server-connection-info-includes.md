---
author: MightyPen
ms.service: sql-database
ms.topic: include
ms.date: 12/06/2018
ms.author: genemi
ms.openlocfilehash: b3b7027e5ae252eb899d2a44d171ea9c9795ab54
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53286085"
---
<!-- sql-database-connect-query-prerequisites-server-connection-info-includes.md 

## Get SQL server connection information
-->

Azure SQL データベースに接続するために必要な接続情報を取得します。 後の手順で、完全修飾サーバー名、データベース名、ログイン情報が必要になります。

1. [Azure Portal](https://portal.azure.com/) にサインインします。

1. 左側のメニューから **[SQL データベース]** を選択し、**[SQL データベース]** ページで目的のデータベースをクリックします。

1. データベースの **[概要]** ページで、**[サーバー名]** の隣の完全修飾サーバー名を確認します。 サーバー名をコピーするには、名前をポイントして **[コピー]** アイコンを選択します。  

![server-name](./media/sql-database-connect-query-prerequisites-server-connection-info-includes/server-name.png)

ログイン情報を忘れた場合は、サーバー名を選択して **[SQL サーバー]** ページを開きます。 ここでは **[サーバー管理者]** の名前を確認でき、必要な場合は **[パスワードのリセット]** を選択できます。