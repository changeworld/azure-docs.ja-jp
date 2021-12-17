---
title: Docker for Linux 上で Azure Cosmos DB エミュレーターを実行する
description: Linux および macOS 上で Azure Cosmos DB Linux Emulator を実行および使用する方法について説明します。 このエミュレーターを使用すると、Azure サブスクリプションを使用せずに、無料でアプリケーションをローカルで開発してテストできます。
ms.service: cosmos-db
ms.topic: how-to
author: StefArroyo
ms.author: esarroyo
ms.date: 06/04/2021
ms.openlocfilehash: c0b7dff4ac6f19b4abcf27ccf98cd8d57b270e21
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123111505"
---
# <a name="run-the-emulator-on-docker-for-linux-preview"></a>Docker for Linux 上でエミュレーターを実行する (プレビュー)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB Linux Emulator は、開発目的で Azure Cosmos DB サービスをエミュレートするローカル環境を提供します。 現時点では、Linux エミュレーターは SQL API のみをサポートしています。 Azure Cosmos DB Emulator を使用すると、ローカルでのアプリケーションの開発とテストが、Azure サブスクリプションを作成したりコストをかけたりせずに実施できます。 Azure Cosmos DB Linux Emulator でのアプリケーションの動作に満足できたら、クラウドでの Azure Cosmos DB アカウントの使用に切り替えることができます。 この記事では、macOS および Linux 環境にエミュレーターをインストールして使用する方法について説明します。

> [!NOTE]
> Cosmos DB Linux Emulator は現在プレビュー モードであり、SQL API のみをサポートしています。 Windows バージョンと比較した場合、エミュレーターによって処理される 1 秒あたりの要求数に関して、パフォーマンスがわずかに低下することがあります。 プロビジョニング可能なコンテナーの数に直接影響を与える物理パーティションの既定の数は 10 個です。
> 
> 運用環境でのエミュレーター (プレビュー) の使用はお勧めしません。 ワークロードが重い場合は、[Windows エミュレーター](local-emulator.md)を使用してください。

## <a name="how-does-the-emulator-work"></a>エミュレーターのしくみ

Azure Cosmos DB Linux Emulator には、Azure Cosmos DB サービスの高忠実度エミュレーションが用意されています。 Azure Cosmos DB と同等の機能がサポートされています。たとえば、データの作成、データのクエリ、コンテナーのプロビジョニングとスケーリング、ストアド プロシージャとトリガーの実行などです。 Azure Cosmos DB Linux Emulator を使用してアプリケーションを開発してテストし、Azure Cosmos DB 接続エンドポイントを更新することで、それらをグローバル規模で Azure にデプロイできます。

エミュレーターを使用する場合、グローバル レプリケーション、読み取りおよび書き取りの 10 ミリ秒を下回る待ち時間、調整可能な一貫性レベルなど、Azure インフラストラクチャに依存する機能は適用されません。

## <a name="differences-between-the-linux-emulator-and-the-cloud-service"></a>Linux Emulator とクラウド サービスの違い
Azure Cosmos DB Emulator はローカルの開発者ワークステーションで動作するエミュレートされた環境を提供するため、このエミュレーターとクラウドにある Azure Cosmos アカウントとの間には機能にいくつかの違いがあります。

- 現在、エミュレーターの **[データ エクスプローラー]** ペインで完全にサポートされているのは、SQL API クライアントのみです。

- Linux エミュレーターを使用すると、[プロビジョニングされたスループット](set-throughput.md) モードでのみ Azure Cosmos アカウントを作成できます。現在、[サーバーレス](serverless.md) モードはサポートされていません。

