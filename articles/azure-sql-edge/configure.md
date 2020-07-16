---
title: Azure SQL Edge (プレビュー) の構成
description: Azure SQL Edge (プレビュー) の構成について説明します。
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: c38bb6100665cc9456b66608660bdca520b934c6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84636242"
---
# <a name="configure-azure-sql-edge-preview"></a>Azure SQL Edge (プレビュー) の構成

Azure SQL Edge では、次の 2 つのオプションのいずれかを使用した構成がサポートされています。

- 環境変数
- /var/opt/mssql フォルダーに配置される mssql.conf フォルダー

> [!NOTE]
> 環境変数を設定すると、mssql.conf ファイルに指定した設定がオーバーライドされます。

## <a name="configure-by-using-environment-variables"></a>環境変数を使用して構成する

Azure SQL Edge では、SQL Edge コンテナーの構成に使用できるいくつかの異なる環境変数が公開されています。 これらの環境変数は、SQL Server on Linux で使用できる環境変数のサブセットです。 SQL Server on Linux 環境変数の詳細については、[環境変数](/sql/linux/sql-server-linux-configure-environment-variables/)に関する記事をご覧ください。

次の SQL Server on Linux 環境変数は、Azure SQL Edge ではサポートされていません。 この環境変数を定義しても、コンテナーの初期化中に無視されます。

| 環境変数 | 説明 |
|-----|-----|
| **MSSQL_ENABLE_HADR** | 可用性グループを有効にします。 たとえば、**1** は有効、**0** は無効です。 |

> [!IMPORTANT]
> SQL Edge の **MSSQL_PID** 環境変数には、有効な値として **Premium** と **Developer** のみを指定できます。 Azure SQL Edge では、プロダクト キーを使用した初期化はサポートされていません。

> [!NOTE]
> Azure SQL Edge 用の [Microsoft ソフトウェア ライセンス条項](https://go.microsoft.com/fwlink/?linkid=2128283)をダウンロードしてください。

### <a name="specify-the-environment-variables"></a>環境変数を指定する

[Azure portal](deploy-portal.md) を使用してサービスをデプロイする場合は、SQL Edge 用の環境変数を指定します。 それらは、モジュールのデプロイの **[環境変数]** セクションに追加することも、 **[コンテナー作成オプション]** の一部として追加することもできます。

**[環境変数]** に値を追加します。

![環境変数リストを使用して設定する](media/configure/set-environment-variables.png)

**[コンテナー作成オプション]** に値を追加します。

![コンテナー作成オプションを使用して設定する](media/configure/set-environment-variables-using-create-options.png)

## <a name="configure-by-using-an-mssqlconf-file"></a>mssql.conf ファイルを使用して構成する

Azure SQL Edge には、SQL Server on Linux に含まれているような [mssql-conf 構成ユーティリティ](/sql/linux/sql-server-linux-configure-mssql-conf/)は含まれていません。 mssql.conf ファイルを手動で構成し、SQL Edge モジュールの /var/opt/mssql/ フォルダーにマップされている永続的なストレージ ドライブに配置する必要があります。 Azure Marketplace から SQL Edge をデプロイする場合、このマッピングは **[コンテナー作成オプション]** で **[マウント]** オプションとして指定されます。

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

|オプション|説明|
|:---|:---|
|**カスタマー フィードバック** | SQL Server によって Microsoft にフィードバックが送信されるかどうかを選択します。 |
|**データベース メール プロファイル** | SQL Server on Linux の既定のデータベース メール プロファイルを設定します。 |
|**高可用性** | 可用性グループを有効にします。 |
|**Microsoft 分散トランザクション コーディネーター** | Linux で MSDTC の構成とトラブルシューティングを行います。 SQL Edge では、分散トランザクションに関連する追加の構成オプションはサポートされていません。 このような追加の構成オプションの詳細については、「[MSDTC の構成](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#msdtc)」を参照してください。 |
|**MLServices の EULA** | Azure Machine Learning パッケージ用の R および Python の EULA に同意します。 これは SQL Server 2019 のみに適用されます。|
|**outboundnetworkaccess** |[Machine Learning Services](/sql/linux/sql-server-linux-setup-machine-learning/) の R、Python、および Java 拡張機能の送信ネットワーク アクセスを有効にします。|

SQL Edge では次の例の mssql.conf ファイルが機能します。 mssql.conf ファイルの形式の詳細については、「[mssql.conf の書式](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#mssql-conf-format)」をご覧ください。

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

## <a name="next-steps"></a>次のステップ

- [Azure SQL Edge に接続する](connect.md)
- [SQL Edge を使用してエンドツーエンドの IoT ソリューションを構築する](tutorial-deploy-azure-resources.md)
