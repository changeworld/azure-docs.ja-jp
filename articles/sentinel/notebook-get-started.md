---
title: Microsoft Sentinel で Jupyter Notebook と MSTICPy を使い始める
description: Microsoft Sentinel ML Notebooks のファースト ステップ ガイドを利用して、MSTICPy とクエリを使った Microsoft Sentinel ノートブックの基本を説明します。
services: sentinel
author: batamig
ms.author: bagol
ms.topic: how-to
ms.date: 11/09/2021
ms.openlocfilehash: 35912f00173b1e3072dffc2b899e003a8c2767d8
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132712720"
---
# <a name="tutorial-get-started-with-jupyter-notebooks-and-msticpy-in-microsoft-sentinel"></a>チュートリアル: Microsoft Sentinel での Jupyter Notebook と MSTICPy の概要

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

このチュートリアルでは、**Microsoft Sentinel ML Notebooks のファースト ステップ ガイド** のノートブックを実行して、Microsoft Sentinel で Jupyter Notebook を実行し、シンプルなデータ クエリを実行するための基本構成を設定する方法について説明します。

**Microsoft Sentinel ML Notebooks のファースト ステップ ガイド** のノートブックにより、Microsoft が作成した Cybersecurity ツールの Python ライブラリである MSTICPy が使用され、脅威ハンティングと調査機能を使用できます。

MSTICPy を使用すると、Microsoft Sentinel 用に記述する必要があるコードの量が減り、以下を行えるようになります。

- データ クエリ機能 (Microsoft Sentinel テーブル、Microsoft Defender for Endpoint、Splunk、およびその他のデータ ソースに対する機能)。
- VirusTotal や AlienVault OTX などの TI プロバイダーを使用した脅威インテリジェンスの検索。
- IP アドレスの位置情報、侵害インジケーター(IoC) 抽出、WhoIs 参照などのエンリッチメント関数。
- イベント タイムライン、プロセス ツリー、geo マッピングを使用した視覚化ツール。
- 時系列分解、異常検出、クラスタリングなどの高度な分析。

このチュートリアルの手順で、Microsoft Sentinel を介して、ご自分の Azure ML ワークスペースで **Microsoft Sentinel ML Notebooks のファースト ステップ ガイド** のノートブックを実行する方法について説明します。 このチュートリアルは、ローカルを含む、他の環境でノートブックを実行する同様の手順を実行するためのガイダンスとして使用することもできます。

