---
title: Azure SQL Edge (プレビュー) の構成
description: Azure SQL Edge (プレビュー) を構成する方法について説明します
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 5dcdd1604674ca56fb7a646d4c571d63bd2c0e3e
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594011"
---
# <a name="configure-azure-sql-edge-preview"></a>Azure SQL Edge (プレビュー) の構成

Azure SQL Edge では、次の 2 つのオプションのいずれかを使用した構成がサポートされています。

- 環境変数を使用する。
- /var/opt/mssql フォルダーに配置された mssql.conf ファイルを使用する。

> [!NOTE]
> 環境変数を設定すると、mssql.conf ファイルで指定した設定がオーバーライドされます。

## <a name="configure-using-environment-variables"></a>環境変数を使用した構成

Azure SQL Edge では、SQL Edge コンテナーの構成に使用できるいくつかの異なる環境変数が公開されています。 これらの環境変数は、SQL Server on Linux で使用できる環境変数のサブセットです。 SQL Server on Linux 環境変数の詳細については、[環境変数](/sql/linux/sql-server-linux-configure-environment-variables/)に関する記事をご覧ください。

次の SQL Server on Linux 環境変数は、Azure SQL Edge ではサポートされていません。 これらの環境変数を定義しても、コンテナーの初期化中に無視されます。

| 環境変数 | 説明 |
|-----|-----|
| **MSSQL_ENABLE_HADR** | 可用性グループを有効にします。 たとえば、'1' は有効、'0' は無効です |

> [!IMPORTANT]
> SQL Edge の *MSSQL_PID* 環境変数には、有効な値として **Premium** と **Developer** のみを指定できます。 Azure SQL Edge では、プロダクト キーを使用した初期化はサポートされていません。

> [!NOTE]
> Azure SQL Edge エンド ユーザー使用許諾契約書をダウンロードする場合は、[エンド ユーザー使用許諾契約書](https://go.microsoft.com/fwlink/?linkid=2128283)を参照してください。

### <a name="specifying-the-environment-variables"></a>環境変数の指定

SQL Edge の環境変数は、[Azure portal](deploy-portal.md) を使用して Azure SQL Edge をデプロイするときに指定できます。 これは、モジュールのデプロイの "環境変数" セクションで追加することも、以下に説明するコンテナー作成オプションの一部として追加することもできます。

"*環境変数オプションを使用した設定*"

![環境変数オプションを使用した設定](media/configure/set-environment-variables.png)

"*コンテナーの作成オプションを使用した設定*"

![コンテナーの作成オプションを使用した設定](media/configure/set-environment-variables-using-create-options.png)

## <a name="configure-using-mssqlconf-file"></a>mssql.conf ファイルを使用した構成

Azure SQL Edge には、SQL Server on Linux のように [mssql-conf 構成ユーティリティ](/sql/linux/sql-server-linux-configure-mssql-conf/)は含まれていません。そのため、mssql.conf ファイルは手動で構成し、SQL Edge モジュールの /var/opt/mssql/ フォルダーにマップされる永続的なストレージ ドライブに配置する必要があります。 Azure Marketplace から SQL Edge をデプロイする場合、このマッピングはコンテナーの作成オプションで **Mounts" オプションとして指定されます

```json
    {
        "Mounts": [
          {
            "Type": "volume",
            "Source": "sqlvolume",
            "Target": "/var/opt/mssql"
          }
        ]
      }
    }
```

次の mssql.conf のオプションは、SQL Edge には適用されません。
</br></br>
|オプション|説明|
|:---|:---|
|**カスタマー フィードバック** | SQL Server が Microsoft にフィードバックを送信するかどうかを選択します。 |
|**[データベース メール プロファイル]** | SQL Server on Linux の既定のデータベース メール プロファイルを設定します。 |
|**高可用性** | 可用性グループを有効にします。 |
|**Microsoft 分散トランザクション コーディネーター** | Linux で MSDTC の構成とトラブルシューティングを行います。 SQL Edge では、分散トランザクションに関連する追加の構成オプションもサポートされていません。 このような追加の構成オプションの詳細については、「[MSDTC の構成](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#msdtc)」を参照してください |
|**MLServices の EULA** | Machine Learning Services パッケージ用の R および Python の EULA に同意します。 これは SQL Server 2019 のみに適用されます。|
|**outboundnetworkaccess** |[Machine Learning Services](/sql/linux/sql-server-linux-setup-machine-learning/) の R、Python、および Java 拡張機能の送信ネットワーク アクセスを有効にします。|

SQL Edge 用に機能するサンプルの mssql.conf ファイルを次に示します。 mssql.conf ファイルの形式の詳細については、「[mssql.conf format](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#mssql-conf-format)」をご覧ください。

```ini
[EULA]
accepteula = Y

[coredump]
captureminiandfull = true
coredumptype = full

[filelocation]
defaultbackupdir = /var/opt/mssql/backup/
defaultdatadir = /var/opt/mssql/data/
defaultdumpdir = /var/opt/mssql/data/
defaultlogdir = /var/opt/mssql/log/

[language]
lcid = 1033

[memory]
memorylimitmb = 6144

[sqlagent]
errorlogfile = /var/opt/mssql/log/sqlagentlog.log
errorlogginglevel = 7

[traceflag]
traceflag0 = 3604
traceflag1 = 3605
traceflag2 = 1204
```

## <a name="next-step"></a>次のステップ

- [Azure SQL Edge に接続する](connect.md)
- [SQL Edge でのエンドツーエンドの IoT ソリューションの構築](tutorial-deploy-azure-resources.md)
