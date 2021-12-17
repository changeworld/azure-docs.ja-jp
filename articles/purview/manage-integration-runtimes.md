---
title: 統合ランタイムの作成および管理
description: この記事では、Azure Purview で統合ランタイムを作成して管理する手順について説明します。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 10/22/2021
ms.openlocfilehash: 10e893886d98fc5ea6d79bf8092cc5b0d948d84a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132309720"
---
# <a name="create-and-manage-a-self-hosted-integration-runtime"></a>セルフホステッド統合ランタイムの作成および管理

この記事では、Azure Purview でデータ ソースのスキャンを行うことができるセルフホステッド統合ランタイム (SHIR) を作成および管理する方法について説明します。

> [!NOTE]
> Purview Integration Runtime は、同じマシン上の Azure Synapse Analytics または Azure Data Factory Integration Runtime と共有することはできません。 別のマシンにインストールする必要があります。

## <a name="prerequisites"></a>前提条件

- サポートされている Windows のバージョンは次のとおりです。
  - Windows 8.1
  - Windows 10
  - Windows Server 2012
  - Windows Server 2012 R2
  - Windows Server 2016
  - Windows Server 2019

セルフホステッド統合ランタイムのドメイン コントローラーへのインストールはサポートされていません。

- セルフホステッド統合ランタイムには、.NET Framework 4.7.2 以降を含む 64 ビット オペレーティング システムが必要です。 詳細については、「 [.NET Framework システム要件](/dotnet/framework/get-started/system-requirements) 」をご覧ください。
- セルフホステッド統合ランタイム コンピューターに推奨される最小構成は、4 コアの 2 GHz プロセッサ、8 GB の RAM、および 80 GB の使用可能なハード ドライブ領域です。 システム要件の詳細については、[ダウンロード](https://www.microsoft.com/download/details.aspx?id=39717)のページを参照してください。
- ホスト コンピューターが休止状態の場合、セルフホステッド統合ランタイムはデータ要求に応答しません。 セルフホステッド統合ランタイムをインストールする前に、コンピューターに適切な電源プランを構成します。 コンピューターが休止状態に入るよう構成されている場合、セルフホステッド統合ランタイムのインストーラーによりメッセージが出力されます。
- セルフホステッド統合ランタイムを正常にインストールして構成するには、コンピューターの管理者である必要があります。
- スキャンは、設定したスケジュールに従って特定の頻度で実行されます。 コンピューター上のプロセッサおよび RAM の使用量は、ピーク時とアイドル時のある同じパターンに従います。 また、リソース使用量は、スキャンされるデータの量に大きく依存します。 複数のスキャン ジョブが進行中のときには、ピーク時にリソース使用率が上昇します。
- Parquet、ORC、または Avro 形式のデータの抽出中にタスクが失敗することがあります。

> [!IMPORTANT]
> セルフホステッド統合ランタイムを使用して Parquet ファイルをスキャンする場合、**64 ビットの JRE 8 (Java Runtime Environment) または OpenJDK** を IR マシンにインストールする必要があります。 インストール ガイドについては、[ページの下部にある Java Runtime Environment のセクション](#java-runtime-environment-installation)を参照してください。

## <a name="setting-up-a-self-hosted-integration-runtime"></a>セルフホステッド統合ランタイムをセットアップする

セルフホステッド統合ランタイムを作成およびセットアップするには、次の手順に従います。

## <a name="create-a-self-hosted-integration-runtime"></a>自己ホスト型統合ランタイムを作成する

1. [Purview Studio](https://web.purview.azure.com/resource/) のホーム ページの左側のナビゲーション ウィンドウで、 **[Data Map]** を選択します。

2. 左側のウィンドウの **[Sources and scanning]\(ソースとスキャン\)** で **[統合ランタイム]** を選択し、 **[+ 新規]** を選択します。

   :::image type="content" source="media/manage-integration-runtimes/select-integration-runtimes.png" alt-text="IR の選択":::

3. **[統合ランタイムのセットアップ]** ページで **[セルフホステッド]** を選択してセルフホステッド IR を作成し、 **[続行]** を選択します。

   :::image type="content" source="media/manage-integration-runtimes/select-self-hosted-ir.png" alt-text="新しい SHIR を作成します。":::

4. IR の名前を入力し、 [作成] を選択します。

5. **[統合ランタイムのセットアップ]** ページで、 **[手動セットアップ]** セクションに記載されている手順に従います。 統合ランタイムは、ダウンロード サイトから、それを実行する VM またはマシンにダウンロードする必要があります。

   :::image type="content" source="media/manage-integration-runtimes/integration-runtime-settings.png" alt-text="キーの取得":::

   - 認証キーをコピーして貼り付けます。

   - セルフホステッド統合ランタイムを、「[Microsoft Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717)」からローカルの Windows マシンにダウンロードします。 インストーラーを実行します。 5\.4.7803.1 や 5.6.7795.1 などのセルフホステッド統合ランタイム バージョンがサポートされています。 

   - **[Integration Runtime (セルフホステッド) の登録]** ページで、前に保存した 2 つのキーのいずれかを貼り付け、 **[登録]** を選択します。

     :::image type="content" source="media/manage-integration-runtimes/register-integration-runtime.png" alt-text="キーの入力":::

   - **[新しい統合ランタイム (セルフホステッド) ノード]** ページで **[完了]** を選択します。

6. セルフホステッド統合ランタイムが正常に登録されると、次のウィンドウが表示されます。

   :::image type="content" source="media/manage-integration-runtimes/successfully-registered.png" alt-text="正常に登録":::

### <a name="configure-proxy-server-settings"></a>プロキシ サーバーの設定を構成する

HTTP プロキシに対して **[システム プロキシを使用する]** オプションを選択すると、セルフホステッド統合ランタイムで、diahost.exe.config と diawp.exe.config のプロキシ設定が使用されます。これらのファイルでプロキシが指定されていない場合、セルフホステッド統合ランタイムではプロキシを経由せず、直接クラウド サービスに接続します。 diahost.exe.config ファイルを更新する手順を以下に示します。

1. エクスプローラーで、元のファイルのバックアップとして C:\Program Files\Microsoft Integration Runtime\5.0\Shared\diahost.exe.config のセーフ コピーを作成します。
1. 管理者として実行中のメモ帳を開きます。
1. メモ帳で、テキスト ファイル C:\Program Files\Microsoft Integration Runtime\5.0\Shared\diahost.exe.config を開きます。
1. 次のコードに示されている既定の **system.net** のタグを見つけます。

    ```xml
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```

    その後、次の例で示すように、プロキシ サーバーの詳細を追加できます。

    ```xml
    <system.net>
        <defaultProxy enabled="true">
              <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    プロキシ タグでは、`scriptLocation` のように必要な設定を指定するための追加のプロパティが許可されます。 構文については、「[\<proxy\> 要素 (ネットワーク設定)](/dotnet/framework/configure-apps/file-schema/network/proxy-element-network-settings)」を参照してください。

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```

1. 元の場所に構成ファイルを保存します。 次に、セルフホステッド統合ランタイムのホスト サービスを再開して、変更を取得します。

   サービスを再開するには、コントロール パネルのサービス アプレットを使用します。 または、Integration Runtime 構成マネージャーから、 **[サービスの停止]** ボタンを選択し、 **[サービスの開始]** を選びます。

   サービスが開始されない場合は、不正確な XML タグ構文が編集済みのアプリケーション構成ファイルに追加されている可能性があります。

> [!IMPORTANT]
> diahost.exe.config と diawp.exe.config の両方を忘れずに更新してください。

また、Microsoft Azure が会社の許可リストにあることを確認する必要もあります。 有効な Azure IP アドレスの一覧をダウンロードできます。 各クラウドの IP 範囲 (リージョン別とクラウド内のタグ付けされたサービス別に分けられています) を、MS ダウンロードから入手できるようになりました。 
   - パブリック: https://www.microsoft.com/download/details.aspx?id=56519

### <a name="possible-symptoms-for-issues-related-to-the-firewall-and-proxy-server"></a>ファイアウォールとプロキシ サーバーに関する問題で発生する可能性がある症状

次のようなエラー メッセージが表示される場合は、ファイアウォールまたはプロキシ サーバーの構成が正しくない可能性があります。 このような構成では、セルフホステッド統合ランタイムは、Azure マネージド ストレージ アカウントまたはデータ ソースに接続できません。 ファイアウォールとプロキシ サーバーが確実に正しく構成されるようにするには、前のセクションを参照してください。

- セルフホステッド統合ランタイムを登録すると、次のエラー メッセージが表示されます。"この Integration Runtime ノードの登録に失敗しました。 認証キーが有効であり、Integration Runtime ホスト サービスがこのマシンで実行されていることをご確認ください。"
- Integration Runtime 構成マネージャーを開くと、 **[切断]** または **[接続中]** という状態が表示されます。 **[イベント ビューアー]**  >  **[アプリケーションとサービス ログ]**  >  **[Microsoft Integration Runtime]** の順に選択して Windows イベント ログを表示すると、次のようなエラー メッセージが表示されます。

  ```output
  Unable to connect to the remote server
  A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
  ```

## <a name="networking-requirements"></a>ネットワーク要件

セルフホステッド統合ランタイム マシンは、正常に動作するために複数のリソースに接続する必要があります。

* セルフホステッド統合ランタイムを使用してスキャンするソース。
* Purview リソースのための資格情報を格納するために使用されている任意の Azure Key Vault。
* Purview によって作成されたマネージド ストレージ アカウント リソースとイベント ハブ リソース。

マネージ ストレージ リソースとイベント ハブ リソースは、サブスクリプション内の Purview リソースの名前が含まれるリソース グループで見つかります。 Azure Purview により、これらのリソースを使用して、他の多くのものと共にスキャンの結果が取り込まれるので、セルフホステッド統合ランタイムはこれらのリソースに直接接続できる必要があります。

企業およびマシンのファイアウォールの通過を許可する必要があるドメインとポートを以下に示します。

> [!NOTE]
> "\<managed Purview storage account>" と示されているドメインについては、Purview リソースに関連付けられているマネージド ストレージ アカウントの名前を追加します。 このリソースはポータルで見つけることができます。 リソース グループで managed-rg-\<your Purview Resource name> という名前のグループを検索します。 たとえば、managed-rg-contosoPurview のようなものです。 このリソース グループのストレージ アカウントの名前を使用します。
> 
> "\<managed Event Hub resource>" と示されているドメインについては、Purview リソースに関連付けられているマネージド イベント ハブの名前を追加します。 これは、マネージド ストレージ アカウントと同じリソース グループで見つかります。

| ドメイン名                  | 送信ポート | 説明                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net` | 443            | グローバル インフラストラクチャ Purview により、スキャンを実行するために使用されます。 専用のリソースがないため、ワイルドカードが必要です。 |
| `<managed Event Hub resource>.servicebus.windows.net` | 443            | これは、Purview により、関連付けられている Service Bus と接続するために使用されます。 これは、上記のドメインを許可することによってカバーされますが、プライベート エンドポイントを使用している場合は、この単一のドメインへのアクセスをテストする必要があります。|
| `*.frontend.clouddatahub.net` | 443            | グローバル インフラストラクチャ Purview により、スキャンを実行するために使用されます。 専用のリソースがないため、ワイルドカードが必要です。 |
| `<managed Purview storage account>.core.windows.net`          | 443            | セルフホステッド統合ランタイムにより、マネージド Azure ストレージ アカウントに接続するために使用されます。|
| `<managed Purview storage account>.queue.core.windows.net` | 443            | スキャン プロセスを実行するために Purview によって使用されるキュー。 |
| `download.microsoft.com` | 443           | SHIR の更新の場合は省略可能。 |

ソースに基づいて、他の Azure または外部ソースのドメインを許可する必要がある場合があります。 Key Vault に保存されている資格情報に接続する場合の Azure Key Vault ドメインとともに、いくつかの例を以下に示します。

| ドメイン名                  | 送信ポート | 説明                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `<storage account>.core.windows.net`          | 443            | オプション。Azure Storage アカウントに接続する場合。 |
| `*.database.windows.net`      | 1433           | オプション。Azure SQL Database または Azure Synapse Analytics に接続する場合。 |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | オプション。Azure Data Lake Store Gen 1 に接続する場合。 |
| `<datastoragename>.dfs.core.windows.net`    | 443            | オプション。Azure Data Lake Store Gen 2 に接続する場合。 |
| `<your Key Vault Name>.vault.azure.net` | 443           | 資格情報が Azure Key Vault に格納されている場合に必要です。 |
| さまざまなドメイン | ドメインに依存          | SHIR の接続先である他のソースのドメイン。 |
  
> [!IMPORTANT]
> ほとんどの環境では、DNS が正しく構成されていることを確認する必要もあります。 確認するには、お使いの SHIR マシンから **nslookup** を使用して、上記の各ドメインへの接続を調べます。 各 nslookup から、リソースの IP が返される必要があります。 [プライベート エンドポイント](catalog-private-link.md)を使用している場合は、パブリック IP ではなく、プライベート IP が返される必要があります。 IP が返されない場合、またはプライベート エンドポイントを使用しているときにパブリック IP が返される場合は、DNS と VNET の関連付け、またはプライベート エンドポイントと VNET のピアリングに、対処する必要があります。

## <a name="manage-a-self-hosted-integration-runtime"></a>セルフホステッド統合ランタイムの管理

セルフホステッド統合ランタイムを編集するには、**管理センター** の **[統合ランタイム]** に移動し、IR を選択して [編集] を選択します。 これで、説明の更新、キーのコピー、新しいキーの再生成ができるようになります。

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime.png" alt-text="IR の編集":::

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime-settings.png" alt-text="IR の詳細の編集":::

セルフホステッド統合ランタイムを削除するには、管理センターの **[統合ランタイム]** に移動し、IR を選択して **[削除]** を選択します。 IR が削除されると、それに依存している実行中のスキャンは失敗します。

## <a name="java-runtime-environment-installation"></a>Java Runtime Environment のインストール

Purview でセルフホステッド統合ランタイムを使用して Parquet ファイルをスキャンする場合、Java Runtime Environment または OpenJDK をセルフホステッド IR マシンにインストールする必要があります。

セルフホステッド IR を使用して Parquet ファイルをスキャンすると、サービスによって、最初に JRE のレジストリ *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* がチェックされます。このレジストリが見つからない場合、OpenJDK のシステム変数 *`JAVA_HOME`* が続いてチェックされます。これらのチェックにより、Java ランタイムが検出されます。

- **JRE を使用する場合**:64 ビット IR には 64 ビット JRE が必要です。 [こちら](https://go.microsoft.com/fwlink/?LinkId=808605)から入手できます。
- **OpenJDK の使用方法**:IR バージョン 3.13 以降でサポートされています。 jvm.dll を他のすべての必要な OpenJDK のアセンブリと共にセルフホステッド IR マシンにパッケージ化し、それに応じてシステム環境変数 JAVA_HOME を設定します。

## <a name="proxy-server-considerations"></a>プロキシ サーバーに関する考慮事項

企業ネットワーク環境でプロキシ サーバーを使用してインターネットにアクセスする場合は、適切なプロキシ設定を使用するようにセルフホステッド統合ランタイムを構成します。 プロキシは、初期登録フェーズ中に設定できます。

:::image type="content" source="media/manage-integration-runtimes/self-hosted-proxy.png" alt-text="プロキシを指定する":::

構成されると、セルフホステッド統合ランタイムはプロキシ サーバーを使用してクラウド サービスのソースおよびコピー先 (HTTP または HTTPS プロトコルを使用しているもの) に接続します。 初期セットアップ時に **[変更] リンク** を選択したのはこのためです。

:::image type="content" source="media/manage-integration-runtimes/set-http-proxy.png" alt-text="プロキシを設定する":::

3 つの構成オプションがあります。

- **プロキシを使用しない**:セルフホステッド統合ランタイムは、クラウド サービスに接続するときにプロキシを明示的には使用しません。
- **システム プロキシを使用する**:セルフホステッド統合ランタイムでは、diahost.exe.config と diawp.exe.config で構成されているプロキシ設定を使用します。これらのファイルでプロキシ構成が指定されていない場合、セルフホステッド統合ランタイムではプロキシを経由せず、直接クラウド サービスに接続します。
- **カスタム プロキシを使用する**:diahost.exe.config と diawp.exe.config の構成は使用せず、セルフホステッド統合ランタイムで使用するように HTTP プロキシ設定を構成します。 **[アドレス]** と **[ポート]** の値は必須です。 **[ユーザー名]** と **[パスワード]** の値は、プロキシの認証設定によっては省略できます。 すべての設定は Windows DPAPI を使用して、自己ホスト型統合ランタイム上で暗号化され、コンピューターにローカルに格納されます。

統合ランタイムのホスト サービスは、更新済みのプロキシ設定を保存した後に自動的に再起動されます。

セルフホステッド統合ランタイムが登録された後、プロキシ設定を表示または更新する必要がある場合は、Microsoft Integration Runtime 構成マネージャーを使用します。

1. **Microsoft Integration Runtime 構成マネージャー** を開きます。
3. **[HTTP プロキシ]** の **[変更]** リンクを選択して、 **[HTTP プロキシを設定する]** ダイアログ ボックスを開きます。
4. **[次へ]** を選択します。 その後、プロキシ設定を保存して統合ランタイムのホスト サービスを再起動するためのアクセス許可を求める警告が表示されます。

構成マネージャー ツールを使用して、HTTP プロキシを表示して更新することができます。

> [!NOTE]
> NTLM 認証でプロキシ サーバーを設定する場合は、ドメイン アカウントで統合ランタイムのホスト サービスが実行されます。 ドメイン アカウントのパスワードを後で変更する場合は、忘れずにサービスの構成設定を更新し、サービスを再起動してください。 この要件のため、パスワードを頻繁に更新する必要がない専用のドメイン アカウントを使用して、プロキシ サーバーにアクセスすることをお勧めします。

## <a name="installation-best-practices"></a>インストールのベスト プラクティス

セルフホステッド統合ランタイムのインストールは、マネージド ID セットアップ パッケージを [Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=39717)からダウンロードして実行できます。

- コンピューターが休止状態にならないように、セルフホステッド統合ランタイム用のホスト コンピューターの電源プランを構成します。 ホスト コンピューターが休止状態になると、セルフホステッド統合ランタイムはオフラインになります。
- 定期的に、セルフホステッド統合ランタイムに関連付けられている資格情報をバックアップします。

## <a name="next-steps"></a>次のステップ

- [削除されたアセットをスキャンが検出する方法](concept-scans-and-ingestion.md#how-scans-detect-deleted-assets)

- [Purview でプライベート エンドポイントを使用する](catalog-private-link.md)
