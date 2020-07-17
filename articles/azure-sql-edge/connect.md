---
title: Azure SQL Edge の接続とクエリ (プレビュー)
description: Azure SQL Edge の接続とクエリ (プレビュー) について説明します。
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 17bb5afebe0335bcdcf40298490f94999c04a621
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83593951"
---
# <a name="connect-and-query-azure-sql-edge-preview"></a>Azure SQL Edge の接続とクエリ (プレビュー)

Azure SQL Edge コンテナーをデプロイした後、次のいずれかの場所から SQL データベース エンジンに接続できます。

- コンテナー内
- 同じホストで実行されている別の Docker コンテナーから。
- ホスト マシンから
- ネットワーク上の他のクライアント コンピューターから。

## <a name="tools-to-connect-to-azure-sql-edge"></a>Azure SQL Edge に接続するためのツール

Azure SQL Edge インスタンスへの接続は、次に示す一般的なツールから行うことができます。

* [sqlcmd](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) - sqlcmd クライアント ツールは、Azure SQL Edge コンテナー イメージに既に含まれています。 対話型の Bash シェルで実行中のコンテナーにアタッチする場合は、ツールをローカルで実行できます。
* [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms)
* [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/download-azure-data-studio)
* [Visual Studio Code](https://docs.microsoft.com/sql/visual-studio-code/sql-server-develop-use-vscode)

ネットワーク マシンから Azure SQL Edge データベース エンジンに接続するには、次のものが必要です。

- *ホスト マシンの IP アドレスまたはネットワーク名* - これは、Azure SQL Edge コンテナーが実行されているホスト マシンです。
- *Azure SQL Edge コンテナー ホストのポート マッピング* - これは、Docker コンテナー ポートをホスト上のポートにマップするポートです。 コンテナー内では、SQL Edge は常にポート 1433 にマップされます。 これは、Azure SQL Edge のデプロイの一部として変更できます。 ポート番号を変更するには、Azure IoT Edge の SQL Edge モジュールの [コンテナーの作成オプション] を更新します。 次の例では、コンテナーのポート 1433 がホストのポート 1600 にマップされています。

    ```JSON
    {
        "PortBindings": {
          "1433/tcp": [
            {
              "HostPort": "1600"
            }
          ]
        }
    }
    ```

- *SQL Edge インスタンスの SA パスワード* - これは、SQL Edge のデプロイ中に **SA_PASSWORD** 環境変数に指定された値です。

## <a name="connecting-to-the-database-engine-from-within-the-container"></a>コンテナー内からデータベース エンジンへの接続

[SQL Server コマンドライン ツール](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools)は、Azure SQL Edge コンテナー イメージに含まれています。 対話型のコマンド プロンプトを使用してコンテナーにアタッチすると、ツールをローカルで実行できます。

1. 実行中のコンテナー内で対話型の Bash シェルを開始するには、`docker exec -it` コマンドを使用します。 次の例では、`e69e056c702d` はコンテナー ID です。

    ```bash
    docker exec -it <Azure SQL Edge container id or name> /bin/bash
    ```

    > [!TIP]
    > 常にコンテナー ID 全体を指定する必要はありません。一意に識別するのに十分な文字を指定するだけでかまいません。 したがって、この例では、完全な ID ではなく `e6` または `e69` を使用すれば十分な可能性があります。

2. コンテナー内では sqlcmd とローカル接続してください。 既定では sqlcmd はパスにないため、完全なパスを指定する必要があります。

    ```bash
    /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P '<YourPassword>'
    ```

3. sqlcmd が終了したら、「`exit`」と入力します。

4. 対話型コマンド プロンプトでの操作が完了したら、「`exit`」と入力します。 コンテナーは、対話型の Bash シェルを終了した後も引き続き実行されます。

## <a name="connect-to-sql-edge-from-another-container-on-the-same-host"></a>同じホスト上の別のコンテナーから SQL Edge に接続する

同じホストで実行されている 2 つのコンテナーは同じ Docker ネットワーク上にあるため、コンテナー名とサービスのポート アドレスを使用して簡単にアクセスできます。 たとえば、同じホスト上の別の Python モジュール (コンテナー) から SQL Edge インスタンスに接続している場合は、次のような接続文字列を使用できます。 次の例では、SQL Edge が既定のポートでリッスンするように構成されていると想定しています。

```python

import pyodbc
server = 'MySQLEdgeContainer' # Replace this with the actual name of your SQL Edge Docker container
username = 'sa' # SQL Server username
password = 'MyStrongestP@ssword' # Replace this with the actual SA password from your deployment
database = 'MyEdgeDatabase' # Replace this with the actual database name from your deployment. If you do not have a database created, you can use Master database.
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
conn = pyodbc.connect(db_connection_string, autocommit=True)

```

## <a name="connect-to-sql-edge-from-another-network-machine"></a>別のネットワーク マシンから SQL Edge に接続する

ネットワーク上の別のマシンから SQL Edge インスタンスに接続するには、Docker ホストの IP アドレスと、SQL Edge コンテナーがマップされているホストポートを使用する必要があります。 たとえば、Docker ホストの IP アドレスが *xxx.xxx.xxx.xxx" であり、SQL Edge コンテナーがホストポート *1600* にマップされている場合、SQL Edge インスタンスのサーバー アドレスは **xxx.xxx.xxx.xxx,1600** になります。 更新された Python スクリプトは次のようになります

```python

import pyodbc
server = 'xxx.xxx.xxx.xxx,1600' # Replace this with the actual name of your SQL Edge Docker container
username = 'sa' # SQL Server username
password = 'MyStrongestP@ssword' # Replace this with the actual SA password from your deployment
database = 'MyEdgeDatabase' # Replace this with the actual database name from your deployment. If you do not have a database created, you can use Master database.
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
conn = pyodbc.connect(db_connection_string, autocommit=True)

```

Windows マシン上で実行されている SQL Server Management Studio を使用して SQL Edge のインスタンスに接続するには、[SQL Server Management Studio](https://docs.microsoft.com/sql/linux/sql-server-linux-manage-ssms) に関するページを参照してください。

Windows、Mac、または Linux マシン上で Visual Studio Code を使用して SQL Edge のインスタンスに接続するには、[Visual Studio Code](https://docs.microsoft.com/sql/visual-studio-code/sql-server-develop-use-vscode) に関するページを参照してください。

Windows、Mac、または Linux マシン上で Azure Data Studio を使用して SQL Edge のインスタンスに接続するには、[Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-server) に関するページを参照してください。

## <a name="see-also"></a>関連項目

[接続とクエリ](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-docker#connect-and-query)

[Linux に SQL Server ツールをインストールする](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools)
