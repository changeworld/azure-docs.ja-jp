---
title: Microsoft Sentinel での Jupyter Notebook と MSTICPy の詳細な構成 | Microsoft Docs
description: Microsoft Sentinel で作業するときに、Jupyter Notebook と MSTICPy で使用できる詳細な構成について説明します。
services: sentinel
author: batamig
ms.author: bagol
ms.topic: how-to
ms.date: 11/09/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 68274be9eb1cc45bf9b96454314246149e90c4ec
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132712682"
---
# <a name="advanced-configurations-for-jupyter-notebooks-and-msticpy-in-microsoft-sentinel"></a>Microsoft Sentinel での Jupyter Notebook と MSTICPy の詳細な構成

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

この記事では、Microsoft Sentinel で Jupyter Notebook と MSTICPy を操作するための詳細な構成について説明します。

詳細については、「[Jupyter のノートブックを使用してセキュリティの脅威を検出する](notebooks.md)」および「[チュートリアル: Microsoft Sentinel での Jupyter Notebook と MSTICPy の概要](notebook-get-started.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

この記事は、「[チュートリアル: Microsoft Sentinel での Jupyter Notebook と MSTICPy の概要](notebook-get-started.md)」の続きです。 以下で説明する高度な手順に進む前に、このチュートリアルを実行することをお勧めします。

## <a name="specify-authentication-parameters-for-azure-and-microsoft-sentinel-apis"></a>Azure および Microsoft Sentinel API の認証パラメーターを指定する

この手順では、**msticpyconfig.yaml** ファイルで Microsoft Sentinel および他の Azure API リソースの認証パラメーターを構成する方法について説明します。

**MSTICPy 設定エディターで Azure 認証と Microsoft Sentinel API の設定を追加するには**:

1. 次のセルに進み、次のコードを入力して実行します。

    ```python
    mpedit.set_tab("Data Providers")
    mpedit
    ```

1. **[Data Providers]\(データ プロバイダー\)** タブで、 **[AzureCLI]**  >  **[Add]\(追加\)** の順に選択します。

1. 使用する認証方法を選択します。

    - [Azure の既定値](notebook-get-started.md#specify-the-azure-cloud-and-azure-authentication-methods)とは異なる一連の方法を使用することもできますが、この使用方法は一般的な構成ではありません。
    - **env** (環境変数) 認証を使用する場合を除き、**clientId**、**tenantiId**、**clientSecret** の各フィールドは空のままにしておきます。
    - 推奨されていませんが、MSTICPy では、クライアント アプリ ID とシークレットを使用した認証もサポートされています。 この場合、 **[Data Providers]\(データ プロバイダー\)** タブで、**clientId**、**tenantId**、**clientSecret** の各フィールドを直接定義します。

1. **[Save File]\(ファイルの保存\)** を選択して、変更を保存します。

## <a name="define-autoloading-query-providers"></a>自動読み込みクエリ プロバイダーを定義する

`nbinit.init_notebook` 関数の実行時に、MSTICPy で自動的に読み込むクエリ プロバイダーを定義します。

新しいノートブックを頻繁に作成する場合、クエリ プロバイダーの自動読み込みにより、ピボット関数やノートブックレットなどの他のコンポーネントの前に必要なプロバイダーを確実に読み込むことができるため、時間を節約できます。

**自動読み込みクエリ プロバイダーを追加するには**:

1. 次のセルに進み、次のコードを入力して実行します。

    ```python
    mpedit.set_tab("Autoload QueryProvs")
    mpedit
    ```

1. **[Autoload QueryProv]\(QueryProv の自動読み込み\)** タブ:

   - Microsoft Sentinel プロバイダーの場合は、プロバイダー名と接続先のワークスペース名の両方を指定します。
   - 他のクエリ プロバイダーの場合は、プロバイダー名だけを指定します。

   各プロバイダーには、次の省略可能な値もあります。

   - **Auto-connect:** このオプションは、既定で **True** として定義されており、MSTICPy は読み込み直後にプロバイダーに対して認証を試みます。 MSTICPy では、設定でプロバイダーの資格情報が構成済みであることを前提としています。

   - **Alias:** MSTICPy では、プロバイダーを読み込むときに、プロバイダーを Python 変数名に割り当てます。 既定では、変数名は、Microsoft Sentinel プロバイダーの場合は **qryworkspace_name**、他のプロバイダーの場合は **qryprovider_name** になります。

        たとえば、*ContosoSOC* ワークスペースのクエリ プロバイダーを読み込むと、ノートブック環境にこのクエリ プロバイダーが `qry_ContosoSOC` という名前で作成されます。 より短い名前や、入力しやすく覚えやすい名前を使用したい場合は、別名を追加します。 プロバイダー変数名は `qry_<alias>` になります。`<alias>` は指定した別名に置き換えられます。

        このメカニズムによって読み込まれるプロバイダーは、たとえば、次のコードで使用されている、MSTICPy の `current_providers` 属性にも追加されます。

        ```python
        import msticpy
        msticpy.current_providers
        ```

1. **[Save Settings]\(設定の保存\)** を選択して変更を保存します。

## <a name="define-autoloaded-msticpy-components"></a>自動読み込み MSTICPy コンポーネントを定義する

この手順では、`nbinit.init_notebook` 関数の実行時に、MSTICPy によって自動的に読み込まれる他のコンポーネントを定義する方法について説明します。

サポートされているコンポーネントは次のとおりです (次の順序で読み込まれます)。

1. **TILookup:** [TI プロバイダー ライブラリ](notebook-get-started.md#add-threat-intelligence-provider-settings)
1. **GeoIP:** 使用する [GeoIP プロバイダー](notebook-get-started.md#add-geoip-provider-settings)
1. **AzureData:** [Azure リソース](#specify-authentication-parameters-for-azure-and-microsoft-sentinel-apis)の詳細のクエリに使用するモジュール
1. **AzureSentinelAPI:** [Microsoft Sentinel API](#specify-authentication-parameters-for-azure-and-microsoft-sentinel-apis) のクエリに使用するモジュール
1. **Notebooklets:** [msticnb パッケージ](https://msticnb.readthedocs.io/en/latest/)の Notebooklets
1. **Pivot:** ピボット関数

> [!NOTE]
> Pivot コンポーネントでは、エンティティにアタッチするピボット関数を見つけるために、クエリ プロバイダーと他のプロバイダーが読み込まれている必要があるため、コンポーネントはこの順序で読み込まれます。 詳細については、[MSTICPy のドキュメント](https://msticpy.readthedocs.io/en/latest/data_analysis/PivotFunctions.html)を参照してください。
>

**自動的に読み込まれる MSTICPy コンポーネントを定義するには**:

1. 次のセルに進み、次のコードを入力して実行します。

   ```python
   mpedit.set_tab("Autoload Components")
   mpedit
   ```

1. **[Autoload Components]\(コンポーネントの自動読み込み\)** タブで、必要に応じてパラメーター値を定義します。 次に例を示します。

   - **GeoIpLookup**。  使用する GeoIP プロバイダーの名前 (*GeoLiteLookup* または *IPStack*) を入力します。  詳細については、「[GeoIP プロバイダーの設定の追加](notebook-get-started.md#add-geoip-provider-settings)」を参照してください。

   - **AzureData および AzureSentinelAPI コンポーネント**。  次の値を定義します。

      - **auth_methods:** AzureCLI の既定の設定をオーバーライドし、選択した方法を使用して接続します。
      - **Auto-connect:** 接続せずに読み込むには、false に設定します。

      詳細については、「[Azure および Microsoft Sentinel API の認証パラメーターを指定する](#specify-authentication-parameters-for-azure-and-microsoft-sentinel-apis)」を参照してください。

   - **Notebooklets**。 **Notebooklets** コンポーネントには、**AzureSentinel** という単一のパラメーター ブロックがあります。

      `workspace:\<workspace name>` 構文を使用して、Microsoft Sentinel ワークスペースを指定します。 ワークスペース名は、 **[Microsoft Sentinel]** タブで定義されているワークスペースのいずれかである必要があります。

      `notebooklets init` 関数に送信するパラメーターをさらに追加する場合は、改行で区切って、キーと値のペアとして指定します。 次に例を示します。

      ```python
      workspace:<workspace name>
      providers=["LocalData","geolitelookup"]
      ```

      詳細については、[MSTICNB (MSTIC Notebooklets) のドキュメント](https://msticnb.readthedocs.io/en/latest/msticnb.html#msticnb.data_providers.init)を参照してください。

    **TILookup** や **Pivot** など、一部のコンポーネントではパラメーターは不要です。

1. **[Save Settings]\(設定の保存\)** を選択して変更を保存します。

## <a name="switch-between-python-36-and-38-kernels"></a>Python 3.6 カーネルと 3.8 カーネルを切り替える

Python 3.65 カーネルと 3.8 カーネルを切り替えると、MSTICPy や他のパッケージが期待どおりにインストールされない場合があります。

これは、`!pip install pkg` コマンドによって、最初の環境では正しくインストールされても、2 番目の環境では正しくインストールされない場合に発生する可能性があります。 これにより、2 番目の環境でパッケージをインポートまたは使用できない状況が発生します。

Azure ML ノートブックにパッケージをインストールするときに、`!pip install...` を使用しないことをお勧めします。 代わりに、次のいずれかのオプションを使用します。

- **ノートブック内で %pip 行マジックを使用する**。 次を実行します。

  ```python

  %pip install --upgrade msticpy
  ```

- **ターミナルからインストールする**:

  1. Azure ML ノートブックでターミナルを開き、次のコマンドを実行します。

     ``` bash
     conda activate azureml_py38
     pip install --upgrade msticpy
     ```

  1. ターミナルを閉じ、カーネルを再起動します。

## <a name="set-an-environment-variable-for-your-msticpyconfigyaml-file"></a>msticpyconfig.yaml ファイルの環境変数を設定する

Azure ML で実行していて、**msticpyconfig.yaml** ファイルがユーザー フォルダーのルートにある場合、MSTICPy によってこれらの設定が自動的に検出されます。 ただし、ノートブックを別の環境で実行している場合は、このセクションの手順に従って、構成ファイルの場所を参照する環境変数を設定します。

環境変数に **msticpyconfig.yaml** ファイルのパスを定義すると、ファイルを既知の場所に保存し、常に同じ設定を読み込むことができるようになります。

ノートブックごとに異なる設定を使用する場合は、複数の構成ファイルと複数の環境変数を使用します。

1. **~/.msticpyconfig.yaml** や **%userprofile%/msticpyconfig.yaml** など、**msticpyconfig.yaml** ファイルの場所を決定します。

    **Azure ML ユーザー**: 構成ファイルを Azure ML ユーザー フォルダーに保存すると、MSTICPy の `init_notebook` 関数 (初期化セルで実行) によってファイルが自動的に検出され、使用されるので、**MSTICPYCONFIG** 環境変数を設定する必要はありません。

    ただし、ファイルにシークレットも保存されている場合は、構成ファイルをコンピューティング ローカル ドライブに保存することをお勧めします。 コンピューティング内部ストレージにアクセスできるのは、コンピューティングを作成したユーザーだけですが、共有ストレージには、Azure ML ワークスペースへのアクセス権を持つすべてのユーザーがアクセスできます。

    詳細については、「[Azure Machine Learning コンピューティング インスタンスとは](../machine-learning/concept-compute-instance.md)」を参照してください。

1. 必要に応じて、**msticpyconfig.yaml** ファイルを選択した場所にコピーします。

1. その場所を参照するように **MSTICPYCONFIG** 環境変数を設定します。

次のいずれかの手順を使用して、**MSTICPYCONFIG** 環境変数を定義します。

# <a name="windows"></a>[Windows](#tab/windows)

Windows システムで **MSTICPYCONFIG** 環境変数を設定する場合:

1. 必要に応じて、**msticpyconfig.yaml** ファイルをコンピューティング インスタンスに移動します。
1. **[システムのプロパティ]** ダイアログ ボックスを開き、 **[詳細設定]** タブに移動します。
1. **[環境変数]** を選択して、 **[環境変数]** ダイアログを開きます。
1. **[システム環境変数]** 領域で **[新規]** を選択し、次のように値を定義します。

    - **[変数名]** : `MSTICPYCONFIG` として定義します
    - **[変数値]** : **msticpyconfig.yaml** ファイルのパスを入力します

# <a name="linux"></a>[Linux](#tab/linux)

この手順では、Linux システムで **.bashrc** ファイルを更新して、**MSTICPYCONFIG** 環境変数を設定する方法を説明します。

1. 必要に応じて、**msticpyconfig.yaml** ファイルをコンピューティング インスタンスに移動します。

1. Microsoft Sentinel の **Notesbooks** ページなどから、Azure ML ターミナルを開きます。

1. **msticpyconfig.yaml** ファイルにアクセスできることを確認します。

   Azure ML ターミナルでは、コンピューティング Linux システムにマウントされた Azure ML ファイル ストアのホーム ディレクトリが現在のディレクトリになっています。 プロンプトは次の例のようになります。

   ```python
   azureuser@alicecontoso-azml7:~/cloudfiles/code/Users/alicecontoso$
   ```

   「`ls`」と入力して、**msticpyconfig.yaml** ファイルなど、ホーム ディレクトリ内のすべてのファイルを一覧表示します。

1. **msticpyconfig.yaml** ファイルをコンピューティング ファイル ストアに移動するには、次のように入力します。

   ```python
   mv msticpyconfig.yaml ~
   ```

1. 次のいずれかのプロセスを使用して、環境変数の **.bashrc** ファイルを編集します。

    |コマンド  |手順  |
    |---------|---------|
    |**vim**     |     1. `vim ~/.bashrc` を実行します。 <br>2. **Shift + G キー** > **End キー** の順に押して、ファイルの末尾に移動します。 3. 「**a**」と入力し、**Enter** キーを押して、新しい行を作成します。 <br>4. 環境変数を追加し、**Esc** キーを押してコマンド モードに戻ります。 <br>5. 「 **:wq**」と入力してファイルを保存します。    |
    |**nano**     |           1. `nano ~/.bashrc` を実行します。<br>        2. **Alt + /** キーまたは **OPTION + /** キーを押して、ファイルの末尾に移動します。<br>        3. 環境変数を追加し、ファイルを保存します。 **Ctrl + X** キーを押し、**Y** キーを押します。      |
    |     |         |

    次のいずれかの環境変数を追加します。

    - **msticpyconfig.yaml** ファイルを移動した場合は、`export MSTICPYCONFIG=~/msticpyconfig.yaml` を実行します。
    - **msticpyconfig.yaml** ファイルを移動しなかった場合は、`export MSTICPYCONFIG=~/cloudfiles/code/Users/<YOURNAME>/msticpyconfig.yaml` を実行します。

# <a name="azure-ml-options"></a>[Azure ML オプション](#tab/azure-ml)

Azure ML ユーザー フォルダー以外の場所に **msticpyconfig.yaml** ファイルを保存する必要がある場合は、次のいずれかのオプションを使用します。

- **ユーザー フォルダーのルートにある *nbuser_settings.py* ファイル**。  このプロセスは、**kernel.json** ファイルを編集するよりも簡単で介入も少なくなりますが、ノートブック コードの先頭で `init_notebook` 関数を実行する場合にのみサポートされます。 これが既定の動作ですが、最初に `init_notebook` を実行せずにノートブック コードを実行すると、MSTICPy が構成ファイルを見つけることができない場合があります。

    1. Azure ML ターミナルで、ユーザー フォルダー (ユーザー名が付けられたフォルダー) のルートに **nbuser_settings.py** ファイルを作成します。
    1. **nbuser_settings.py** ファイルに、次の行を追加します。

        ```python
          import os
          os.environ["MSTICPYCONFIG"] = "~/msticpyconfig.yaml"
        ```

    このファイルは `init_notebook` 関数によって自動的にインポートされ、現在のノートブックの `MSTICPYCONFIG` 環境変数を設定します。

- **Python カーネルの *kernel.json* ファイル**。 ノートブックを手動で実行し、最初に `init_notebook` 関数を呼び出さない可能性がある場合は、この手順を使用します。

  Python 3.6 と Python 3.8 のカーネルがあります。 両方のカーネルを使用する場合は、両方のファイルを編集します。

  - **Python 3.8 の場所**: */usr/local/share/jupyter/kernels/python38-azureml/kernel.json*
  - **Python 3.6 の場所**: */usr/local/share/jupyter/kernels/python3-azureml/kernel.json*

  **kernel.json** ファイルで環境変数を設定するには:

  1. **kernel.json** ファイルのコピーを作成し、エディターで元のファイルを開きます。 `sudo` を使用して **kernel.json** ファイルを上書きすることが必要な場合があります。ファイルの内容は次の例のようになります。

      ```python
      {
         "argv": [
         "/anaconda/envs/azureml_py38/bin/python",
         "-m",
         "ipykernel_launcher",
         "-f",
         "{connection_file}"
         ],
         "display_name": "Python 3.8 - AzureML",
         "language": "python"
      }
      ```

  1. `"language"` 項目の後に、`"env": { "MSTICPYCONFIG": "~/msticpyconfig.yaml" }` 行を追加します。

      `"language": "python"` 行の末尾に必ずコンマを追加してください。 次に例を示します。

      ```python
      {
          "argv": [
          "/anaconda/envs/azureml_py38/bin/python",
          "-m",
          "ipykernel_launcher",
          "-f",
          "{connection_file}"
          ],
          "display_name": "Python 3.8 - AzureML",
          "language": "python",
          "env": { "MSTICPYCONFIG": "~/msticpyconfig.yaml" }
      }
      ```

---

> [!NOTE]
> Linux および Windows オプションでは、定義した環境変数が選択されるように、Jupyter サーバーを再起動する必要があります。

## <a name="next-steps"></a>次のステップ

詳細については、次を参照してください。

|サブジェクト  |その他の参考資料  |
|---------|---------|
|**MSTICPy**     |      - [MSTICPy パッケージ構成](https://msticpy.readthedocs.io/en/latest/getting_started/msticpyconfig.html)<br> - [MSTICPy 設定エディター](https://msticpy.readthedocs.io/en/latest/getting_started/SettingsEditor.html)<br>    - [ノートブック環境の構成](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/ConfiguringNotebookEnvironment.ipynb)<br>    - [MPSettingsEditor ノートブック](https://github.com/microsoft/msticpy/blob/master/docs/notebooks/MPSettingsEditor.ipynb) <br><br>**注**: Azure-Sentinel-Notebooks GitHub リポジトリには、コメントアウトされたセクションを含むテンプレート *msticpyconfig.yaml* ファイルも含まれており、設定を理解するのに役立ちます。      |
|**Microsoft Sentinel と Jupyter Notebook**     |      - [Jupyter Notebook: 概要](https://realpython.com/jupyter-notebook-introduction/)<br>    - [MSTICPy のドキュメント](https://msticpy.readthedocs.io/)<br>    - [Microsoft Sentinel Notebooks のドキュメント](notebooks.md)<br>    - [Infosec Jupyterbook](https://infosecjupyterbook.com/introduction.html)<br>    - [Linux Host Explorer Notebook のチュートリアル](https://techcommunity.microsoft.com/t5/azure-sentinel/explorer-notebook-series-the-linux-host-explorer/ba-p/1138273)<br>    - [セキュリティ調査に Jupyter を使用する理由](https://techcommunity.microsoft.com/t5/azure-sentinel/why-use-jupyter-for-security-investigations/ba-p/475729)<br>    - [Microsoft Sentinel と Notebooks を使用したセキュリティ調査](https://techcommunity.microsoft.com/t5/azure-sentinel/security-investigation-with-azure-sentinel-and-jupyter-notebooks/ba-p/432921)<br>    - [Pandas のドキュメント](https://pandas.pydata.org/pandas-docs/stable/user_guide/index.html)<br>    - [Batch のドキュメント](https://docs.bokeh.org/en/latest/)       |
|     |         |