- Linux エミュレーターはスケーラブルなサービスではなく、多数のコンテナーはサポートされていません。 Azure Cosmos DB Emulator を使用する場合、既定では、400 RU/s を指定した固定サイズのコンテナー (Azure Cosmos DB SDK を使用する場合にのみサポートされます) を 10 個まで、容量無制限のコンテナーを 5 個まで作成できます。 この値を変更する方法の詳細については、[PartitionCount 値の設定](emulator-command-line-parameters.md#set-partitioncount)に関する記事をご覧ください。

- [整合性レベル](consistency-levels.md)はテスト シナリオの場合にのみコマンド ライン引数を使用して調整できます (既定の設定は Session) が、ユーザーはクラウド サービスの場合と同じ動作を想定してはなりません。 たとえば、強固および有界整合性制約の一貫性は、Cosmos DB SDK へのシグナル通知 (このアカウントの既定の一貫性) 以外にエミュレーターに対して影響を及ぼしません。

- Linux エミュレーターは、[マルチリージョン レプリケーション](distribute-data-globally.md)を提供していません。

- Azure Cosmos DB Linux Emulator のコピーには Azure Cosmos DB サービスの最新の変更が反映されていないことがあるため、必ず [Azure Cosmos DB Capacity Planner](estimate-ru-with-capacity-planner.md) に関する記事を参照し、アプリケーションのスループット (RU) のニーズを正確に見積もる必要があります。

- Linux エミュレーターは、254 文字の最大 ID プロパティ サイズをサポートしています。

## <a name="run-the-linux-emulator-on-macos"></a><a id="run-on-macos"></a>macOS で Linux Emulator を実行する

> [!NOTE]
> このエミュレーターは、Intel プロセッサを使用した MacBooks のみをサポートしています。

作業を開始するには、Docker Hub にアクセスし、[Docker Desktop for macOS](https://hub.docker.com/editions/community/docker-ce-desktop-mac/) をインストールします。 次の手順を使用して macOS 上でエミュレーターを実行します。

[!INCLUDE[linux-emulator-instructions](includes/linux-emulator-instructions.md)]

## <a name="install-the-certificate"></a><a id="install-certificate"></a>証明書をインストールする

1. エミュレーターが実行中になったら、別のターミナルを使用してローカル コンピューターの IP アドレスを変数に読み込みます。

    ```bash
    ipaddr="`ifconfig | grep "inet " | grep -Fv 127.0.0.1 | awk '{print $2}' | head -n 1`"
    ```

1. 次に、エミュレーターの証明書をダウンロードします。

    ```bash
    curl -k https://$ipaddr:8081/_explorer/emulator.pem > emulatorcert.crt
    ```


## <a name="consume-the-endpoint-via-ui"></a><a id="consume-endpoint-ui"></a>UI を介してエンドポイントを使用する

エミュレーターは、そのエンドポイントへの接続をセキュリティで保護するために自己署名証明書を使用しており、手動で信頼する必要があります。 目的の Web ブラウザーを使用して UI を介してエンドポイントを使用するには、次の手順を使用します。

1. エミュレーターの自己署名証明書がダウンロードされていることを確認してください。

   ```bash
   curl -k https://$ipaddr:8081/_explorer/emulator.pem > emulatorcert.crt
   ```

1. Mac で **キーチェーン アクセス** アプリを開き、エミュレーター証明書をインポートします。

1. **[File]** 、 **[Import Items]** の順に選択し、**emulatorcert.crt** をインポートします。

1. *emulatorcert.crt* がキーチェーンに読み込まれたら、**localhost** 名をダブルクリックし、信頼設定を **[Always Trust]** に変更します。

1. これで、`https://localhost:8081/_explorer/index.html` または `https://{your_local_ip}:8081/_explorer/index.html` を参照して、エミュレーターへの接続文字列を取得できます。

必要に応じて、アプリケーションで SSL 検証を無効にすることができます。 これは、開発目的でのみ推奨されます。運用環境で実行するときは無効にしないでください。

## <a name="run-the-linux-emulator-on-linux-os"></a><a id="run-on-linux"></a>Linux OS で Linux Emulator を実行する

開始するには、`apt` パッケージを使用し、最新バージョンの Docker をインストールします。

```bash
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
```

Linux 用 Windows サブシステム (WSL) を使用している場合は、次のコマンドを実行して `ifconfig` を取得します。

```bash
sudo apt-get install net-tools
```

次の手順を使用して Linux 上でエミュレーターを実行します。

[!INCLUDE[linux-emulator-instructions](includes/linux-emulator-instructions.md)]

4. エミュレーターが実行中になったら、別のターミナルを使用してローカル コンピューターの IP アドレスを変数に読み込みます。

    ```bash
    ipaddr="`ifconfig | grep "inet " | grep -Fv 127.0.0.1 | awk '{print $2}' | head -n 1`"
    ```

5. 次に、エミュレーターの証明書をダウンロードします。 また、自己署名エミュレーター証明書をダウンロードする下のエンドポイントは、エミュレーター エンドポイントが別のアプリケーションから要求を受信する準備ができているときのシグナル通知に使用することもできます。

    ```bash
    curl -k https://$ipaddr:8081/_explorer/emulator.pem > ~/emulatorcert.crt
    ```

6. ご利用の Linux ディストリビューションの、カスタム証明書が格納されているフォルダーに CRT ファイルをコピーします。 一般に、Debian ディストリビューションでは `/usr/local/share/ca-certificates/` に格納されます。

   ```bash
   cp ~/emulatorcert.crt /usr/local/share/ca-certificates/
   ```

7. TLS/SSL 証明書を更新します。これによって `/etc/ssl/certs/` フォルダーが更新されます。

   ```bash
   update-ca-certificates
   ```

    Java ベースのアプリケーションの場合は、証明書を [Java の信頼されたストア](local-emulator-export-ssl-certificates.md)にインポートする必要があります。

    ```bash
    keytool -keystore ~/cacerts -importcert -alias  emulator_cert -file ~/emulatorcert.crt
    java -ea -Djavax.net.ssl.trustStore=~/cacerts -Djavax.net.ssl.trustStorePassword="changeit" $APPLICATION_ARGUMENTS
    ```

## <a name="configuration-options"></a><a id="config-options"></a>構成オプション

|Name  |Default  |説明  |
|---------|---------|---------|
|  ポート: `-p`   |         |   現時点では、エミュレーター エンドポイントに必要なポートは 8081 と、10251 から 10255 のみです。     |
| `AZURE_COSMOS_EMULATOR_PARTITION_COUNT`    |    10     |    物理パーティションの総数を制御します。それによって、特定の時点で作成および存在できるコンテナーの数が制御されます。 エミュレーターの起動時間を改善するために、小規模から始めることをお勧めします。つまり、 3.     |
|  メモリ: `-m`   |         | メモリについては、3 GB 以上が必要です。     |
| コア: `--cpus`  |         |   十分なメモリと CPU コアをプロビジョニングするようにしてください。エミュレーターは 0.5 コア程度でも (非常に低速ながら) 動作する場合がありますが、少なくとも 2 コアをお勧めします。      |
|`AZURE_COSMOS_EMULATOR_ENABLE_DATA_PERSISTENCE`  | false  | この設定を単独で使用すると、コンテナーの再起動の間にデータを保持することができます。  |

## <a name="troubleshoot-issues"></a><a id="troubleshoot-issues"></a>問題のトラブルシューティング

このセクションでは、Linux エミュレーターを使用する際のエラーをトラブルシューティングするためのヒントを提供します。

### <a name="connectivity-issues"></a>接続に関する問題

#### <a name="my-app-cant-connect-to-emulator-endpoint-the-ssl-connection-could-not-be-established-or-i-cant-start-the-data-explorer"></a>アプリがエミュレーター エンドポイントに接続できない ("The SSL connection could not be established" (SSL 接続を確立できませんでした))、またはデータ エクスプローラーを開始できない

- 次のコマンドを使用して、エミュレーターが実行されていることを確認します。

    ```bash
    docker ps --all
    ```

- 特定のエミュレーター コンテナーが実行中の状態であることを確認します。

- 他のアプリケーションがエミュレーター ポート 8081 と、10250 から 10255 を使用していないことを確認します。

- コンテナーのポート 8081 が正しくマップされていて、コンテナーの外部にある環境からアクセスできることを確認します。  

   ```bash
   netstat -lt
   ```

- "localhost" の代わりに Docker コンテナーの IP アドレスを使用して、エミュレーターのエンドポイントおよびポートにアクセスしてみます。

- エミュレーターの自己署名証明書が[キーチェーン](#consume-endpoint-ui)に適切に追加されていることを確認します。

- Java アプリケーションの場合、[Java 証明書ストア セクション](#run-on-linux)に証明書をインポートしたことを確認します。

- .NET アプリケーションの場合、SSL 検証を無効にすることができます。

# <a name="net-standard-21"></a>[.NET Standard 2.1+](#tab/ssl-netstd21)

.NET Standard 2.1 以降と互換性のあるフレームワークで実行されているアプリケーションの場合は、`CosmosClientOptions.HttpClientFactory` を活用できます。

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/HttpClientFactory/Program.cs?name=DisableSSLNETStandard21)]

# <a name="net-standard-20"></a>[.NET Standard 2.0](#tab/ssl-netstd20)

.NET Standard 2.0 と互換性のあるフレームワークで実行されているアプリケーションの場合は、`CosmosClientOptions.HttpClientFactory` を活用できます。

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/HttpClientFactory/Program.cs?name=DisableSSLNETStandard20)]

