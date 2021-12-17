---
title: Microsoft Sentinel SAP ソリューションのデプロイのトラブルシューティング | Microsoft Docs
description: Microsoft Sentinel SAP ソリューションのデプロイで発生する可能性がある、特定の問題のトラブルシューティングを行う方法について説明します。
author: batamig
ms.author: bagold
ms.topic: troubleshooting
ms.custom: mvc, ignite-fall-2021
ms.date: 11/09/2021
ms.openlocfilehash: 0a833292784f62c16a8b0394ac10e602fa054a2c
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132711961"
---
# <a name="troubleshooting-your-microsoft-sentinel-sap-solution-deployment"></a>Microsoft Azure Sentinel SAP ソリューションのデプロイのトラブルシューティング

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

## <a name="useful-docker-commands"></a>便利な Docker コマンド

SAP データ コネクタのトラブルシューティングを行うときに、次のコマンドが役に立つ場合があります。

|機能  |コマンド  |
|---------|---------|
|**Docker コンテナーを停止する**     |  `docker stop sapcon-[SID]`       |
|**Docker コンテナーを起動する**     |`docker start sapcon-[SID]`         |
|**Docker システム ログを表示する**     |  `docker logs -f sapcon-[SID]`       |
|**Docker コンテナーを入力する**     |   `docker exec -it sapcon-[SID] bash`      |
|     |         |

