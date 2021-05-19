---
title: Azure Blockchain Service の提供終了のお知らせとガイダンス
description: Azure Blockchain Service を、マネージドまたは自己管理型のブロックチェーン オファリングに移行する
ms.date: 05/10/2021
ms.topic: how-to
ms.openlocfilehash: 6fb86e426e446ba5515a285b04587093ee6fe4e5
ms.sourcegitcommit: 1b19b8d303b3abe4d4d08bfde0fee441159771e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/11/2021
ms.locfileid: "109752735"
---
# <a name="migrate-azure-blockchain-service"></a>Azure Blockchain Service を移行する

Azure Blockchain Service から代替オファリングに台帳データを移行できます。

> [!IMPORTANT]
> **2021 年 9月 10 日** に、Azure Blockchain は提供終了となります。 運用環境または評価環境での開発の状態に基づいて、Azure Blockchain Service から代替オファリングに台帳データを移行してください。

## <a name="evaluate-alternatives"></a>代替手段を評価する

移行を計画する際の最初の手順は、代替オファリングを評価することです。 運用環境または評価環境で行われている開発の状態に基づいて、次に使用する代替手段を評価します。

### <a name="production-or-pilot-phase"></a>運用またはパイロット フェーズ

ブロックチェーン ソリューションを既にデプロイ、開発し、それが運用またはパイロット フェーズの場合、次の代替手段を検討してください。

#### <a name="quorum-blockchain-service"></a>Quorum Blockchain Service

Quorum Blockchain Service は、Azure での ConsenSys によるマネージド オファリングであり、台帳テクノロジとして Quorum をサポートします。