---

#### <a name="my-nodejs-app-is-reporting-a-self-signed-certificate-error"></a>Node.js アプリから自己署名証明書エラーが報告される

コンテナーの IP アドレスなど、`localhost` 以外のアドレスを使用してエミュレーターに接続しようとすると、証明書がインストールされている場合でも、Node.js で証明書の自己署名に関するエラーが発生します。

`NODE_TLS_REJECT_UNAUTHORIZED` 環境変数を `0` に設定することによって、TLS 検証を無効にすることができます。

```bash
NODE_TLS_REJECT_UNAUTHORIZED=0
```

このフラグは、Node.js の TLS を無効にするため、ローカル開発にのみ推奨されます。 詳細については、[Node.js に関するドキュメント](https://nodejs.org/api/cli.html#cli_node_tls_reject_unauthorized_value)および [Cosmos DB Emulator 証明書に関するドキュメント](local-emulator-export-ssl-certificates.md#how-to-use-the-certificate-in-nodejs)を参照してください。

#### <a name="the-docker-container-failed-to-start"></a>Docker コンテナーを開始できない

エミュレーターでエラーが発生し、次のメッセージが表示される。

```bash
/palrun: ERROR: Invalid mapping of address 0x40037d9000 in reserved address space below 0x400000000000. Possible causes:
1. The process (itself, or via a wrapper) starts up its own running environment sets the stack size limit to unlimited via syscall setrlimit(2);
2. The process (itself, or via a wrapper) adjusts its own execution domain and flag the system its legacy personality via syscall personality(2);
3. Sysadmin deliberately sets the system to run on legacy VA layout mode by adjusting a sysctl knob vm.legacy_va_layout.
```

このエラーは、現在の Docker ホスト プロセッサの種類が Microsoft の Docker イメージと互換性がないことによる可能性があります。つまり、そのコンピューターが M1 チップセットを搭載した MacBook であるためです。

#### <a name="my-app-received-too-many-connectivity-related-timeouts"></a>アプリで表示される接続関連のタイムアウトが多すぎる

- Docker コンテナーが、十分なリソース [(コアまたはメモリ)](#config-options) を使用してプロビジョニングされていません。 コアの数を増やすことをお勧めします。または、起動時にプロビジョニングされる物理パーティションの数を減らします。

- TCP 接続数が現在の OS 設定を超えていないことを確認してください。

- アプリケーション内のドキュメントのサイズを小さくしてみてください。
    
#### <a name="my-app-could-not-provision-databasescontainers"></a>アプリでデータベースまたはコンテナーをプロビジョニングできない

エミュレーターでプロビジョニングされた物理パーティションの数が少なすぎます。 未使用のデータベースまたはコレクションを削除するか、[より多くの物理パーティション](#config-options)を使用してエミュレーターを起動します。

### <a name="reliability-and-crashes"></a>信頼性とクラッシュ

- エミュレーターを起動できない:

  - [Linux 用 Cosmos DB エミュレーターの最新イメージを実行](#refresh-linux-container)していることを確認します。 そうでない場合は、接続関連の問題に関する上記のセクションを参照してください。

  - Cosmos DB エミュレーターのデータ フォルダーが "マウントされたボリューム" である場合は、そのボリュームに十分な領域があることと、読み取りと書き込みが可能であることを確認します。

  - 推奨される設定を使用したコンテナーの作成が機能することを確認します。 そうである場合、失敗の原因として最も考えられるのは、コンテナーの起動時にそれぞれの Docker コマンドを介して渡された追加の設定です。

  - エミュレーターの起動に失敗し、次のエラーが発生した場合:
  
    ```bash
    "Failed loading Emulator secrets certificate. Error: 0x8009000f or similar, a new policy might have been added to your host that prevents an application such as Azure Cosmos DB emulator from creating and adding self signed certificate files into your certificate store."
    ```

    IT 部門によって通常追加される特定のポリシーはローカル管理者よりも優先されるため、これは管理者コンテキストで実行する場合でも発生する可能性があります。 代わりにエミュレーターに Docker イメージを使用すると、このケースに役立つ可能性があります。ただし、ホスト マシンのコンテキストに自己署名エミュレーター SSL 証明書を追加するアクセス許可を持っている場合に限ります (これは Java および .NET Cosmos SDK クライアント アプリケーションで必要です)。

- エミュレーターがクラッシュしている:

  - [推奨される設定](#run-on-linux)を使用したコンテナーの作成が機能することを確認します。 そうである場合、失敗の原因として最も考えられるのは、コンテナーの起動時にそれぞれの Docker コマンドを介して渡された追加の設定です。

  - エミュレーターの Docker コンテナーをアタッチ モードで起動します (`docker start -it` を参照)。

  - クラッシュ関連のダンプやデータを収集し、[説明されている手順](#report-an-emulator-issue)に従って問題を報告します。  

### <a name="data-explorer-errors"></a>データ エクスプローラーのエラー

- データを表示できない:

  - 上記の接続関連の問題に関するセクションを参照してください。

  - ブラウザーがデータ エクスプローラー ページにアクセスできるように、エミュレーターの自己署名証明書が正しくインポートされ、手動で信頼されていることを確認します。

  - データベースまたはコンテナーを作成し、データ エクスプローラーを使用して項目を挿入してみます。 成功した場合は、問題の原因がアプリケーションにある可能性が最も高くなります。 そうでない場合は、[Cosmos DB チームにお問い合わせください](#report-an-emulator-issue)。

### <a name="performance-issues"></a>パフォーマンスの問題

1 秒あたりの要求数が少ないが、要求の待機時間が長い:

- Docker コンテナーが、十分なリソース [(コアまたはメモリ)](#config-options) を使用してプロビジョニングされていません。 コアの数を増やすことをお勧めします。または、起動時にプロビジョニングされる物理パーティションの数を減らします。

## <a name="refresh-linux-container"></a><a id="refresh-linux-container"></a>Linux コンテナーを更新する

Linux コンテナーを更新するには、次の手順を使用します。

1. 次のコマンドを実行して、すべての Docker コンテナーを表示します。

   ```bash
   docker ps --all
   ```

1. 上記のコマンドから取得した ID を使用して、コンテナーを削除します。

   ```bash
   docker rm ID_OF_CONTAINER_FROM_ABOVE
   ```

1. 次に、すべての Docker イメージを一覧表示します。

   ```bash
   docker images
   ```

1. 前の手順で取得した ID を使用してイメージを削除します。

   ```bash
   docker rmi ID_OF_IMAGE_FROM_ABOVE
   ```

1. Cosmos DB Linux Emulator の最新のイメージをプルします。

   ```bash
   docker pull mcr.microsoft.com/cosmosdb/linux/azure-cosmos-emulator
   ```

1. 停止したコンテナーを開始するには、以下を実行します。

   ```bash
   docker start -ai ID_OF_CONTAINER
   ```

## <a name="report-an-emulator-issue"></a>エミュレーターの問題を報告する

Linux エミュレーターに関する問題を報告する際は、問題についてできるだけ多くの情報を提供してください。 詳細情報は次のとおりです。

- 発生したエラーまたは問題の説明
- 環境 (OS、ホスト構成)
- コンピューターおよびプロセッサの種類
- エミュレーターの作成と開始に使用したコマンド (Docker Compose を使用している場合は YML ファイル)
- ワークロードの説明
- 使用されているデータベースまたはコレクションと項目のサンプル
- アタッチ モードでエミュレーターの Docker コンテナーを起動したときのコンソール出力を含めます
- 上記のすべてを [Azure Cosmos DB チーム](mailto:cdbportalfeedback@microsoft.com)に送信してください。

## <a name="next-steps"></a>次の手順

この記事では、無料のローカル開発のために Azure Cosmos DB Linux エミュレーターを使用する方法について学習しました。 次の記事に進むことができます。

- [Java、Python、および Node.js アプリで使用する Azure Cosmos DB エミュレーター証明書のエクスポート](local-emulator-export-ssl-certificates.md)
- [エミュレーターを使用して問題をデバッグする](troubleshoot-local-emulator.md)
