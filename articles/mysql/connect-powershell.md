---
title: PowerShell を使用して接続文字列を生成する - Azure Database for MySQL
description: このクイックスタートでは、Azure Database for MySQL に接続するための接続文字列を生成する Azure PowerShell の例を提供します。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc, devx-track-azurepowershell
ms.topic: quickstart
ms.date: 8/3/2020
ms.openlocfilehash: 9de569e09e1b8bcf29f47077cbe15f0a6c40ddd1
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2020
ms.locfileid: "87544923"
---
# <a name="azure-database-for-mysql-generate-a-connection-string-with-powershell"></a>Azure Database for MySQL: PowerShell で接続文字列を生成する

この記事では、Azure Database for MySQL サーバーに対する接続文字列を生成する方法について説明します。 接続文字列を使用して、さまざまなアプリケーションから Azure Database for MySQL に接続できます。

## <a name="requirements"></a>必要条件

この記事では、次のガイドで作成されたリソースを出発点として使用します。

* [クイック スタート: PowerShell を使用して Azure Database for MySQL サーバーを作成する](quickstart-create-mysql-server-database-using-azure-powershell.md)

## <a name="get-the-connection-string"></a>接続文字列を取得する

アプリケーションを Azure Database for MySQL に接続するための接続文字列を生成するには、`Get-AzMySqlConnectionString` コマンドレットを使用します。 次の例では、**mydemoserver** から PHP クライアントの接続文字列が返されます。

```azurepowershell-interactive
Get-AzMySqlConnectionString -Client PHP -Name mydemoserver -ResourceGroupName myresourcegroup
```

```Output
$con=mysqli_init();mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL); mysqli_real_connect($con, "mydemoserver.mysql.database.azure.com", "myadmin@mydemoserver", {your_password}, {your_database}, 3306);
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
> [PowerShell を使用して Azure Database for MySQL を設計する](tutorial-design-database-using-powershell.md)
