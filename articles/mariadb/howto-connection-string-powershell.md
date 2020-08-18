---
title: PowerShell を使用して接続文字列を生成する - Azure Database for MariaDB
description: この記事では、Azure Database for MariaDB に接続するための接続文字列を生成する Azure PowerShell の例を提供します。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.custom: mvc, devx-track-azurepowershell
ms.topic: quickstart
ms.date: 8/5/2020
ms.openlocfilehash: b4b0853cb71ccc85826d1230fb7828ed9df24b52
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87839960"
---
# <a name="how-to-generate-an-azure-database-for-mariadb-connection-string-with-powershell"></a>PowerShell を使用して Azure Database for MariaDB 接続文字列を生成する方法

この記事では、Azure Database for MariaDB サーバーに対する接続文字列を生成する方法について説明します。 接続文字列を使用して、さまざまなアプリケーションから Azure Database for MariaDB に接続できます。

## <a name="requirements"></a>必要条件

この記事では、次のガイドで作成されたリソースを出発点として使用します。

* [クイック スタート:PowerShell を使用した Azure Database for MariaDB サーバーの作成](quickstart-create-mariadb-server-database-using-azure-powershell.md)

## <a name="get-the-connection-string"></a>接続文字列を取得する

アプリケーションを Azure Database for MariaDB に接続するための接続文字列を生成するには、`Get-AzMariaDbConnectionString` コマンドレットを使用します。 次の例では、**mydemoserver** から PHP クライアントの接続文字列が返されます。

```azurepowershell-interactive
Get-AzMariaDbConnectionString -Client PHP -Name mydemoserver -ResourceGroupName myresourcegroup
```

```Output
$con=mysqli_init();mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL); mysqli_real_connect($con, "mydemoserver.mariadb.database.azure.com", "myadmin@mydemoserver", {your_password}, {your_database}, 3306);
```

`Client` パラメーターの有効な値は次のとおりです。

* ADO&#46;NET
* JDBC
* Node.js
* PHP
* Python
* Ruby
* WebApp

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [PowerShell を使用して Azure Database for MariaDB サーバー パラメーターをカスタマイズする](howto-configure-server-parameters-using-powershell.md)
