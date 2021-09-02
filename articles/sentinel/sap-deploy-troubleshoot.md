---
title: Azure Sentinel SAP ソリューションのデプロイのトラブルシューティング | Microsoft Docs
description: Azure Sentinel SAP ソリューションのデプロイで発生する可能性がある、特定の問題のトラブルシューティングについて説明します。
author: batamig
ms.author: bagold
ms.service: azure-sentinel
ms.topic: troubleshooting
ms.custom: mvc
ms.date: 07/29/2021
ms.subservice: azure-sentinel
ms.openlocfilehash: e3503b8f7464f66bd404b9b70196a017d9c058b6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778874"
---
# <a name="troubleshooting-your-azure-sentinel-sap-solution-deployment"></a>Azure Sentinel SAP ソリューションのデプロイのトラブルシューティング

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

データ コネクタをインストールまたはリセットした後に、システム ログを確認することを強くお勧めします。

次を実行します。

```bash
docker logs -f sapcon-[SID]
```
## <a name="enable-debug-mode-printing"></a>デバッグ モード印刷を有効にする

デバッグ モード印刷を有効にするには:

1. 次のファイルを **sapcon/[SID]** ディレクトリにコピーし、`loggingconfig.yaml` に名前を変更します: https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/template/loggingconfig_DEV.yaml

1. [SAP データ コネクタをリセットします](#reset-the-sap-data-connector)。

たとえば、SID A4H の場合:

```bash
wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/template/loggingconfig_DEV.y
              cp loggingconfig.yaml ~/sapcon/A4H
              docker restart sapcon-A4H
```

## <a name="view-all-docker-execution-logs"></a>すべての Docker 実行ログを表示する

Azure Sentinel SAP データ コネクタのデプロイのすべての Docker 実行ログを表示するには、次のいずれかのコマンドを実行します。

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
-rw-r--r-- 1 root root 465311 Mar 14 06:54  API.log # view this log to see submits of data into Azure Sentinel
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

次の手順は、コネクタをリセットし、過去 24 時間の SAP ログを再取り込みします。

1.  コネクタを停止します。 次を実行します。

    ```bash
    docker stop sapcon-[SID]
    ```

1.  **sapcon/[SID]** ディレクトリから、**metadata.db** ファイルを削除します。

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

これは、PyRfc でコネクタの起動に失敗した場合、または zip 関連のエラー メッセージが表示された場合に発生します。

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
1. トランザクションの **SM19** と **RASU_CONFIG** を確認してください。
1. 必要に応じてイベントを有効にします。
1. コネクタ ログに特別なエラーが表示されずに、SAP **SM20** または **RSAU_READ_LOG** にメッセージが到着し、存在していることを確認します。


### <a name="incorrect-azure-sentinel-workspace-id-or-key"></a>Azure Sentinel ワークスペース ID またはキーが正しくない

[デプロイ スクリプト](sap-deploy-solution.md#create-key-vault-for-your-sap-credentials)に間違ったワークスペース ID またはキーを入力した場合は、Azure Key Vault に格納されている資格情報を更新します。

### <a name="incorrect-sap-abap-user-credentials-in-a-fixed-configuration"></a>固定構成で SAP ABAP ユーザー資格情報が正しくない

固定構成では、パスワードが **systemconfig.ini** 構成ファイルに直接格納されます。

資格情報が正しくない場合は、資格情報を確認します。

base64 暗号化を使用して、ユーザーとパスワードを暗号化します。 オンライン暗号化ツールを使用して、これを行います (例: https://www.base64encode.org/ )。

### <a name="incorrect-sap-abap-user-credentials-in-key-vault"></a>キー コンテナーの SAP ABAP ユーザー資格情報が正しくない

資格情報を確認し、必要に応じて修正します。

Azure Key Vault の **ABAPUSER** と **ABAPPASS** の値に正しい値を適用します。



### <a name="missing-abap-sap-user-permissions"></a>ABAP (SAP ユーザー) アクセス許可がない

**..バックエンド RFC 承認がありません...** のようなエラー メッセージが表示される場合には、SAP の承認とロールが正しく適用されていません。

1. [変更要求](sap-solution-detailed-requirements.md#required-sap-log-change-requests)転送の一部として **MSFTSEN/SENTINEL_CONNECTOR** ロールがインポートされ、コネクタ ユーザーに適用されていることを確認してください。

1. SAP トランザクション PFCG を使用して、ロールの生成とユーザー比較プロセスを実行します。

### <a name="missing-data-in-your-workbooks-or-alerts"></a>ブックまたはアラートにデータが見つからない

Azure Sentinel ブックまたはアラートにデータがない場合は、**監査ログ** ポリシーが SAP 側で正しく有効になっており、ログ ファイルにエラーがないことを確認します。 

この手順には、**RSAU_CONFIG_LOG** トランザクションを使用します。


### <a name="missing-sap-change-request"></a>SAP 変更要求が見つからない

必要な [SAP 変更要求](sap-solution-detailed-requirements.md#required-sap-log-change-requests)が見つからないというエラーが表示される場合は、システムに正しい SAP 変更要求がインポートされていることを確認します。

詳細については、「[SAP システムを構成する](sap-deploy-solution.md#configure-your-sap-system)」を参照してください。

### <a name="network-connectivity-issues"></a>ネットワーク接続の問題

SAP 環境または Azure Sentinel へのネットワーク接続の問題が発生している場合は、ネットワーク接続を確認し、データが予想通り流れているか確認します。

### <a name="other-unexpected-issues"></a>その他の予期せぬ問題

この記事に記載されていない予期せぬ問題がある場合は、次を試してください。

- [コネクタをリセットしてログを再度読み込みます](#reset-the-sap-data-connector)。
- [コネクタを最新バージョンにアップグレードします](sap-deploy-solution.md#update-your-sap-data-connector)。

> [!TIP]
> 大きな構成変更を行った後には、コネクタをリセットし、アップグレードが最新であることを確認することをお勧めします。

### <a name="retrieving-an-audit-log-fails-with-warnings"></a>監査ログを取得しようとすると警告が出て失敗する

[必要な変更要求](sap-solution-detailed-requirements.md#required-sap-log-change-requests)がデプロイされていない状態か、または古い/パッチが未適用のバージョンで監査ログを取得しようとして、警告が出てプロセスが失敗した場合は、次のいずれかの方法を使用して SAP 監査ログを取得できるか確認してください。

- 古いバージョンで *XAL* という互換性モードを使用する
- 最近修正プログラムが適用されていないバージョンを使用する
- 必要な変更要求がインストールされていない状態で

システムは必要に応じて互換性モードに自動的に切り替わりますが、手動で切り替える必要がある場合があります。 互換性モードに手動で切り替えるには:

1. **sapcon/SID** ディレクトリで、**systemconfig.ini** ファイルを編集します
1. 次のように定義します: `auditlogforcexal = True`

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


## <a name="next-steps"></a>次のステップ

詳細については、次を参照してください。

- [SAP の継続的な脅威監視をデプロイする (パブリック プレビュー)](sap-deploy-solution.md)
- [Azure Sentinel SAP ソリューションのログ リファレンス (パブリック プレビュー)](sap-solution-log-reference.md)
- [エキスパートの構成オプション、オンプレミス デプロイ、SAPControl のログ ソース](sap-solution-deploy-alternate.md)
- [Azure Sentinel SAP ソリューション: セキュリティ コンテンツ リファレンス (パブリック プレビュー)](sap-solution-security-content.md)
- [Azure Sentinel SAP ソリューションの詳細な SAP 要件 (パブリック プレビュー)](sap-solution-detailed-requirements.md)