- **マネージド オファリング** - Quorum Blockchain Service には、Azure Blockchain Service と比較して追加の管理オーバーヘッドはありません。
- **台帳テクノロジ** - ConsenSys Quorum に基づきます。これは、Azure Blockchain Service で使用されている GoQuorum 台帳テクノロジの拡張バージョンです。 新たな学習は不要です。 詳細については、[Consensys Quorum の FAQ](https://consensys.net/quorum/faq) に関するページを参照してください。
- **継続性** - ConsenSys 提供の Quorum Blockchain Service に既存のデータを移行できます。 詳細については、「[Azure Blockchain Service からのデータのエクスポート](#export-data-from-azure-blockchain-service)」を参照してください。

詳細については、[Quorum Blockchain Service](https://consensys.net/QBS) に関するページを参照してください。

#### <a name="azure-vm-based-deployment"></a>Azure VM ベースのデプロイ

IaaS VM にブロックチェーンをデプロイするために使用できるブロックチェーン リソース管理テンプレートが複数あります。

- **台帳テクノロジ** - 新しい ConsenSys Quorum を含む Quorum 台帳テクノロジを引き続き使用できます。
- **自己管理** - デプロイ後、インフラストラクチャとブロックチェーン スタックをご自分で管理します。

### <a name="new-deployment-or-evaluation-phase"></a>新規デプロイまたは評価フェーズ

新しいソリューションの開発を開始する場合、または評価フェーズの場合、シナリオの要件に基づいて次の代替手段を検討してください。

- [Azure Marketplace の Quorum テンプレート](https://azuremarketplace.microsoft.com/marketplace/apps/consensys.quorum-dev-quickstart)
- [Azure Marketplace の Besu テンプレート](https://azuremarketplace.microsoft.com/marketplace/apps/consensys.hyperledger-besu-quickstart)

### <a name="how-to-migrate-to-an-alternative"></a>代替手段に移行する方法

運用ワークロードを移行するには、最初に [Azure Blockchain Service からデータをエクスポート](#export-data-from-azure-blockchain-service)します。 データのコピーを作成した後、このデータを任意の代替手段に移行できます。

移行先として推奨されるのは ConsenSys Quorum Blockchain Service です。 このサービスにオンボードするには、[Quorum Blockchain Service](https://consensys.net/QBS) のページで登録を行います。

Azure で仮想マシンを使用してブロックチェーン ソリューションを自己管理するには、「[Azure VM ベースの Quorum に関するガイダンス](#azure-vm-based-quorum-guidance)」を参照して、トランザクションと検証のノードを設定してください。
## <a name="export-data-from-azure-blockchain-service"></a>Azure Blockchain Service からデータをエクスポートする

現在の開発状態に基づいて、Azure Blockchain Service で既存の台帳データを使用すること、または新しいネットワークを開始して、任意のソリューションを使用することを選択できます。 Azure Blockchain Service で既存の台帳データを使用する必要がない、または使用する意図がないすべてのシナリオで、任意のソリューションに基づいた新しいコンソーシアムを作成することをお勧めします。

### <a name="open-support-case"></a>サポート ケースを開く

有料サポート プランがある場合は、コンソーシアムを一時停止し、ブロックチェーン データをエクスポートするために、Microsoft サポート チケットを開いてください。

1. サポート チケットを開くには、Azure portal を使用します。 *[問題の説明]* に、次の詳細を入力します。

    ![Azure portal のサポート チケットの [問題の説明] フォーム](./media/migration-guide/problem-description.png)

    | フィールド | Response |
    |-------|--------- |
    | 問題の種類 | 技術面 |
    | サービス | Azure Blockchain Service - プレビュー |
    | まとめ | [Request data for migration]\(移行のためにデータを要求\) |
    | 問題の種類 | その他 |

1. *[追加情報]* に、次の詳細を追加します。

    ![Azure portal のサポート チケットの [追加情報] フォーム](./media/migration-guide/additional-details.png)

    - サブスクリプション ID または Azure Resource Manager リソース ID
    - Tenant
    - コンソーシアム名
    - リージョン
    - メンバー名
    - 移行を開始する任意の日時

コンソーシアムに複数のメンバーが含まれる場合、各メンバーは、それぞれのメンバー データに対して個別のサポート チケットを開く必要があります。

### <a name="pause-consortium"></a>コンソーシアムを一時停止する

コンソーシアムはデータのエクスポートのために一時停止し、この期間中のトランザクションは失敗するため、データのエクスポートについて、コンソーシアムのメンバーと調整を行う必要があります。

Azure Blockchain Service チームは、コンソーシアムを一時停止し、データのスナップショットをエクスポートし、暗号化形式でのダウンロード用の有効期間が短い SAS URL を介してデータを利用できるようにします。 スナップショットの作成後、コンソーシアムは再開されます。

> [!IMPORTANT]
> ネットワークに対して新しいブロックチェーン トランザクションを開始するすべてのアプリケーションを停止する必要があります。 アクティブなアプリケーションが原因で、データが失われる場合や、元と移行先のネットワークが同期されない場合があります。

### <a name="download-data"></a>データをダウンロードする

Microsoft サポート提供の有効期間が短い SAS URL リンクを使用してデータをダウンロードします。

> [!IMPORTANT]
> 7 日以内にデータをダウンロードする必要があります。

API アクセス キーを使用してデータの暗号化を解除します。 キーは、[Azure portal から取得](configure-transaction-nodes.md#access-keys)すること、または [REST API を使用して取得](/rest/api/blockchain/2019-06-01-preview/blockchainmembers/listapikeys)することができます。

> [!CAUTION]
> 既定のトランザクション ノード API アクセス キー 1 のみが、そのメンバーのすべてのノード データを暗号化するために使用されます。
>
> 移行の間に API アクセス キーをリセットしないでください。

ConsenSys Quorum Blockchain サービスまたは IaaS VM ベースのデプロイのいずれかでデータを使用できます。

ConsenSys Quorum Blockchain Service の移行については、ConsenSys ([qbsmigration@consensys.net](mailto:qbsmigration@consensys.net)) にお問い合わせください。

IaaS VM ベースのデプロイでデータを使用する場合、この記事の「[Azure VM ベースの Quorum に関するガイダンス](#azure-vm-based-quorum-guidance)」セクションの手順に従ってください。

### <a name="delete-resources"></a>リソースを削除する

データのコピーが完了したら、Azure Blockchain のメンバー リソースを削除することをお勧めします。 これらのリソースが存在する間は、引き続き課金されます。

## <a name="azure-vm-based-quorum-guidance"></a>Azure VM ベースの Quorum に関するガイダンス

次の手順を使用して、トランザクション ノードと検証ノードを作成します。

### <a name="transaction-node"></a>トランザクション ノード

トランザクション ノードには 2 つのコンポーネントがあります。 Tessera はプライベート トランザクションに使用され、Geth は Quorum アプリケーションに使用されます。 検証ノードには Geth コンポーネントのみ必要です。

#### <a name="tessera"></a>Tessera

1. Java 11 をインストールします。 たとえば、`apt install default-jre` のようにします。
1. `tessera-config.json` でパスを更新します。 `/working-dir/**` のすべての参照を `/opt/blockchain/data/working-dir/**` に変更します。
1. 新しい IP アドレスに基づき他のトランザクション ノードの IP アドレスを更新します。 HTTPS は、Tessera 構成では有効になっていないため機能しません。 TLS の構成方法について詳しくは、[Tessera での TLS の構成](https://docs.tessera.consensys.net/en/stable/HowTo/Configure/TLS/)に関する記事を参照してください。
1. NSG ルールを更新して、ポート 9000 への着信接続を許可します。
1. 次のコマンドを使用して、Tessera を実行します。

    ```bash
    java -Xms512M -Xmx1731M -Dlogback.configurationFile=/tessera/logback-tessera.xml -jar tessera.jar -configfile /opt/blockchain/data/working-dir/tessera-config.json > tessera.log 2>&1 &
    ```

#### <a name="geth"></a>Geth

1. `/opt/blockchain/data/working-dir/dd/static-nodes.json` で enode アドレスの IP を更新します。 パブリック IP アドレスが許可されています。
1. `/geth/config.toml` で StaticNodes キーの同じ IP アドレスを変更します。
1. NSG ルールを更新して、ポート 30303 への着信接続を許可します。
1. 次のコマンドを使用して Geth を実行します。

    ```bash
    export NETWORK_ID='' # Get network ID from metadata. The network ID is the same for consortium.

    PRIVATE_CONFIG=tm.ipc geth --config /geth/config.toml --datadir /opt/blockchain/data/working-dir/dd --networkid $NETWORK_ID --istanbul.blockperiod 5 --nodiscover --nousb --allow-insecure-unlock --verbosity 3 --txpool.globalslots 80000 --txpool.globalqueue 80000 --txpool.accountqueue 50000 --txpool.accountslots 50000 --targetgaslimit 700000000 --miner.gaslimit 800000000 --syncmode full --rpc --rpcaddr 0.0.0.0 --rpcport 3100 --rpccorsdomain '*' --rpcapi admin,db,eth,debug,net,shh,txpool,personal,web3,quorum,istanbul --ws --wsaddr 0.0.0.0 --wsport 3000 --wsorigins '*' --wsapi admin,db,eth,debug,net,shh,txpool,personal,web3,quorum,istanbul
    ```

### <a name="validator-node"></a>検証ノード

検証ノードの手順はトランザクション ノードに似ていますが、Geth スタートアップ コマンドには追加フラグ `-mine` を設定します。 Tessera は、検証ノードでは開始しません。 ペアの Tessera なしで Geth を実行するには、Geth コマンドに `PRIVATE_CONFIG=ignore` を渡します。 次のコマンドを使用して Geth を実行します。

```bash
export NETWORK_ID=`j q '.APP_SETTINGS | fromjson | ."network-id"' env.json`

PRIVATE_CONFIG=ignore geth --config /geth/config.toml --datadir /opt/blockchain/data/working-dir/dd --networkid $NETWORK_ID --istanbul.blockperiod 5 --nodiscover --nousb --allow-insecure-unlock --verbosity 3 --txpool.globalslots 80000 --txpool.globalqueue 80000 --txpool.accountqueue 50000 --txpool.accountslots 50000 --targetgaslimit 700000000 --miner.gaslimit 800000000 --syncmode full --rpc --rpcaddr 0.0.0.0 --rpcport 3100 --rpccorsdomain '*' --rpcapi admin,db,eth,debug,net,shh,txpool,personal,web3,quorum,istanbul --ws --wsaddr 0.0.0.0 --wsport 3000 --wsorigins '*' --wsapi admin,db,eth,debug,net,shh,txpool,personal,web3,quorum,istanbul –mine
```

## <a name="upgrading-quorum"></a>Quorum のアップグレード

Azure Blockchain Service は、次に示されているいずれかの Quorum バージョンで実行されています。 同じ Quorum バージョンを使用すること、または下の手順に従って最新バージョンの ConsenSys Quorum を使用することができます。

### <a name="upgrade-quorum-version-260-or-270-to-consensys-2110"></a>Quorum バージョン 2.6.0 または 2.7.0 を ConsenSys 21.1.0 にアップグレードする

Quorum バージョン 2.6 または 2.7 からのアップグレードは簡単です。 次のリンクを使用してダウンロードと更新を行います。
1. [ConsenSys Quorum と関連バイナリ v21.1.0](https://github.com/ConsenSys/quorum/releases/tag/v21.1.0) をダウンロードします。
1. Tessera の最新バージョン [tessera-app-21.1.0-app.jar](https://github.com/ConsenSys/tessera/releases/tag/tessera-21.1.0) をダウンロードします。

### <a name="upgrade-quorum-version-250-to-consensys-2110"></a>Quorum バージョン 2.5.0 を ConsenSys 21.1.0 にアップグレードする

1. [ConsenSys Quorum と関連バイナリ v21.1.0](https://github.com/ConsenSys/quorum/releases/tag/v21.1.0) をダウンロードします。
1. Tessera の最新バージョン [tessera-app-21.1.0-app.jar](https://github.com/ConsenSys/tessera/releases/tag/tessera-21.1.0) をダウンロードします。
バージョン 2.5.0 の場合、genesis ファイルの軽微な変更がいくつかあります。 genesis ファイルで次の変更を行います。

1. `byzantiumBlock` の値は 1 に設定されており、これを `constantinopleBlock` (0 に設定されています) よりも小さくすることはできません。 `byzantiumBlock` の値を 0 に設定します。
1. `petersburgBlock`、`istanbulBlock` を future ブロックに設定します。 この値は、すべてのノードで同じである必要があります。
1. この手順は省略可能です。 `ceil2Nby3Block` は、Azure Blockchain Service Quorum 2.5.0 バージョンでは誤って配置されています。 これは、istanbul 構成内に配置する必要があり、値は future ブロックに設定します。 この値は、すべてのノードで同じである必要があります。
1. 次のコマンドを使用して Geth を実行し、genesis ブロックを再初期化します。

    ```bash
    geth --datadir "Data Directory Path" init "genesis file path"
    ```

1.  Geth を実行します。

## <a name="exported-data-reference"></a>エクスポートされたデータ参照

このセクションでは、IaaS VM デプロイへのデータのインポートを支援するために、メタデータとフォルダー構造について説明します。

### <a name="metadata-info"></a>メタデータ情報

| 名前               | サンプル                | 説明           |
|--------------------|-----------------------|-----------------------|
| consortium_name    | \<ConsortiumName\>    | コンソーシアム名 (Azure Blockchain Service 全体で一意)。 |
| Consortium_Member_Count || コンソーシアム内のメンバーの数。 |
| member_name        | \<memberName\>        | Blockchain メンバー名 (Azure Blockchain Service 全体で一意)。 |
| node_name          | transaction-node      | ノード名 (各メンバーが複数のノードを所持します)。 |
| network_id         | 543                   | Geth ネットワーク ID。      |
| is_miner           | 誤                 | Is_Miner == true (検証ノード)、Is_Miner == false (トランザクション ノード) |
| quorum_version     | 2.7.0                 | Quorum のバージョン     |
| tessera_version    | 0.10.5                | Tessera のバージョン      |
| java_version       | java-11-openjdk-amd64 | Tessera で使用される Java のバージョン |
| CurrentBlockNumber |                       | ブロックチェーン ネットワークの現在のブロック番号 |

## <a name="migrated-data-folder-structure"></a>移行対象のデータ フォルダーの構造

最上位レベルには、メンバーの各ノードに対応するフォルダーがあります。

- **Standard SKU** - 2 つの検証ノード (validator-node-0 と validator-node-1)
- **Basic SKU** - 1 つの検証ノード (validator-node-0)
- **トランザクション ノード** - transaction-node という名前の既定のトランザクション ノード。

その他のトランザクション ノード フォルダーには、トランザクション ノード名に対応する名前が付けられます。

### <a name="node-level-folder-structure"></a>ノード レベルのフォルダー構造

各ノード レベルのフォルダーには、暗号化キーを使用して暗号化された zip ファイルが含まれます。 暗号化キーの取得について詳しくは、この記事の「[データをダウンロードする](#download-data)」セクションを参照してください。

| ディレクトリ/ファイル | 説明 |
|----------------|--------------|
| /config/config.toml | Geth のパラメーター。 コマンド ライン パラメーターが優先されます。 |
| /config/genesis.json | Genesis ファイル |
| /config/logback-tessera.xml | Tessera の Logback 構成 |
| /config/static-nodes.json | 静的ノード。 ブートストラップ ノードが削除され、自動検出が無効になります。 |
| /config/tessera-config.json | Tessera の構成 |
| /data/c/ | Tessera の DB |
| /data/dd/ | Geth のデータ ディレクトリ |
| /env/env | Metadata |
| /keys/ | Tessera のキー |
| /scripts/ | スタートアップ スクリプト (参照専用として提供されています) |

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="what-does-service-retirement-mean-for-existing-customers"></a>既存の顧客にとって、サービスの提供終了は何を意味しますか?

既存の Azure Blockchain Service デプロイは、2021 年 9 月 10 日より後は、継続使用できません。 提供終了の前に、要件に基づいて、この記事で提案されている代替手段の評価を開始してください。

### <a name="what-happens-to-existing-deployments-after-the-announcement-of-retirement"></a>提供終了がアナウンスされた後、既存のデプロイはどうなりますか?

既存のデプロイは、2021 年 9 月 10 日までサポートされます。 推奨される代替手段を評価し、代替オファリングにデータを移行し、代替オファリングで要件に対処し、Azure Blockchain Service のデプロイから移行を開始します。

### <a name="how-long-will-the-existing-deployments-be-supported-on-azure-blockchain-service"></a>Azure Blockchain Service で既存のデプロイはいつまでサポートされますか?

既存のデプロイは、2021 年 9 月 10 日までサポートされます。

### <a name="will-i-be-allowed-to-create-new-azure-blockchain-members-while-in-retirement-phase"></a>提供終了の段階で、新しい Azure Blockchain メンバーは作成できますか?

2021 年 5 月 10 日より後は、新しいメンバーの作成やデプロイはサポートされません。
