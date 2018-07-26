---
title: Azure ポータルから接続文字列を取得する
description: Azure ポータルから接続文字列を取得する
keywords: sql 接続,接続文字列
services: sql-database
author: dalechen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: include
ms.date: 07/13/2018
ms.author: ninarn
ms.openlocfilehash: dab7623c86bea4e562313e618f238b9b33c0fdc5
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2018
ms.locfileid: "39117292"
---
### <a name="obtain-the-connection-string-from-the-azure-portal"></a>Azure ポータルから接続文字列を取得する
[Azure Portal](https://portal.azure.com/) を使って、クライアント プログラムが Azure SQL Database と対話するために必要な接続文字列を取得します。

1. **[すべてのサービス]** > **[SQL データベース]** の順にクリックします。

2. **[SQL データベース]** ブレードの左上近辺にあるフィルター テキスト ボックスにデータベースの名前を入力します。

3. お使いのデータベースの行を選びます。

4. お使いのデータベースのブレードが表示されたら、画面を見やすくするために、**[最小化]** ボタンを選んで、参照とデータベースのフィルター処理に使ったブレードを折りたたみます。

5. お使いのデータベースのブレードで、**[データベース接続文字列の表示]** を選びます。

6. 適切な接続文字列をコピーします。 つまり、ADO.NET 接続ライブラリを使用する場合は、**[ADO.NET]** タブから適切な文字列をコピーします。

    ![データベース用の ADO 接続文字列をコピーする][20-CopyAdoConnectionString]

7. 必要に応じて、接続文字列を編集します。 つまり、接続文字列にパスワードを挿入したり、ユーザー名またはサーバー名が長すぎる場合はユーザー名から "@&lt;servername&gt;" を削除します。

8. 特定の形式の接続文字列情報を、クライアント プログラム コードに貼り付けます。

詳しくは、「[接続文字列と構成ファイル](http://msdn.microsoft.com/library/ms254494.aspx)」をご覧ください。

<!-- Image references. -->



[20-CopyAdoConnectionString]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-b.png


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->