詳細については、「[ノートブックを使用して調査を支援する](hunting.md#use-notebooks-to-power-investigations)」および「[Jupyter Notebook を使用してセキュリティの脅威を検出する](notebooks.md)」をご覧ください。

> [!NOTE]
> **Credential Scanner** ノートブック、または PowerShell サンプルや C# サンプルなど、一部の Microsoft Sentinel ノートブックでは MSTICPy を使用しません。 MSTICpy を使用しないノートブックでは、ここで説明されている MSTICPy 構成は必要ありません。
>

## <a name="prerequisites"></a>前提条件

- Microsoft Sentinel でノートブックを使用するには、必要な権限を持っていることを確認します。 詳細については、「[Microsoft Sentinel ノートブックへのアクセスを管理する](notebooks.md#manage-access-to-microsoft-sentinel-notebooks)」をご覧ください。

- このチュートリアルの手順を実行するには、Python 3.6 以降が必要です。 Azure ML では、Python 3.8 カーネル (推奨) または Python 3.6 カーネルのいずれかを使用できます。

- このノートブックでは、IP アドレスについて [MaxMind GeoLite2](https://www.maxmind.com) 位置情報参照サービスを使用します。 MaxMind GeoLite2 サービスを使用するには、アカウント キーが必要です。 無料アカウントとキーのサインアップは、[Maxmind のサインアップ ページ](https://www.maxmind.com/en/geolite2/signup)で行えます。

- このノートブックでは、脅威インテリジェンス ソースとして [VirusTotal](https://www.virustotal.com) (VT) を使用します。 VirusTotal 脅威インテリジェンス検索を使用するには、VirusTotal アカウントと API キーが必要です。

    「[VirusTotal getting started (VirusTotal ファースト ステップ ガイド)](https://developers.virustotal.com/v3.0/reference#getting-started)」ページで、無料の VT アカウントにサインアップできます。 すでに VirusTotal のユーザーである場合、既存のキーを使用できます。

    > [!WARNING]
    > VT エンタープライズ キーを使用している場合、**msticpyconfig.yaml** ファイルではなく、Azure Key Vault に保管してください。 詳細については、MSTICPY ドキュメントの[ Key Vault のシークレットとしてシークレットを指定](https://msticpy.readthedocs.io/en/latest/getting_started/msticpyconfig.html#specifying-secrets-as-key-vault-secrets)する手順をご覧ください。
    >
    > 今すぐ Azure Key Vault を設定しない場合は、Azure Key Vault ストレージを設定できるようになるまで、無料アカウントにサインアップして使用してください。

## <a name="run-and-initialize-the-getting-started-guide-notebook"></a>ファースト ステップ ガイド ノートブックを実行する

この手順では、ノートブックを起動して MSTICpy を初期化する方法について説明します。

1. Microsoft Sentinel で、左側から **[ノートブック]** を選択します。

1. **[テンプレート]** タブで、**A Getting Started Guide For Microsoft Sentinel ML Notebooks\(Microsoft Sentinel ML Notebooks のファースト ステップ ガイド\)**  >  **[Save notebook]\(ノートブックを保存する\)** を選択し、ご自分の Azure ML ワークスペースに保存します。

    **[ノートブックの起動]** を選択して、ノートブックを実行します。 ノートブックには、次の一連のセルが含まれています。

    - *Markdown* セルには、ノートブックを使用するための手順が含まれたテキストとグラフィックスが含まれています。
    - *Code* セルには、ノートブック関数を実行する実行可能コードが含まれています。

    **コード セルの読み取りと実行**

    コード セルを順番に読み取って実行します。 セルをスキップしたり、順不同で実行したりすると、後でノートブックでエラーが発生する可能性があります。

    各セルの左側にある再生ボタンを選択して、各セルを実行します。 実行される関数によっては、セル内のコードが非常に迅速に実行される場合や、完了まで数秒かかる場合があります。

    実行すると、再生ボタンが読み込みスピナーに変わり、`Executing` の状態が経過時間とともにセルの下部に表示されます。

    > [!TIP]
    > 説明どおりにノートブックが動作していないように見える場合は、カーネルを再起動し、最初からノートブックを実行してください。 たとえば、**ファースト ステップ ガイド** ノートブック内のセルで実行に 1 分以上かかるものがある場合、カーネルを再起動して、ノートブックを再実行してみてください。
    >
    > **ファースト ステップ ガイド** ノートブックには、Jupyter カーネルの再起動など、Jupyter ノートブックの基本的な使用手順が含まれています。
    >

    **[What is a Jupyter Notebook (Jupyter Notebook の概要)]** セクションでセルの読み取りと実行が完了したら、構成タスクを開始し、 **[Setting up the notebook environment (ノートブック環境のセットアップ)]** セクションを開始できるようになります。

1. ノートブックの **[Setting up the notebook environment (ノートブック環境のセットアップ)]** セクションで、次のコードを含む、最初のコード セルを実行します。

    ```python
    # import some modules needed in this cell
    from pathlib import Path
    from IPython.display import display, HTML

    REQ_PYTHON_VER="3.6"
    REQ_MSTICPY_VER="1.2.3"

    display(HTML("Checking upgrade to latest msticpy version"))
    %pip install --upgrade --quiet msticpy[azuresentinel]>=$REQ_MSTICPY_VER

    # intialize msticpy
    from msticpy.nbtools import nbinit
    nbinit.init_notebook(
    namespace=globals(),
    extra_imports=["urllib.request, urlretrieve"]
    )
    pd.set_option("display.html.table_schema", False)
    ```

    初期化状態が出力に表示されます。 この時点ではまだ何も構成していないため、`Missing msticpyconfig.yaml` ファイル内の設定の欠落に関する構成警告が表示されます。

> [!NOTE]
> ほとんどの Microsoft Sentinel ノートブックは、以下を行う MSTICpy 初期化セルで始まります。
>
> - ノートブックに必要な Python と MSTICPy の最小バージョンを定義します。
> - 最新バージョンの MSTICPy がインストールされていることを確認します。
> - `init_notebook` 関数をインポートして実行します。
>

## <a name="create-your-configuration-file"></a>構成ファイルを作成する

基本的な初期化が完了したら、MSTICPy を操作するための基本設定で構成ファイルを作成できます。

多くの Microsoft Sentinel ノートブックは、[VirusTotal](https://www.virustotal.com) (VT) などの外部サービスに接続して、データを収集し、強化します。 これらのサービスに接続するには、認証トークンなどの構成の詳細を設定して保存する必要があります。 このデータを構成ファイルに保存すると、ノートブックを使用するたびに認証トークンとワークスペースの詳細を入力しなくて済みます。

MSTICPy は、さまざまな構成の詳細を保存するために、**msticpyconfig.yaml** を使用します。  既定では、**msticpyconfig.yaml** ファイルはノートブックの初期化関数によって生成されます。 [Microsoft Sentinel ポータルからこのノートブックを複製](#run-and-initialize-the-getting-started-guide-notebook)した場合、Microsoft Sentinel ワークスペースのデータがこの構成ファイルに取り込まれます。 このデータは、ノートブックの起動時に Azure ML ワークスペースで作成された **config.json** ファイルから読み取られます。 詳細については、[MSTICPy パッケージ構成に関するドキュメント](https://msticpy.readthedocs.io/en/latest/getting_started/msticpyconfig.html)をご覧ください。

次のセクションでは、**msticpyconfig.yaml** ファイルに構成の詳細をさらに追加する方法について説明します。

> [!NOTE]
> *ファースト ステップ ガイド* ノートブックをもう一度実行するときに、最小構成の **msticpyconfig.yaml** ファイルがすでにある場合は、`init_notebook` 関数によって既存のファイルが上書きされたり、変更されたりすることはありません。
>

> [!TIP]
> MSTICPy 構成ツールの **[-ヘルプ]** ドロップダウン メニューを選択して、詳細な説明や、詳細ドキュメントへのリンクをいつでも確認できます。
>

### <a name="display-the-msticpy-settings-editor"></a>MSTICPy 設定エディターを表示する

1. コード セルで次のコードを実行し、`MpConfigEdit` ツールをインポートして、**msticpyconfig.yaml** ファイルの設定エディターを表示します。

    ```python
    from msticpy.config import MpConfigEdit

    mpedit = MpConfigEdit( "msticpyconfig.yaml")
    mpedit.set_tab("AzureSentinel")
    display(mpedit)
    ```

    例:

    :::image type="content" source="media/notebook-get-started/msticpy-editor.png" alt-text="MSTICPy 設定エディターのスクリーンショット。":::

    自動的に作成された **msticpyconfig.yaml** ファイルが設定エディターに表示され、Microsoft Sentinel セクションに 2 つのエントリが含まれています。 どちらにも、ノートブックの複製元である Microsoft Sentinel ワークスペースの詳細データが取り込まれます。 一方のエントリには workspace という名前が付けられ、もう一方には **Default** という名前が付けられます。

    MSTICPy を使用して、複数の Microsoft Sentinel ワークスペースの構成を保存し、それらを切り替えることができます。 **Default** エントリを使用すると、明示的に名前を付けなくても、既定で "home" ワークスペースに対する認証を行えます。 さらにワークスペースを追加する場合、それらのいずれかが **Default** エントリになるように構成できます。

    > [!NOTE]
    > Azure ML 環境では、設定エディターが表示されるまで 10 ～ 20 秒かかる場合があります。

1. 現在の設定を確認し、 **[設定の保存]** を選択します。

### <a name="add-threat-intelligence-provider-settings"></a>脅威インテリジェンス プロバイダー設定を追加する

この手順では、**msticpyconfig.yaml** ファイルに [VirusTotal API キー](#prerequisites)を保存する方法について説明します。 API キーを Azure Key Vault にアップロードすることもできますが、最初に Key Vault の設定を構成する必要があります。 詳細については、「[Key Vault 設定を構成する](#configure-key-vault-settings)」をご覧ください。

**MSTICPy 設定エディターで VirusTotal の詳細を追加するには**:

1. コード セルに次のコードを入力し、次を実行します。

   ```python
   mpedit.set_tab("TI Providers")
   mpedit
   ```

1. **[TI プロバイダー]** タブで、 **[Add prov (プロバイダーの追加)]**  >  **[VirusTotal]**  >  **[追加]** を選択します。

1. **[認証キー]** の下で、 **[ストレージ]** オプションの横にある **[テキスト]** を選択します。

1. **[値]** フィールドに、API キーを貼り付けます。

1. **[更新]** を選択し、設定エディターの下部にある **[設定の保存]** を選択します。

> [!TIP]
> サポートされているその他の脅威インテリジェンス プロバイダーの詳細については、MSTICPy ドキュメントの「[Threat intelligence providers](https://msticpy.readthedocs.io/en/latest/data_acquisition/TIProviders.html)」 (脅威インテリジェンス プロバイダー) と「[Microsoft Sentinel への脅威インテリジェンスの統合](threat-intelligence-integration.md)」をご覧ください。
>
### <a name="add-geoip-provider-settings"></a>GeoIP プロバイダーの設定の追加

この手順では、[MaxMind GeoLite2 アカウント キー](#prerequisites)を **msticpyconfig.yaml** ファイルに保存する方法について説明します。これにより、ノートブックで IP アドレスについて位置情報検索サービスを使用できるようになります。

**MSTICPy 設定エディターで GeoIP プロバイダー設定を追加するには**:

1. 次のコードを空のコード セルに入力し、実行します。

   ```python
   mpedit.set_tab("GeoIP Providers")
   mpedit
   ```

1. **[GeoIP Providers (GeoIP プロバイダー)]** タブで、 **[Add prov (プロバイダーの追加)]**  >  **[GeoIPLite]**  >  **[追加]** を選択します。

1. **[値]** フィールドに、MaxMind アカウント キーを入力します。

1. 必要に応じて、ダウンロードした GeoIP データベースを保存するための、既定の **~/.msticpy** フォルダーを更新します。

    - Windows では、このフォルダーは **%USERPROFILE%/.msticpy** にマップされます。
    - Linux または macOS では、このパスはホーム フォルダー内の **.msticpy** フォルダーにマップされます。

> [!TIP]
> サポートされているその他の位置情報検索サービスの詳細については、[MSTICPy GeoIP プロバイダーのドキュメント](https://msticpy.readthedocs.io/en/latest/data_acquisition/GeoIPLookups.html)をご覧ください。
>

### <a name="configure-azure-cloud-settings"></a>Azure Cloud 設定を構成する

組織で Azure パブリック クラウドを使用していない場合は、Microsoft Sentinel と Azure のデータを正常に認証して使用するために、設定でこれを指定する必要があります。 詳細については、「[Azure クラウドと既定の Azure 認証方法を指定する](#specify-the-azure-cloud-and-azure-authentication-methods)」をご覧ください。

### <a name="validate-settings"></a>設定を検証する

設定エディターで **[設定を検証する]** を選択します。

構成が欠落していることに関する警告メッセージが想定されますが、脅威インテリジェンス プロバイダーまたは GeoIP プロバイダーの設定について欠落があってはなりません。

環境によっては、[Key Vault 設定を構成](#configure-key-vault-settings)したり、[Azure クラウドを指定](#specify-the-azure-cloud-and-azure-authentication-methods)したりする必要もあります。

検証によって変更を加える必要がある場合は、それらの変更を行い、 **[設定の保存]** を選択します。

完了したら、 **[閉じる]** ボタンを選択して、検証の出力を非表示にします。

詳細については、「[Microsoft Sentinel での Jupyter Notebook と MSTICPy の詳細な構成](notebooks-msticpy-advanced.md)」をご覧ください

## <a name="load-saved-msticpy-settings"></a>保存された MSTICPy 設定を読み込む

「[構成ファイルの作成](#create-your-configuration-file)」の手順で、ローカルの **msticpyconfig** ファイルに設定を保存しました。

ただし、カーネルを再起動するか別のノートブックを実行するまで、MSTICPy はこれらの設定を自動的には再読み込みしません。 MSTICPy で新しい構成ファイルから強制的に再読み込みを行うには、次のコード セルに進み、次のコードを使用して、実行します。

```python
import msticpy
msticpy.settings.refresh_config()
```

## <a name="test-your-notebook"></a>ノートブックをテストする

環境を初期化し、ワークスペースの基本設定を構成したので、MSTICPy `QueryProvider` クラスを使用して、ノートブックをテストします。 `QueryProvider` で、データ ソース (この場合は Microsoft Sentinel ワークスペース) に対してクエリを行い、クエリが行われたデータをノートブックでの表示と分析に使用できるようにします。

次の手順に従って、`QueryProvider` クラスのインスタンスを作成し、ノートブックから Microsoft Sentinel に対して認証を行って、さまざまなパラメーター オプションを設定してクエリを表示し、実行します。

> [!TIP]
> `QueryProvider` の複数のインスタンスを読み込んで、複数の Microsoft Sentinel ワークスペースや、Microsoft Defender for Endpoint などのその他のデータ プロバイダーで使用できます。
>

### <a name="load-the-queryprovider"></a>QueryProvider を読み込む

`AzureSentinel` の `QueryProvider` を読み込むには、次のセルに進み、次のコードを使用して、実行します。

```python
# Initialize a QueryProvider for Microsoft Sentinel
qry_prov = QueryProvider("AzureSentinel")
```

> [!NOTE]
> Microsoft Sentinel ドライバーを読み込んでいるときに、警告 `Runtime dependency of PyGObject is missing` が表示された場合は、「[Error: *Runtime dependency of PyGObject is missing*](https://github.com/Azure/Azure-Sentinel-Notebooks/wiki/%22Runtime-dependency-of-PyGObject-is-missing%22-error)」 (エラー: PyGObject のランタイム依存関係が欠落しています) を確認してください。
この警告は、ノートブックの機能には影響しません。
>

### <a name="authenticate-to-your-microsoft-sentinel-workspace-from-your-notebook"></a>ノートブックから Microsoft Sentinel ワークスペースに対して認証する

Azure 資格情報による[デバイス認可](../active-directory/develop/v2-oauth2-device-code.md)を使用して、ご自分の Microsoft Sentinel ワークスペースに対して認証を行います。

デバイスの承認では、認証プロセスの一環として指定する 1 回限りのデバイス コードを生成することによって、認証に別の要素を追加します。

**デバイス承認を使用して認証するには**:

1. デバイス コードを生成して表示するには、次のコード セルを実行します。

   ```python
   # Get the Microsoft Sentinel workspace details from msticpyconfig
   # Loading WorkspaceConfig with no parameters uses the details
   # of your Default workspace
   # If you want to connect to a specific workspace use this syntax:
   #    ws_config = WorkspaceConfig(workspace="WorkspaceName")
   # ('WorkspaceName' should be one of the workspaces defined in msticpyconfig.yaml)
   ws_config = WorkspaceConfig()

   # Connect to Microsoft Sentinel with your QueryProvider and config details
   qry_prov.connect(ws_config)
   ```

    例:

    :::image type="content" source="media/notebook-get-started/device-authorization.png" alt-text="デバイス認証コードを示すスクリーンショット。":::

1. 指定したコードを選択してクリップボードにコピーします。 次に、[https://microsoft.com/devicelogin](https://microsoft.com/devicelogin) に移動して、メッセージで指定された場所にコードを貼り付けます。

1. サインインしたことを示す確認メッセージが表示されたら、ブラウザーのタブを閉じて、Microsoft Sentinel のノートブックに戻ります。

   ノートブックには、次のような出力が表示されます。

   :::image type="content" source="media/notebook-get-started/authorization-complete.png" alt-text="デバイスの承認プロセスが完了したことを示すスクリーンショット。":::

**Azure CLI を使用してサインイン トークンをキャッシュする**

カーネルを再起動したり、別のノートブックを実行したりした場合に再認証が不要になるように、Azure CLI を使用してサインイン トークンをキャッシュできます。

コンピューティング インスタンスの Azure CLI コンポーネントは、トークンがタイムアウトになるまで再利用できる *更新トークン* をキャッシュします。MSTICPy は、使用可能な場合は、Azure CLI 資格情報を自動的に使用します。

Azure CLI を使用して認証するには、空のセルに以下を入力し、実行します。

```python
!az login
```

> [!NOTE]
> コンピューティング インスタンスを再起動するか、別のインスタンスに切り替える場合は、再認証を行う必要があります。 詳細については、Microsoft Sentinel ノートブック GitHub リポジトリ Wiki の「[Caching credentials with Azure CLI](https://github.com/Azure/Azure-Sentinel-Notebooks/wiki/Caching-credentials-with-Azure-CLI)」 (Azure CLI を使用した資格情報のキャッシュ) セクションをご覧ください。
>

### <a name="view-the-microsoft-sentinel-workspace-data-schema-and-built-in-msticpy-queries"></a>Microsoft Sentinel ワークスペースのデータ スキーマと組み込みの MSTICPy クエリを表示する

Microsoft Sentinel QueryProvider に接続した後、Microsoft Sentinel ワークスペース データ スキーマに対してクエリを実行すると、クエリに使用できるデータの型を理解できます。

Microsoft Sentinel QueryProvider には、スキーマ テーブルの一覧を提供する `schema_tables` プロパティと、各テーブルの列名とデータ型も含まれる `schema` プロパティがあります。

**Microsoft Sentinel スキーマ内の最初の 10 個のテーブルを表示するには**、次のようにします。

次のセルに進み、次のコードを入力して実行します。 `[:10]` を省略すると、ワークスペース内のすべてのテーブルを一覧表示できます。

```python
# Get list of tables in the Workspace with the 'schema_tables' property
qry_prov.schema_tables[:10]  # Output only a sample of tables for brevity
                             # Remove the "[:10]" to see the whole list
```

次のような出力が表示されます。

```output
Sample of first 10 tables in the schema
    ['AACAudit',
     'AACHttpRequest',
     'AADDomainServicesAccountLogon',
     'AADDomainServicesAccountManagement',
     'AADDomainServicesDirectoryServiceAccess',
     'AADDomainServicesLogonLogoff',
     'AADDomainServicesPolicyChange',
     'AADDomainServicesPrivilegeUse',
     'AADDomainServicesSystemSecurity',
     'AADManagedIdentitySignInLogs']
```

MSTICPy には、実行できる多数の組み込みクエリも含まれています。 `.list_queries()` で使用可能なクエリを一覧表示し、パラメーターとして組み込まれた疑問符 (`?`) を使用してクエリを呼び出すことによって、クエリに関する特定の詳細を取得します。 クエリ ブラウザーでクエリおよび関連するヘルプの一覧を表示することもできます。

**使用可能なクエリのサンプルを表示するには**:

次のセルに進み、次のコードを入力して実行します。 `[::5]` を省略して、すべてのクエリを一覧表示できます。

```python
# Get a sample of available queries
print(qry_prov.list_queries()[::5])  # showing a sample - remove "[::5]" for whole list
```

次のような出力が表示されます。

```output
Sample of queries
=================
['Azure.get_vmcomputer_for_host', 'Azure.list_azure_activity_for_account', 'AzureNetwork.az_net_analytics', 'AzureNetwork.get_heartbeat_for_ip', 'AzureSentinel.get_bookmark_by_id', 'Heartbeatget_heartbeat_for_host', 'LinuxSyslog.all_syslog', 'LinuxSyslog.list_logon_failures', 'LinuxSyslog.sudo_activity', 'MultiDataSource.get_timeseries_decompose', 'Network.get_host_for_ip','Office365.list_activity_for_ip', 'SecurityAlert.list_alerts_for_ip', 'ThreatIntelligence.list_indicators_by_filepath', 'WindowsSecurity.get_parent_process', 'WindowsSecurity.list_host_events','WindowsSecurity.list_hosts_matching_commandline', 'WindowsSecurity.list_other_events']
```

**パラメーターとして `?` を渡すことでクエリに関するヘルプを表示するには**:

```python
# Get help about a query by passing "?" as a parameter
qry_prov.Azure.list_all_signins_geo("?")
```

次のような出力が表示されます。

```output
Help for 'list_all_signins_geo' query
=====================================
Query:  list_all_signins_geo
Data source:  AzureSentinel
Gets Signin data used by morph charts

Parameters
----------
add_query_items: str (optional)
    Additional query clauses
end: datetime (optional)
    Query end time
start: datetime (optional)
    Query start time
    (default value is: -5)
table: str (optional)
    Table name
    (default value is: SigninLogs)
Query:
     {table} | where TimeGenerated >= datetime({start}) | where TimeGenerated <= datetime({end}) | extend Result = iif(ResultType==0, "Sucess", "Failed") | extend Latitude = tostring(parse_json(tostring(LocationDetails.geoCoordinates)).latitude) | extend Longitude = tostring(parse_json(tostring(LocationDetails.geoCoordinates)).longitude)
```

**スクロールとフィルターを行えるリストでテーブルとクエリの両方を表示するには**:

次のセルに進み、次のコードを入力して実行します。

```python
qry_prov.browse_queries()
```

選択したクエリについて、必須のパラメーターと省略可能なパラメーターがすべて、クエリのフルテキストと共に表示されます。 例:

:::image type="content" source="media/notebook-get-started/view-tables-queries-in-list.png" alt-text="スクロールとフィルターを行えるリストに表示されたテーブルとクエリのスクリーンショット。":::

詳細については、MSTICPy ドキュメントの「[Running a pre-defined query (事前定義されたクエリの実行)](https://msticpy.readthedocs.io/en/latest/data_acquisition/DataProviders.html#running-a-pre-defined-query)」をご覧ください。

> [!NOTE]
> ブラウザーからクエリを実行することはできませんが、各クエリの最後に例をコピーして貼り付けることで、ノートブック内の他の場所で実行できます。
>

### <a name="run-queries-with-time-parameters"></a>時間パラメーターを使用してクエリを実行する

ほとんどのクエリでは、時間パラメーターが必要です。 日付/時刻の文字列は入力するのが面倒であり、複数の場所で変更するとエラーが発生しやすくなります。

各クエリ プロバイダーには、クエリ用の既定の開始時刻と終了時刻のパラメーターがあります。 これらの時間パラメーターは、時間パラメーターが呼び出されるたびに、既定で使用されます。 既定の時間範囲を変更するには、`query_time` コントロールを開きます。 変更は、再度変更するまで有効になります。

次のセルに進み、次のコードを入力して実行します。

```python
# Open the query time control for your query provider
qry_prov.query_time
```

必要に応じて、`start` と `end` の時刻を設定します。 例:

:::image type="content" source="media/notebook-get-started/set-time-parameters.png" alt-text="クエリの既定の時間パラメーターを設定するスクリーンショット。":::

### <a name="run-a-query-using-the-built-in-time-range"></a>組み込みの時間範囲を使用してクエリを実行する

クエリ結果は、スプレッドシートやデータベース テーブルなどの表形式のデータ構造である、[Pandas DataFrame](https://pandas.pydata.org) として返されます。 [pandas functions](https://pandas.pydata.org/docs/user_guide/10min.html) を使用すると、クエリの結果に対して追加のフィルター処理や分析を実行できます。

次のコード セルは、クエリ プロバイダーの既定の時刻設定を使用してクエリを実行します。 この範囲を変更し、コード セルをもう一度実行して新しい時間範囲に対してクエリを実行できます。

```python
# The time parameters are taken from the qry_prov time settings
# but you can override this by supplying explict "start" and "end" datetimes
signins_df = qry_prov.Azure.list_all_signins_geo()

# display first 5 rows of any results
# If there is no data, just the column headings display
signins_df.head()
```

出力には、結果の最初の 5 行が表示されます。 例:

:::image type="content" source="media/notebook-get-started/run-query-with-built-in-time-range.png" alt-text="組み込みの時間範囲を使用して実行されるクエリのスクリーンショット。":::

データがない場合は、列見出しのみが表示されます。

### <a name="run-a-query-using-a-custom-time-range"></a>カスタムの時間範囲を使用してクエリを実行する

新しいクエリ時間オブジェクトを作成し、それをパラメーターとしてクエリに渡すこともできます。これにより、クエリ プロバイダーの既定値に影響を与えずに、別の時間範囲に対して 1 回限りのクエリを実行できます。

```python
# Create and display a QueryTime control.
time_range = nbwidgets.QueryTime()
time_range
```

目的の時間範囲を設定した後、時間範囲をクエリ関数に渡して、前のコードとは別のセルで次のコードを実行できます。

```python
signins_df = qry_prov.Azure.list_all_signins_geo(time_range)
signins_df.head()
```

また、`start` パラメーターと `end` パラメーターを使用して、Python datetime または日付時刻文字列として datetime の値を渡すこともできます。

```python
from datetime import datetime, timedelta
q_end = datetime.utc.now()
q_start = end – timedelta(5)
signins_df = qry_prov.Azure.list_all_signins_geo(start=q_start, end=q_end)
```

### <a name="customize-your-queries"></a>クエリをカスタマイズする

組み込みクエリをカスタマイズするには、クエリ ロジックを追加するか、`exec_query` 関数を使用して完全なクエリを実行します。

たとえば、ほとんどの組み込みクエリでは `add_query_items` パラメーターがサポートされています。このパラメーターを使用して、フィルターやその他の操作をクエリに追加できます。

1. 次のコード セルを実行して、アラート名別にアラートの数が要約されたデータ フレームを追加します。

    ```python
    from datetime import datetime, timedelta

    qry_prov.SecurityAlert.list_alerts(
       start=datetime.utcnow() - timedelta(28),
        end=datetime.utcnow(),
        add_query_items="| summarize NumAlerts=count() by AlertName"
    )
    ```

1. 完全な KQL クエリ文字列をクエリ プロバイダーに渡します。 接続されたワークスペースに対してクエリが実行され、panda DataFrame としてデータが返されます。 次を実行します。

    ```python
    # Define your query
    test_query = """
    OfficeActivity
    | where TimeGenerated > ago(1d)
    | take 10
    """

    # Pass the query to your QueryProvider
    office_events_df = qry_prov.exec_query(test_query)
    display(office_events_df.head())

    ```

詳細については、次を参照してください。

- [MSTICPy クエリのリファレンス](https://msticpy.readthedocs.io/en/latest/data_acquisition/DataQueries.html)
- [MSTICPy の定義済みクエリの実行](https://msticpy.readthedocs.io/en/latest/data_acquisition/DataProviders.html#running-an-pre-defined-query)

### <a name="test-virustotal-and-geolite2"></a>VirusTotal と GeoLite2 をテストする

**VirusTotal データの IP アドレスを確認するには**:

脅威インテリジェンスを使用して VirusTotal データに IP アドレスが表示されるかどうかを確認するには、次のコードを使用してセルを実行します。

```python
# Create your TI provider – note you can re-use the TILookup provider (‘ti’) for
# subsequent queries - you don’t have to create it for each query
ti = TILookup()

# Look up an IP address
ti_resp = ti.lookup_ioc("85.214.149.236")

ti_df = ti.result_to_df(ti_resp)
ti.browse_results(ti_df, severities="all")
```

出力には、結果の詳細が表示されます。 例:

:::image type="content" source="media/notebook-get-started/test-virustotal-ip.png" alt-text="VirusTotal データに表示される IP アドレスのスクリーンショット。":::

すべての結果を表示するには、下にスクロールしてください。 詳細については、「[Threat Intel Lookups in MSTICPy (MSTICPy での脅威インテリジェンス検索)](https://msticpy.readthedocs.io/en/latest/data_acquisition/TIProviders.html)」を参照してください。

**位置情報 IP 検索をテストするには**:

MaxMind サービスを使用して IP アドレスの地理位置情報の詳細を取得するには、次のコードを使用してセルを実行します。

```python
# create an instance of the GeoLiteLookup provider – this
# can be re-used for subsequent queries.
geo_ip = GeoLiteLookup()
raw_res, ip_entity = geo_ip.lookup_ip("85.214.149.236")
display(ip_entity[0])
```

出力には、IP アドレスの地理的位置情報が表示されます。 例:

```output
ipaddress
{ 'AdditionalData': {},
  'Address': '85.214.149.236',
  'Location': { 'AdditionalData': {},
                'CountryCode': 'DE',
                'CountryName': 'Germany',
                'Latitude': 51.2993,
                'Longitude': 9.491,
                'Type': 'geolocation',
                'edges': set()},
  'ThreatIntelligence': [],
  'Type': 'ipaddress',
  'edges': set()}
```

> [!NOTE]
> このコードを初めて実行するときに、そのデータベースをダウンロードする GeoLite ドライバーが表示されます。
>

詳細については、「[MSTICPy GeoIP Providers (MSTICPy GeoIP プロバイダー)](https://msticpy.readthedocs.io/en/latest/data_acquisition/GeoIPLookups.html)」をご覧ください。

## <a name="configure-key-vault-settings"></a>Key Vault の設定を構成する

このセクションは、Azure Key Vault にシークレットを保存する場合にのみ関連します。

Azure Key Vault にシークレットを保存する場合は、まず、[Azure グローバル KeyVault 管理ポータル](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.KeyVault%2Fvaults)で Key Vault を作成する必要があります。

必須の設定は、Vault のプロパティから取得するすべての値ですが、名前が異なる場合もあります。 例:

- **VaultName** は、Azure Key Vault の **[プロパティ]** 画面の左上に表示されます
- **TenantId** は、**ディレクトリ ID** として表示されます
- **AzureRegion** は、**場所** として表示されます
- **Authority** は、Azure サービスのクラウドです。

Vault からシークレットを取得するのに必要なのは、**VaultName**、**TenantId**、および **Authority** の値のみです。 その他の値は、MSTICPy から Vault を作成する場合に必要です。 詳細については、「[Specifying secrets as Key Vault secrets (Key Vault シークレットとしてシークレットを指定する)](https://msticpy.readthedocs.io/en/latest/getting_started/msticpyconfig.html#specifying-secrets-as-key-vault-secrets)」をご覧ください。

既定で **[Use KeyRing (KeyRing を使用)]** オプションが選択されており、Key Vault 資格情報をローカル KeyRing にキャッシュできます。 詳細については、[KeyRing のドキュメント](https://keyring.readthedocs.io/en/latest/index.html)をご覧ください。

> [!CAUTION]
> ノートブックが実行されているホスト コンピューティングを完全に信頼していない場合は、 **[Use KeyRing (KeyRing を使用)]** オプションを使用しないでください。
>
> この例では、*コンピューティング* はノートブックのカーネルが実行されている Jupyter ハブ サーバーであり、必ずしもブラウザーが実行されているコンピューターではありません。 Azure ML を使用している場合、*コンピューティング* は、選択した Azure ML コンピューティング インスタンスになります。 キーリングは、ノートブックのカーネルが実行されているホストでキャッシュを行います。
>

**MSTICPy 設定エディターで Key Vault 設定を追加するには**:

1. 次のセルに進み、次のコードを入力して実行します。

    ```python
    mpedit.set_tab("Key Vault")
    mpedit
    ```

1. Key Vault の Vault 詳細を入力します。 例:

    :::image type="content" source="media/notebook-get-started/add-kv-settings.png" alt-text="Key Vault セットアップ セクションのスクリーンショット":::

1. **[保存]** を選択してから、 **[設定の保存]** を選択します。

### <a name="test-key-vault"></a>Key Vault をテストする

Key vault をテストするには、シークレットに接続して表示できるかどうかを確認してください。 シークレットを追加していない場合、詳細は表示されません。 必要に応じて、テスト シークレットを Azure Key Vault ポータルから Vault に追加して、Microsoft Sentinel に表示されることを確認します。

例:

```Python
mpconfig = MpConfigFile()
mpconfig.refresh_mp_config()
mpconfig.show_kv_secrets()
```

> [!CAUTION]
> 保存したノートブックに出力を表示したままにしないでください。 出力に実際のシークレットが含まれている場合、ノートブックの **[Clear output (出力のクリア)]** コマンドを使用してから、ノートブックを保存してください。
>
> また、ノートブックのキャッシュされたコピーを削除します。 たとえば、notebook ディレクトリの **.ipynb_checkpoints** サブフォルダーを探し、見つかったこのノートブックのコピーを削除します。 消去された出力でノートブックを保存すると、チェックポイントのコピーが上書きされます。
>

Key Vault を構成した後、データ プロバイダー セクションと TI プロバイダー セクションの **Upload to KV (KV にアップロード)** ボタンを使用して、選択した設定を Vault に移動できます。 MSTICPy は、`TIProviders-VirusTotal-Args-AuthKey` などの設定のパスに基づいて、シークレットの既定の名前を生成します。

値が正常にアップロードされると、設定エディターの **[値]** フィールドの内容が削除され、基になる設定がプレースホルダー値に置き換えられます。 MSTICPy はこれを使用して、キーを取得しようとしたときに Key Vault パスを自動的に生成する必要があることを示します。

必要なシークレットがすでに Key Vault に保存されている場合は、 **[値]** フィールドにシークレット名を入力できます。 シークレットが既定の Vault ([[Key Vault](https://msticpy.readthedocs.io/en/latest/getting_started/SettingsEditor.html#key-vault)] セクションに指定された値) に保存されていない場合、**VaultName/SecretName** のパスを指定できます。

別のテナントの Vault から設定を取得する操作は、現在サポートされていません。 詳細については、「[Specifying secrets as Key Vault secrets (Key Vault シークレットとしてシークレットを指定する)](https://msticpy.readthedocs.io/en/latest/getting_started/msticpyconfig.html#specifying-secrets-as-key-vault-secrets)」をご覧ください。

## <a name="specify-the-azure-cloud-and-azure-authentication-methods"></a>Azure クラウドと Azure の認証方法を指定する

パブリックまたはグローバルの Azure クラウドではなく、ソブリンまたは government の Azure クラウドを使用している場合は、設定で適切なクラウドを選択する必要があります。 ほとんどの組織では、グローバル クラウドが既定値です。

これらの Azure 設定を使用して、Azure 認証の種類の既定の設定を定義することもできます。

**Azure クラウドと Azure の認証方法を指定するには**:

1. 次のセルに進み、次のコードを入力して実行します。

    ```python
    mpedit.set_tab("Azure")
    mpedit
    ```

1. 組織で使用されているクラウドを選択するか、 **[グローバル]** オプションで選択されている既定値を使用します。

1. 次の方法から、1 つまたは複数選択します。

    - **env** を使用して、環境変数に Azure 資格情報を保存します。
    - **msi** を使用して、管理対象サービス ID を使用します。これは、Jupyter ハブが実行されているホストまたは仮想マシンに割り当てられた ID です。 現在、MSI は Azure ML コンピューティング インスタンスではサポートされていません。
    - **cli** を使用して、認証された Azure CLI セッションからの資格情報を使用します。
    - **interactive** を使用して、[1 回限りのデバイス コード](#authenticate-to-your-microsoft-sentinel-workspace-from-your-notebook)を使用した対話型のデバイス承認フローを使用します。

    > [!TIP]
    > 大部分の場合、**cli** と **interactive** の両方など、複数の方法を選択することをお勧めします。 Azure 認証では、構成された各方法は、1 つが正常に完了するまで、上記の順序で試行されます。
    >

1. **[保存]** を選択してから、 **[設定の保存]** を選択します。

例:

:::image type="content" source="media/notebook-get-started/settings-for-azure-gov-cloud.png" alt-text="Azure Government クラウド用に定義された設定のスクリーンショット。":::

## <a name="next-steps"></a>次のステップ

この記事では、Microsoft Sentinel で MSTICPy を Jupyter Notebook と使用する方法の基本について説明しました。 詳細については、「[Microsoft Sentinel での Jupyter Notebook と MSTICPy の詳細な構成](notebooks-msticpy-advanced.md)」をご覧ください。

[Microsoft Sentinel ノートブック GitHub リポジトリ](https://github.com/Azure/Azure-Sentinel-Notebooks)に保存されている次のような他のノートブックを試してみることもできます。

- [Cybersec 機能のツアー](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/9bba6bb9007212fca76169c3d9a29df2da95582d/A%20Tour%20of%20Cybersec%20notebook%20features.ipynb)
- [Machine Learning の例](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/9bba6bb9007212fca76169c3d9a29df2da95582d/Machine%20Learning%20in%20Notebooks%20Examples.ipynb)
- ノートブックの [Entity Explorer シリーズ](https://github.com/Azure/Azure-Sentinel-Notebooks/)では、ホスト、アカウント、IP アドレス、およびその他のエンティティに関する詳細なドリルダウンを行えます。

> [!TIP]
> このチュートリアルで説明されているノートブックを別の Jupyter 環境で使用する場合は、Python 3.6 以降をサポートする任意のカーネルを使用できます。
>
> Microsoft Sentinel と Azure Machine Learning (ML) 以外で MSTICPy ノートブックを使用するには、Python 環境も構成する必要があります。 Anaconda ディストリビューションで Python 3.6 以降をインストールします。これには、必要なパッケージの多くが含まれています。
>

### <a name="more-reading-on-msticpy-and-notebooks"></a>MSTICPy とノートブックに関するその他の資料

次の表に、MSTICPy、Microsoft Sentinel、および Jupyter Notebook について学習するための参考資料を示します。

|サブジェクト  |その他の参考資料  |
|---------|---------|
|**MSTICPy**     |      - [MSTICPy パッケージ構成](https://msticpy.readthedocs.io/en/latest/getting_started/msticpyconfig.html)<br> - [MSTICPy 設定エディター](https://msticpy.readthedocs.io/en/latest/getting_started/SettingsEditor.html)<br>    - [ノートブック環境の構成](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/ConfiguringNotebookEnvironment.ipynb)<br>    - [MPSettingsEditor ノートブック](https://github.com/microsoft/msticpy/blob/master/docs/notebooks/MPSettingsEditor.ipynb) <br><br>**注**: `Azure-Sentinel-Notebooks` GitHub リポジトリには、テンプレートの *msticpyconfig.yaml* ファイルもあり、設定を理解できるように、セクションがコメントアウトされています。      |
|**Microsoft Sentinel と Jupyter Notebook**     |      - [Jupyter Notebook: 概要](https://realpython.com/jupyter-notebook-introduction/)<br>    - [MSTICPy のドキュメント](https://msticpy.readthedocs.io/)<br>    - [Microsoft Sentinel Notebooks のドキュメント](notebooks.md)<br>    - [Infosec Jupyterbook](https://infosecjupyterbook.com/introduction.html)<br>    - [Linux Host Explorer Notebook のチュートリアル](https://techcommunity.microsoft.com/t5/azure-sentinel/explorer-notebook-series-the-linux-host-explorer/ba-p/1138273)<br>    - [セキュリティ調査に Jupyter を使用する理由](https://techcommunity.microsoft.com/t5/azure-sentinel/why-use-jupyter-for-security-investigations/ba-p/475729)<br>    - [Microsoft Sentinel と Notebooks を使用したセキュリティ調査](https://techcommunity.microsoft.com/t5/azure-sentinel/security-investigation-with-azure-sentinel-and-jupyter-notebooks/ba-p/432921)<br>    - [Pandas のドキュメント](https://pandas.pydata.org/pandas-docs/stable/user_guide/index.html)<br>    - [Batch のドキュメント](https://docs.bokeh.org/en/latest/)       |
|     |         |