詳細については、[Docker CLI のドキュメント](https://docs.docker.com/engine/reference/commandline/docker/)をご覧ください。

## <a name="review-system-logs"></a>システム ログを確認する

[データ コネクタをインストールまたはリセット](#reset-the-sap-data-connector)した後に、システム ログを確認することを強くお勧めします。

次を実行します。

```bash
docker logs -f sapcon-[SID]
```

## <a name="enable-debug-mode-printing"></a>デバッグ モード印刷を有効にする

**デバッグ モード印刷を有効にするには**:

1. 次のファイルを **sapcon/[SID]** ディレクトリにコピーし、`loggingconfig.yaml` に名前を変更します: https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/template/loggingconfig_DEV.yaml

1. [SAP データ コネクタをリセットします](#reset-the-sap-data-connector)。

たとえば、SID `A4H` の場合:

```bash
wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/template/loggingconfig_DEV.yaml
              cp loggingconfig.yaml ~/sapcon/A4H
              docker restart sapcon-A4H
```

**デバッグ モードの印刷を再び無効にするには、以下を実行します。**

```bash
mv loggingconfig.yaml loggingconfig.old
ls
docker restart sapcon-[SID]
```

## <a name="view-all-docker-execution-logs"></a>すべての Docker 実行ログを表示する

Microsoft Sentinel SAP データ コネクタのデプロイのすべての Docker 実行ログを表示するには、次のいずれかのコマンドを実行します。

```bash
docker exec -it sapcon-[SID] bash && cd /sapcon-app/sapcon/logs
```

または

```bash
docker exec –it sapcon-[SID] cat /sapcon-app/sapcon/logs/[FILE_LOGNAME]
```

次のような出力が表示されます。

```bash
Logs directory:
root@644c46cd82a9:/sapcon-app# ls sapcon/logs/ -l
total 508
-rwxr-xr-x 1 root root      0 Mar 12 09:22 ' __init__.py'
-rw-r--r-- 1 root root    282 Mar 12 16:01  ABAPAppLog.log
-rw-r--r-- 1 root root   1056 Mar 12 16:01  ABAPAuditLog.log
-rw-r--r-- 1 root root    465 Mar 12 16:01  ABAPCRLog.log
-rw-r--r-- 1 root root    515 Mar 12 16:01  ABAPChangeDocsLog.log
-rw-r--r-- 1 root root    282 Mar 12 16:01  ABAPJobLog.log
-rw-r--r-- 1 root root    480 Mar 12 16:01  ABAPSpoolLog.log
-rw-r--r-- 1 root root    525 Mar 12 16:01  ABAPSpoolOutputLog.log
-rw-r--r-- 1 root root      0 Mar 12 15:51  ABAPTableDataLog.log
-rw-r--r-- 1 root root    495 Mar 12 16:01  ABAPWorkflowLog.log
-rw-r--r-- 1 root root 465311 Mar 14 06:54  API.log # view this log to see submits of data into Microsoft Sentinel
-rw-r--r-- 1 root root      0 Mar 12 15:51  LogsDeltaManager.log
-rw-r--r-- 1 root root      0 Mar 12 15:51  PersistenceManager.log
-rw-r--r-- 1 root root   4830 Mar 12 16:01  RFC.log
-rw-r--r-- 1 root root   5595 Mar 12 16:03  SystemAdmin.log
```

ログをホスト オペレーティング システムにコピーするには、次を実行します。

```bash
docker cp sapcon-[SID]:/sapcon-app/sapcon/logs /directory
```

次に例を示します。

```bash
docker cp sapcon-A4H:/sapcon-app/sapcon/logs /tmp/sapcon-logs-extract
```

## <a name="review-and-update-the-sap-data-connector-configuration"></a>SAP データ コネクタの構成を確認して更新する

SAP データ コネクタの構成ファイルを確認し、手動で更新する場合は、次の手順を実行します。

1. VM のユーザーのホーム ディレクトリで **~/sapcon/[SID]/systemconfig.ini** ファイルを開きます。
1. 必要に応じて構成を更新し、コンテナーを再起動します。

    ```bash
    docker restart sapcon-[SID]
    ```

## <a name="reset-the-sap-data-connector"></a>SAP データ コネクタをリセットする

次の手順では、コネクタをリセットし、過去 24 時間の SAP ログを再取り込みします。

1.  コネクタを停止します。 次を実行します。

    ```bash
    docker stop sapcon-[SID]
    ```

1.  **sapcon/[SID]** ディレクトリから、**metadata.db** ファイルを削除します。 次を実行します。

    ```bash
    cd ~/sapcon/<SID>
    ls
    mv metadata.db metadata.old
    ```

    > [!NOTE]
    > **metadata.db** ファイルには各ログの最後のタイムスタンプが含まれ、重複を防止します。

1. コネクタを再度開始します。 次を実行します。

    ```bash
    docker start sapcon-[SID]
    ```

作業を終えたら、必ず[システム ログを確認します](#review-system-logs)。



## <a name="common-issues"></a>一般的な問題

SAP データ コネクタとセキュリティ コンテンツの両方をデプロイすると、次のエラーまたは問題が発生する可能性があります。

### <a name="corrupt-or-missing-sap-sdk-file"></a>破損した、または見つからない SAP SDK ファイル

このエラーは、PyRfc でコネクタの起動に失敗した場合、または zip 関連のエラー メッセージが表示された場合に発生することがあります。

1. SAP SDK を再インストールします。
1. 正しい Linux 64 ビット バージョンを使用していることを確認します。 現時点において、リリース ファイル名は、**nwrfc750P_8-70002752.zip** です。

データ コネクタを手動でインストールした場合は、SDK ファイルを Docker コンテナーにコピーしたことを確認してください。

次を実行します。

```bash
Docker cp SDK by running docker cp nwrfc750P_8-70002752.zip /sapcon-app/inst/
```

### <a name="abap-runtime-errors-appear-on-a-large-system"></a>大規模なシステムに ABAP ランタイム エラーが表示される

大規模なシステムに ABAPランタイム エラーが表示される場合は、小さなチャンク サイズを設定してみてください。

1. **sapcon/SID/systemconfig.ini** ファイルを編集し、`timechunk = 5` を定義します。
2. [SAP データ コネクタをリセットします](#reset-the-sap-data-connector)。

> [!NOTE]
> **timechunk** のサイズは、分単位で定義されます。

### <a name="empty-or-no-audit-log-retrieved-with-no-special-error-messages"></a>監査ログが空か、監査ログが取得されず、特別なエラー メッセージも表示されない

1. SAP で監査ログが有効になっているか確認します。
1. **SM19** または **RSAU_CONFIG** トランザクションを確認します。
1. 必要に応じてイベントを有効にします。
1. コネクタ ログに特別なエラーが表示されずに、SAP **SM20** または **RSAU_READ_LOG** にメッセージが到着し、存在していることを確認します。


### <a name="incorrect-microsoft-sentinel-workspace-id-or-key"></a>Microsoft Sentinel ワークスペース ID またはキーが正しくない

[デプロイ スクリプト](sap-deploy-solution.md#create-a-key-vault-for-your-sap-credentials)に間違ったワークスペース ID またはキーを入力した場合は、Azure Key Vault に格納されている資格情報を更新します。

Azure KeyVault で資格情報を確認した後、コンテナーを再起動します。

```bash
docker restart sapcon-[SID]
```

### <a name="incorrect-sap-abap-user-credentials-in-a-fixed-configuration"></a>固定構成で SAP ABAP ユーザー資格情報が正しくない

固定構成では、パスワードが **systemconfig.ini** 構成ファイルに直接格納されます。

資格情報が正しくない場合は、資格情報を確認します。

base64 暗号化を使用して、ユーザーとパスワードを暗号化します。 https://www.base64encode.org/ のようなオンライン暗号化ツールを使用して、資格情報を暗号化することができます。

### <a name="incorrect-sap-abap-user-credentials-in-key-vault"></a>キー コンテナーの SAP ABAP ユーザー資格情報が正しくない

資格情報を確認し、必要に応じて修正して、Azure Key Vault の **ABAPUSER** と **ABAPPASS** の値に正しい値を適用します。

次に、コンテナーを再起動します。

```bash
docker restart sapcon-[SID]
```


### <a name="missing-abap-sap-user-permissions"></a>ABAP (SAP ユーザー) アクセス許可がない

**..バックエンド RFC 承認がありません...** のようなエラー メッセージが表示される場合には、SAP の承認とロールが正しく適用されていません。

1. [変更要求](sap-solution-detailed-requirements.md#required-sap-log-change-requests)転送の一部として **MSFTSEN/SENTINEL_CONNECTOR** ロールがインポートされ、コネクタ ユーザーに適用されていることを確認してください。

1. SAP トランザクション PFCG を使用して、ロールの生成とユーザー比較プロセスを実行します。

### <a name="missing-data-in-your-workbooks-or-alerts"></a>ブックまたはアラートにデータが見つからない

Microsoft Sentinel ブックまたはアラートにデータがない場合は、**Auditlog** ポリシーが SAP 側で正しく有効になっており、ログ ファイルにエラーがないことを確認します。 

この手順には、**RSAU_CONFIG_LOG** トランザクションを使用します。


### <a name="missing-sap-change-request"></a>SAP 変更要求が見つからない

必要な [SAP 変更要求](sap-solution-detailed-requirements.md#required-sap-log-change-requests)が見つからないというエラーが表示される場合は、システムに正しい SAP 変更要求がインポートされていることを確認します。

詳細については、「[SAP システムを構成する](sap-deploy-solution.md#configure-your-sap-system)」を参照してください。

### <a name="network-connectivity-issues"></a>ネットワーク接続の問題

SAP 環境または Microsoft Sentinel へのネットワーク接続の問題が発生している場合は、ネットワーク接続を確認し、データが予想どおり流れていることを確認します。

次のような一般的な問題があります。

- Docker コンテナーと SAP ホストの間のファイアウォールによってトラフィックがブロックされている可能性があります。 SAP ホストでは、開いている必要がある **32xx**、**5xx13**、**33xx** (**xx** は SAP インスタンス番号) の TCP ポートを介して通信を受信します。

- SAP ホストから Microsoft Container Registry または Azure への送信通信にはプロキシ構成が必要です。 これは通常、インストールに影響を与え、`HTTP_PROXY` および `HTTPS_PROXY` 環境変数を構成する必要があります。 Docker `create` / `run` コマンドに `-e` フラグを追加して、コンテナーの作成時に環境変数を Docker コンテナーに取り込むこともできます。

### <a name="other-unexpected-issues"></a>その他の予期せぬ問題

この記事に記載されていない予期せぬ問題がある場合は、次のステップを試してください。

- [コネクタをリセットしてログを再度読み込みます](#reset-the-sap-data-connector)。
- [コネクタを最新バージョンにアップグレードします](sap-deploy-solution.md#update-your-sap-data-connector)。

> [!TIP]
> 大きな構成変更を行った後には、コネクタをリセットし、アップグレードが最新であることを確認することをお勧めします。

### <a name="retrieving-an-audit-log-fails-with-warnings"></a>監査ログを取得しようとすると警告が出て失敗する

[必要な変更要求](sap-solution-detailed-requirements.md#required-sap-log-change-requests)がデプロイされていない状態か、古い、またはパッチが未適用のバージョンで監査ログを取得しようとして、警告が出てプロセスが失敗した場合は、次のいずれかの方法を使用して SAP 監査ログを取得できることを確認してください。

- 古いバージョンで *XAL* という互換性モードを使用する
- 最近修正プログラムが適用されていないバージョンを使用する
- 必要な変更要求がインストールされていない状態で

システムは必要に応じて互換性モードに自動的に切り替わりますが、手動で切り替える必要がある場合があります。 互換性モードに手動で切り替えるには:

1. **sapcon/SID** ディレクトリで、**systemconfig.ini** ファイルを編集します

1. 次のように定義します: `auditlogforcexal = True`

1. Docker コンテナーを再起動します。

    ```bash
    docker restart sapcon-[SID]
    ```

### <a name="sapcontrol-or-java-subsystems-unable-to-connect"></a>SAPCONTROL または JAVA サブシステムが接続できない

OS ユーザーが有効であり、ターゲットの SAP システムで次のコマンドを実行できるかどうか確認します。

```bash
sapcontrol -nr <SID> -function GetSystemInstanceList
```

### <a name="sapcontrol-or-java-subsystem-fails-with-timezone-related-error-message"></a>SAPCONTROL または JAVA サブシステムがタイムゾーン関連のエラー メッセージで失敗する

SAPCONTROL または JAVA サブシステムが、「**SAP サーバー 'Etc/NZST' の構成とネットワーク アクセスを確認してください**」のようなタイムゾーン関連のエラー メッセージが表示されて失敗する場合には、標準のタイムゾーン コードを使用しているかどうか確認します。

たとえば、`javatz = GMT+12` または `abaptz = GMT-3**` を使用します。


### <a name="unable-to-import-the-change-request-transports-to-sap"></a>変更要求転送を SAP にインポートできない

[必要な SAP ログ変更要求](sap-solution-detailed-requirements.md#required-sap-log-change-requests)をインポートできず、無効なコンポーネント バージョンに関するエラーが表示される場合には、変更要求をインポートする際に `ignore invalid component version` を追加します。

### <a name="audit-log-data-not-ingested-past-initial-load"></a>初期読み込み後に監査ログ データが取り込まれない

**RSAU_READ_LOAD** または **SM200** のいずれかのトランザクションに表示される SAP 監査ログ データが、初期読み込み後に Microsoft Sentinel に取り込まれない場合は、SAP システムと SAP ホスト オペレーティング システムの構成が間違っている可能性があります。

- 初期読み込みは、SAP データ コネクタの新規インストール後、または **metadata.db** ファイルが削除された後に取り込まれます。
- 誤った構成の例として、**STZAC** トランザクション内で SAP システムのタイムゾーンが **CET** に設定されているのに、SAP ホスト オペレーティング システムのタイム ゾーンが **UTC** に設定されている場合があります。

構成の誤りを確認するには、トランザクション **SE38** で **RSDBTIME** レポートを実行します。 SAP システムと SAP ホスト オペレーティング システムの間で不一致が見つかった場合:

1. Docker コンテナーを停止します。 実行

    ```bash
    docker stop sapcon-[SID]
    ```

1.  **sapcon/[SID]** ディレクトリから、**metadata.db** ファイルを削除します。 次を実行します。

    ```bash
    rm ~/sapcon/[SID]/metadata.db
    ```

1. SAP システムと SAP ホスト オペレーティング システムを、同じタイム ゾーンなどの一致する設定に更新します。 詳細については、[SAP コミュニティの Wiki](https://wiki.scn.sap.com/wiki/display/Basis/Time+zone+settings%2C+SAP+vs.+OS+level) を参照してください。

1. もう一度コンテナーを起動します。 次を実行します。

    ```bash
    docker start sapcon-[SID]
    ```

## <a name="next-steps"></a>次のステップ

詳細については、次を参照してください。

- [SAP の継続的な脅威監視をデプロイする (パブリック プレビュー)](sap-deploy-solution.md)
- [Microsoft Sentinel SAP ソリューションのログ リファレンス (パブリック プレビュー)](sap-solution-log-reference.md)
- [SNC を使用して Microsoft Sentinel SAP データ コネクタをデプロイする](sap-solution-deploy-snc.md)
- [エキスパートの構成オプション、オンプレミス デプロイ、SAPControl のログ ソース](sap-solution-deploy-alternate.md)
- [Microsoft Sentinel SAP ソリューション: セキュリティ コンテンツ リファレンス (パブリック プレビュー)](sap-solution-security-content.md)
- [Microsoft Sentinel SAP ソリューションの詳細な SAP 要件 (パブリック プレビュー)](sap-solution-detailed-requirements.md)
