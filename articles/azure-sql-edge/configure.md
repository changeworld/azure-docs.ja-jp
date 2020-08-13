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
ms.date: 07/28/2020
ms.openlocfilehash: 0cb2eed0895c10f649facaa184a5f9f9ea158aa5
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/04/2020
ms.locfileid: "87551984"
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
|**ML サービスの EULA** | Azure Machine Learning パッケージ用の R および Python の EULA に同意します。 これは SQL Server 2019 のみに適用されます。|
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

## <a name="run-azure-sql-edge-as-non-root-user"></a>非ルート ユーザーとして Azure SQL Edge を実行する

Azure SQL Edge CTP 2.2 より、ルート以外のユーザーまたはグループを使用して SQL Edge コンテナーを実行できます。 Azure Marketplace を通してデプロイされると、異なるユーザーやグループが指定されていない限り、SQL Edge コンテナーは mssql (非ルート) ユーザーとして起動されます。 デプロイ時に異なる非ルート ユーザーを指定するには、コンテナー作成オプションの下に、キーと値のペア `*"User": "<name|uid>[:<group|gid>]"*` を追加します。 下の例では、SQL Edge はユーザー `*IoTAdmin*` として起動するように構成されています。

```json
{
    ..
    ..
    ..
    "User": "IoTAdmin",
    "Env": [
        "MSSQL_AGENT_ENABLED=TRUE",
        "ClientTransportType=AMQP_TCP_Only",
        "MSSQL_PID=Premium"
    ]
}
```

マウントされたボリューム上にある DB ファイルへのアクセスを非ルート ユーザーに許可するには、コンテナーを実行するのに使用するユーザーまたはグループが、永続的なファイル ストレージに対する読み取りおよび書き込みアクセス許可を持っているようにします。 下の例では、ファイルの所有者として、user_id が 10001 である非ルート ユーザーを設定しています。 

```bash
chown -R 10001:0 <database file dir>
```

### <a name="upgrading-from-earlier-ctp-releases"></a>以前の CTP リリースからのアップグレード

以前の CTP の Azure SQL Edge は、ルート ユーザーとして実行するように構成されていました。 以前の CTP からアップグレードする場合は、以下のオプションを使用できます

- 引き続きルート ユーザーを使用する - ルート ユーザーを使用し続けるには、コンテナー作成オプションの下に、キーと値のペア `*"User": "0:0"*` を追加します。
- 既定の mssql ユーザーを使用する - 既定の mssql ユーザーを使用するには、以下の手順に従います
  - Docker ホストに mssql という名前のユーザーを追加します。 下の例では、ID が 10001 のユーザー mssql を追加しています。 このユーザーは、ルート グループにも追加されます。
    ```bash
    sudo useradd -M -s /bin/bash -u 10001 -g 0 mssql
    ```
  - データベース ファイルがあるディレクトリまたはマウント ボリュームに対するアクセス許可を変更します 
    ```bash
    sudo chgrp -R 0 /var/lib/docker/volumes/kafka_sqldata/
    sudo chmod -R g=u /var/lib/docker/volumes/kafka_sqldata/
    ```
- 異なる非ルート ユーザー アカウントを使用する - 異なる非ルート ユーザー アカウントを使用するには
  - コンテナー作成オプションの下にキーと値のペア `*"User": "user_name | user_id*` を追加することを指定するように、コンテナー作成オプションを更新します。 user_name または user_id は、実際の Docker ホストの user_name または user_id に置き換えてください。 
  - ディレクトリまたはマウント ボリュームに対するアクセス許可を変更します。



## <a name="next-steps"></a>次のステップ

- [Azure SQL Edge に接続する](connect.md)
- [SQL Edge を使用してエンドツーエンドの IoT ソリューションを構築する](tutorial-deploy-azure-resources.md)
