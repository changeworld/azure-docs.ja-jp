---
title: アプリケーション インストール テンプレートを生成する
description: Azure 上の SAP デプロイ自動化フレームワークで使用する SAP アプリケーション テンプレートを生成する方法。
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: how-to
ms.service: virtual-machines-sap
ms.openlocfilehash: 7d9ade08ce2e07c6a93866ea137eca558e9d0c19
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725226"
---
# <a name="generate-sap-application-templates-for-automation"></a>自動化のための SAP アプリケーション テンプレートを生成する

[Azure 上の SAP デプロイ自動化フレームワーク](automation-deployment-framework.md)では、部品表 (BOM) を使用して SAP アプリケーションを定義します。 カスタム BOM を使用してシステムをデプロイする前に、無人 SAP インストールで使用される ini ファイル用のテンプレートも作成する必要があります。 このガイドでは、SAP/S4 デプロイ用のアプリケーション テンプレートを作成する方法について説明します。 プロセスは、他の SAP アプリケーションでも同じです。

## <a name="prerequisites"></a>前提条件

- まだ行っていない場合は、[SAP インストール メディアと関連ファイルをダウンロードして準備します](automation-bom-get-files.md)。 [ダウンロードした SAPCAR ユーティリティ ファイルの名前](automation-bom-get-files.md#acquire-media)を控えておいてください。
- まだ行っていない場合は、[BOM を準備します](automation-bom-prepare.md)。 作成した BOM ファイルを使用できる状態にしておきます。
- Azure サブスクリプション。 Azure サブスクリプションをまだお持ちでない場合は、[無料のアカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成できます。
- 使用するデータベースを操作するためのアクセス許可を持つ SAP アカウント。
- 必要に応じて、ストレージ アカウントから SAP メディアを転送するために使用する仮想マシン (VM) を Azure 内に作成します。 この方法により、転送速度が向上します。 VM とターゲット SAP VM の間に接続が確立されていることを確認します。 たとえば、SSH キーが配置されていることを確認します。

## <a name="check-media-and-tools"></a>メディアとツールを確認する

SAP アプリケーション テンプレートを生成する前に、必要なすべてのインストール メディアとツールがある必要があります。

1. ターゲット VM にルート ユーザーとしてサインインします。

1. ルート ユーザーのパスワードを既知の値に変更します。 このパスワードは後で SAP Software Provisioning Manager (SWPM) に接続するために使用します。

1. 一時ディレクトリを作成してそこに移動します。

    ```bash
    mkdir /tmp/workdir; cd $_
    ```

1. SAP アプリケーション テンプレート用の一時ディレクトリがあることを確認します。

    ```bash
    mkdir /tmp/app_template/
    ```

1. SAPCAR ユーティリティのアクセス許可を変更して、このファイルを実行可能にします。 `<SAPCAR>.EXE` をダウンロードしたファイルの名前に置き換えます。 たとえば、`SAPCAR_1311-80000935.EXE` のようにします。

    ```bash
    chmod +x /usr/sap/install/download_basket/<SAPCAR>.EXE
    ```

1. SWPM のインストール フォルダーが存在することを確認します。

    ```bash
    mkdir -p /usr/sap/install/SWPM
    ```

1. SAPCAR ユーティリティを使用して SWPM インストール ファイルを解凍します。

```bash
/usr/sap/install/download_basket/SAPCAR_1311-80000935.EXE -xf /usr/sap/install/SWPM20SP07_0-80003424.SAR -R /usr/sap/install/SWPM/
```

パラメーター ファイルを使用して SAP の無人インストールを実行できます。 これらのファイルから、必要なすべてのパラメーターが SWPM インストーラーに渡されます。 

> [!NOTE]
> パラメーター ファイルを生成するには、手動インストールを部分的に実行する必要があります。 理由については、[SAP NOTE 2230669](https://launchpad.support.sap.com/#/notes/2230669) を参照してください。

## <a name="generate-ascs-parameter-file"></a>ASCS パラメーター ファイルを生成する

ASCS の無人インストール用パラメーター ファイルを生成するには、次の手順を実行します。

1. コマンド ライン インターフェイス (CLI) を使用して、ルート ユーザーとして VM にサインインします。

1. `hostname` コマンドを実行して、インストールを実行している VM のホスト名を取得します。 一意のホスト名 (例の出力では `<example-vm-hostname>` になっています) と GUI の完全な URL の両方をメモします。
   

1. [必要なすべてのメディアとツールが VM にインストールされていることを確認します](#check-media-and-tools)。

1. 次のようにして SWPM を起動します。 

    1. `<target-VM-hostname>` を前に取得したホスト名に置き換えます。

    1. `<XML-stack-file-path>` を作成した XML スタック ファイルのパスに置き換えます。 たとえば、`/usr/sap/install/config/MP_STACK_S4_2020_v001.xml` のようにします。

    ```bash
    /usr/sap/install/SWPM/sapinst                      \
    SAPINST_XML_FILE=<XML-stack-file-path>.xml         \
    SAPINST_USE_HOSTNAME=<target-VM-hostname>
    SAPINST_START_GUISERVER=true \
    SAPINST_STOP_AFTER_DIALOG_PHASE=true 
    
    ```

    ```output
    Connecting to the ASCS VM to launch
    ********************************************************************************
    Open your browser and paste the following URL address to access the GUI
    https://<example-VM-hostname>.internal.cloudapp.net:4237/sapinst/docs/index.html
    Logon users: [root]
    ********************************************************************************
    ```


1. ブラウザーを開き、前に取得した GUI の URL にアクセスします。

    1. セキュリティ リスクの警告を受け入れます。
    
    1. システムのルート ユーザーの資格情報を使用して認証します。
    
1. ドロップダウン メニューで、 **[SAP S/4HANA サーバー 2020]** &gt; **[SAP HANA データベース]** &gt; **[インストール]** &gt; **[アプリケーション サーバー ABAP]** &gt; **[分散システム]** &gt; **[ASCS インスタンス]** を選択します。
        
1. **[パラメーター モード]** で、 **[カスタム]** を選択します。 次に、 **[次へ]** を選択します。

1. SAP システム設定を構成します。

    1. SAP システム ID が `{SID}` である必要があります。
    
    1. SAP マウント ディレクトリの値が `/sapmnt` である必要があります。
    
    1. **[次へ]** を選択します。

1. 完全修飾ドメイン名 (FQDN) 設定を構成します。 

    1. FQDN 値が自動的に事前設定されることを確認します。
    
    1. **[SAP システムの FQDN の設定]** を有効にしたことを確認します。
    
    1. **[次へ]** を選択します。

1. この ASCS インスタンスの作成時にのみ使用するメイン パスワードを設定します。 パスワードには、英数字と特殊文字 (`#`、`$`、`@`、`_`) のみを使用できます。 最初の文字として数字やアンダースコアを使用することはできません。

    1. メイン パスワードを入力します。

    1. メイン パスワードを確認します。

    1. **[次へ]** を選択します。 

1. その他の管理者設定を構成します。 他のパスワード フィールドは、設定したメイン パスワードに基づいて事前設定されます。

    1. 管理者 OS ユーザー (`<sid>adm`、ここで `<sid`> は実際の SID です) の ID を `2000` に設定します。

    1. SAP システム (`sapsys`) の ID を `2000` に設定します。

    1. **[次へ]** を選択します。

1. SAPEXE カーネル ファイル パスの入力を求めるメッセージが表示されたら、「`/usr/sap/install/download_basket`」と入力して、 **[次へ]** を選択します。

1. パッケージの状態が **[使用可能]** であることを確認して、 **[次へ]** を選択します。

1. SAP Host Agent のインストール ファイルの状態が **[使用可能]** であることを確認して、 **[次へ]** を選択します。

1. SAP 管理者 OS ユーザーの情報を指定します。

    1. パスワードは、メイン パスワードから継承されたままにします。

    1. OS ユーザーの ID を `2100` に設定します。

    1. **[次へ]** を選択します。

1. インストールの設定を確認します。

    1. インストールのインスタンス番号が正しいことを確認します。

    1. インスタンスの仮想ホスト名を設定したことを確認します。

    1. **[次へ]** を選択します。

1.  ABAP メッセージ サーバーのポート設定を維持します。 これらの既定の設定は `3600` と `3900` です。 **[次へ]** を選択します。

1. インストールする他のコンポーネントを選択しない場合は、 **[次へ]** を選択します。

1. **[セキュリティ パラメーターの設定をスキップする]** を有効にして、 **[次へ]** を選択します。

1. **[はい、オペレーティング システム ユーザーをクリーンアップします]** を有効にして、 **[次へ]** を選択します。

1. **[パラメーターの概要]** では、まだ何もしないでください。

1. CLI で、SAP インストールの一時ディレクトリでインストール構成ファイルを探します。 この時点では、ファイルは `inifile.params` と呼ばれます。

    1. `ls /tmp/sapinst_instdir/` を実行して、SAP インストール ディレクトリ内のファイルを一覧表示します。

    1. `.lastInstallationLocation` ファイルが存在する場合は、ファイルの内容を表示し、一覧表示されているディレクトリをメモします。

    1. インストールする製品のディレクトリ (`S4HANA2020` など) が存在する場合は、その製品のフォルダーに移動します。 たとえば、 `cd /tmp/sapinst_instdir/S4HANA2020/CORE/HDB/INSTALL/HA/ABAP/ASCS/`を実行します。

1. ブラウザーの SWPM GUI で、 **[キャンセル]** を選択します。 これで、ASCS の無人インストールを実行できるテンプレートをビルドするために必要な ini ファイルが作成されました。

1. `inifile.params` をコピーし、名前を `scs.inifile.params` に変更して `/tmp/app_template` に保存します。 次のように `<path-to-INI-file>` を INI ファイルへのパスに置き換えます。

    ```bash
    cp <path-to-INI-file>/inifile.params /tmp/app_template/scs.inifile.params
    ```

## <a name="load-database-content"></a>データベース コンテンツの読み込み

開始する前に、VM で次の設定を行ったことを確認します。

- HANA および SCS インスタンスをインストールして構成します。 データベースのコンテンツの読み込みを行う前に、これらのインスタンスがオンラインである必要があります。

- [ASCS の無人インストール ファイルを生成したときに作成した](#generate-ascs-parameter-file) `<sid>adm` ユーザーは、`sapinst` グループのメンバーである必要があります。

- `<sid>adm` のユーザー ID は、`hdblcm` の値と一致する必要があります。 この例では、`2000` を使用します。

- SWPM では、`/sapmnt/<SID>/global/` にアクセスする必要があります。 アクセス許可を構成するには、`chown <sid>adm:sapsys /sapmnt/<SID>/global` を実行します。

### <a name="generate-database-load-template"></a>データベース読み込みテンプレートの生成

データベース コンテンツの読み込み用の無人インストールのパラメーター ファイルを生成するには、次の手順を実行します。

1. 一時ディレクトリを作成してそこに移動します。 `<sid>` を実際の SID に置き換えます。
    
    ```bash
    sudo install -d -m 0777 <sid>adm -g sapinst "/tmp/db_workdir"; cd $_
    ```

1. SWPM を起動し、表示された URL をメモします。

    ```bash
    /usr/sap/install/SWPM/sapinst   \
    SAPINST_XML_FILE=/usr/sap/install/config/MP_STACK_S4_2020_v001.xml
    ```

1. ブラウザーで、メモした URL にアクセスします。

1. セキュリティ リスクの警告を受け入れます。
    
1. システムのルート ユーザーの資格情報を使用して認証します。

1. カスタム パラメーターを使用して分散システムを作成します。

    1. ドロップダウン メニューで、 **[SAP S4/HANA サーバー 2020]** &gt; **[SAP HANA データベース]** &gt; **[インストール]** &gt; **[アプリケーション サーバー ABAP]** &gt; **[分散システム]** &gt; **[データベース インスタンス]** &gt; **[分散システム]** に移動します。

    1. **[カスタム]** パラメーター モードを選択します。

    1. **[次へ]** を選択します。

1. ASCS のインストールによって作成されるプロファイル ディレクトリのパスをメモしておきます。 たとえば、`/usr/sap/<SID>/SYS/profile` のようになります (ここで、`<SID>` は実際の SID です)。 次に、 **[次へ]** を選択します。

1. ASCS インスタンスの ABAP メッセージ サーバー ポートを入力します。 ポート番号は `36<InstanceNumber>` です。ここで、 `<InstanceNumber>` は HANA インスタンス番号です。 たとえば、インスタンスがゼロの場合は、`3600` がポート番号です。 次に、 **[次へ]** を選択します。

1. データベース コンテンツのインストール時に使用するメイン パスワードを入力します。 次に、 **[次へ]** を選択します。

1. 管理者ユーザー `<SID>adm` (ここで、`SID` は実際の SID です) の詳細が正しいことを確認します。 次に、 **[次へ]** を選択します。

1. **[SAP HANA データベース テナント]** の情報を入力します。 

    1. **[データベース ホスト]** に、HANA データベース VM のホスト名を入力します。 このホスト名を見つけるには、Azure portal のリソース ページに移動します。

    1. **[インスタンス番号]** に、HANA インスタンス番号を入力します。 たとえば、`00` のようにします。

    1. 新しいデータベース テナントの ID を入力します。 たとえば、`S4H` のようにします。

    1. データベース システム管理者に対して自動的に生成されたパスワードを維持します。

    1. **[次へ]** を選択します。

1. 接続の詳細が正しいことを確認します。 **[OK]** をクリックします。

1. システム データベースの管理者パスワードを入力します。 次に、 **[次へ]** を選択します。

1. SAPEXE カーネルへのパス (`/usr/sap/install/download_basket`) を入力します。 次に、 **[次へ]** を選択します。

1. 使用可能なファイルを確認します。
    
    1. **[次へ]** を選択します。

    1. `SAPHOSTAGENT` ファイルにアクセスできることを確認します。

    1. もう一度 **[次へ]** を選択します

1. パスワードの確認ページで、 **[次へ]** を選択します。

1. すべてのコア HANA データベース エクスポート ファイルが使用可能であることを確認します。 次に、 **[次へ]** を選択します。

1. `SAPHANADB` の **[データベース スキーマ]** で、 **[次へ]** を選択します。

1. **[データベース接続のセキュリティで保護されたストレージ]** で、 **[次へ]** を選択します。

1. **[SAP HANA インポート パラメーター]** で **[次へ]** を選択します。

1. データベース VM の HANA データベース管理者 (`<SID>adm`) のパスワードを入力します。 次に、 **[次へ]** を選択します。

1. **[SAP HANA クライアント ソフトウェアのインストール パス]** で、 **[次へ]** を選択します。

1. SAP HANA クライアント ファイルが使用可能であることを確認します。 次に、 **[次へ]** を選択します。

1. **[はい、オペレーティング システム ユーザーをクリーンアップします]** を有効にしたことを確認します。 次に、 **[次へ]** を選択します。

1. **[パラメーターの概要]** では、まだ何も選択しないでください。

1. CLI を開いて、インストール構成ファイルを見つけます。

    1. 一時ディレクトリ (`/tmp/sapinst_instdir/`) 内のファイルを一覧表示します。

    1. インストール構成ファイル (`inifile.params`) があることを確認します。

    1. `lastInstallationLocation` ファイルがある場合は、そのファイルを開きます。 ファイルの内容にリストされているディレクトリをメモします。

    1. インストールしている製品のディレクトリ (`S4HANA2020` など) が既に存在する場合は、対応するフォルダーに移動します。 たとえば、`/tmp/sapinst_instdir/S4HANA2020/CORE/HDB/INSTALL/HA/ABAP/DB/` のようにします。

1. SWPM を再度開きます。

1. **[キャンセル]** を選択します。 これで、データベース コンテンツの読み込みに無人モードを使用できるようになりました。

1. 次のように、インストール構成ファイルをコピーして名前を変更します。 `<path_to_config_file>` を構成ファイルへのパスで置き換えます。

    ```bash
    cp <path_to_config_file>/inifile.params /tmp/app_template/db.inifile.params
    ```

1. SWPM の `sapinst` ツールのバージョンを確認します。
    
    ```bash
    /usr/sap/install/SWPM/sapinst -version
    ```

1. `sapinst` のバージョンが `749.0.6` よりも大きい場合は、`keydb.xml` および `instkey.pkey` ファイルもコピーして、[SAP Note 2393060](https://launchpad.support.sap.com/#/notes/2393060) に従います。 `<path_to_config_file>` を構成ファイルへのパスで置き換えます。
    
    ```bash
    cp <path_to_config_file>/{keydb.xml,instkey.pkey} /tmp/app_template/
    ```

## <a name="generate-pas-parameter-file"></a>PAS パラメーター ファイルを生成する

PAS で使用する無人インストールのパラメーター ファイルを生成します。 これらのファイルはすべて `inifile` で始まります。 

> [!IMPORTANT]
> 2020 バージョンの SAP 製品では、これらの設定の一部が表示されない場合があります。 その場合は、このステップをスキップします。

1. CLI を使用して VM に接続します。

1. [必要なすべてのメディアとツールが VM にインストールされていることを確認します](#check-media-and-tools)。

1. 一時ディレクトリを作成してそこに移動します。 `<SID>` を実際の SID に置き換えます。

    ```bash
    sudo install -d -m 0777 <SID>adm -g sapinst "/tmp/pas_workdir"; cd $_
    ```
    
1. ルート ユーザーとしてノードに接続します。 

1. SWPM にサインインします。 

    1. SWPM GUI の URL にアクセスします。 この URL は、[ASCS の無人インストール ファイルを生成](#generate-ascs-parameter-file)したときに取得しました。

    1. セキュリティの警告を受け入れます。
    
    1. システムのルート ユーザーの資格情報を使用して認証します。

1. ドロップダウン メニューで、 **[SAP S/4HANA サーバー 2020]** &gt; **[SAP HANA データベース]** &gt; **[インストール]** &gt; **[アプリケーション サーバー ABAP]** &gt; **[分散システム]** &gt; **[プライマリ アプリケーション サーバー インスタンス]** に移動します。

1. **[パラメーター設定]** で、 **[カスタム]** を選択します。 次に、 **[次へ]** を選択します。

1. **[プロファイル ディレクトリ]** が `/sapmnt/<SID>/profile/` または `/usr/sap/<SID>/SYS/profile` に設定されていることを確認します。ここで、`<SID>` は実際の SID です。 次に、 **[次へ]** を選択します。

1. **[メッセージ サーバー ポート]** を `36<instance-number>` に設定します。ここで、`<instance-number>` は ASCS インスタンス番号です。 たとえば、`3600` のようにします。 次に、 **[次へ]** を選択します。

1. すべてのユーザーのメイン パスワードを設定します。 次に、 **[次へ]** を選択します。

1. `below-the-fold-list` の一覧が事前設定されるのを待ちます。 次に、 **[次へ]** を選択します。

1. **[SAP Host Agent を提供された SAPHOSTAGENT.SAR アーカイブのバージョンにアップグレード]** の設定を無効にしたことを確認します。 次に、 **[次へ]** を選択します。

1. SAP HANA データベースのインスタンス番号と、データベース システム管理者のパスワードを入力します。 次に、 **[次へ]** を選択します。

1. **[SAP HANA を使用した SAP liveCache の構成]** で、 **[次へ]** を選択します。

1. `DBACOCKPIT` の **[データベース スキーマ]** で、 **[次へ]** を選択します。

1. `SAPHANADB` の **[データベース スキーマ]** で、 **[次へ]** を選択します。

1. **[データベース接続のセキュリティで保護されたストレージ]** で、 **[次へ]** を選択します。

1. PAS インスタンス番号とインスタンス ホストが正しいことを確認します。 次に、 **[次へ]** を選択します。

1. **[ABAP メッセージ サーバーのポート]** で、 **[次へ]** を選択します。

1. **[作業プロセスの構成]** で、 **[次へ]** を選択します。

1. **[SAP Web ディスパッチャーの ICM ユーザー管理]** で、 **[次へ]** を選択します。

1. **[SAP システム OS レベルの SLD 宛先]** で、次の設定を構成します。

    1. **[SLD 宛先なし]** を有効にします。 次に、 **[次へ]** を選択します。

    1. **[メッセージ サーバーのアクセス制御リストを作成しない]** を有効にします。 **[次へ]** を選択します。

    1. **[TMS の実行]** を有効にします。 

    1. **クライアント 000** の **TMSADM** のユーザー パスワードをメイン パスワードに設定します。 次に、 **[次へ]** を選択します。

    1. **[ABAP トランスポートのインポートを行わない]** を有効にします。 次に、 **[次へ]** を選択します。

1. **[追加の SAP システム言語]** で、 **[次へ]** を選択します。 

1. **[SAP システム DDIC ユーザー]** で、 **[次へ]** を選択します。

1. **[セキュリティで保護されたストレージ キーの生成]** で、 **[個別のキー]** を選択します。 次に、 **[次へ]** を選択します。

1. 警告画面で次の操作を行います。

    1. キー ID とキー値をコピーします。 
    
    1. キー ID とキー値を安全に格納します。

    1. **[次へ]** を選択します。 

1. **[オペレーティング システム ユーザーをクリーンアップします]** で、 **[はい]** を選択します。 次に、 **[次へ]** を選択します。

1. CLI で、インストールの一時ディレクトリを開きます。 

1. パラメーター ファイル (`inifile.params`) のコピーがあることを確認します。 たとえば、`/tmp/sapinst_instdir/S4HANA2020/CORE/HDB/INSTALL/DISTRIBUTED/ABAP/APP1/inifile.params` のようにします。

1. SWPM で **[キャンセル]** を選択します。 これで、無人モードを使用して PAS をインストールできるようになりました。

1. 次のように、PAS パラメーター ファイルをコピーし、名前を `pas.inifile.params` に変更して `/tmp/app_template` に保存します。 `<path_to_config_file>` をパラメーター ファイルへのパスで置き換えます。 

    ```bash
    cp <path_to_config_file>/inifile.params /tmp/app_template/pas.inifile.params
    ```

1. `pas.inifile.params` のコピーを作成して、コンピューターまたは VM にダウンロードします。

## <a name="generate-additional-application-servers-parameter-file"></a>追加のアプリケーション サーバー パラメーター ファイルを生成する

AAS で使用する無人インストールのパラメーター ファイルを生成します。 これらのファイルはすべて `inifile` で始まります。 

> [!IMPORTANT]
> 2020 バージョンの SAP 製品では、これらの設定の一部が表示されない場合があります。 その場合は、このステップをスキップします。

1. CLI を使用して AAS VM に接続 します。

1. [必要なすべてのメディアとツールが VM にインストールされていることを確認します](#check-media-and-tools)。

1. グループ `sapinst` が存在することを確認します。
    
    ```bash
    groupadd -g 2000 sapinst
    ```

1. インストール用の一時ディレクトリを次のように作成します。 `<sid>` を実際の SID に置き換えます。

    ```bash
    sudo install -d -m 0777 <sid>adm -g sapinst "/tmp/aas_workdir"; cd $_
    ```

1. SWPM にサインインします。 

    1. SWPM GUI の URL にアクセスします。 この URL は、[ASCS の無人インストール ファイルを生成](#generate-ascs-parameter-file)したときに取得しました。

    1. セキュリティの警告を受け入れます。
    
    1. システムのルート ユーザーの資格情報を使用して認証します。

1. ドロップダウン メニューで、 **[SAP S/4HANA サーバー 2020]** &gt; **[SAP HANA データベース]** &gt; **[インストール]** &gt; **[アプリケーション サーバー ABAP]** &gt; **[高可用性システム]** &gt; **[追加のアプリケーション サーバー インスタンス]** に移動します。

1. **[パラメーター設定]** で、 **[カスタム]** を選択します。 次に、 **[次へ]** を選択します。

1. **[プロファイル ディレクトリ]** が `/sapmnt/<SID>/profile/` または `/usr/sap/<SID>/SYS/profile` に設定されていることを確認します。ここで、`<SID>` は実際の SID です。 次に、 **[次へ]** を選択します。

1. **[メッセージ サーバー ポート]** を `36<instance-number>` に設定します。ここで、`<instance-number>` は ASCS インスタンス番号です。 次に、 **[次へ]** を選択します。

1. すべてのユーザーのメイン パスワードを設定します。 次に、 **[次へ]** を選択します。

1. **[ソフトウェア パッケージ ブラウザー]** で、 **[検索ディレクトリ]** を `/usr/sap/install/download_basket` に設定します。 次に、 **[次へ]** を選択します。

1. `below-the-fold-list` の一覧が事前設定されるのを待ちます。 次に、 **[次へ]** を選択します。

1. **[SAP Host Agent を提供された SAPHOSTAGENT.SAR アーカイブのバージョンにアップグレード]** を有効にしたことを確認します。 次に、 **[次へ]** を選択します。

1. SAP HANA データベースのインスタンス番号と、データベース システム管理者のパスワードを入力します。 次に、 **[次へ]** を選択します。

1. **[SAP HANA を使用した SAP liveCache の構成]** で、 **[次へ]** を選択します。

1. `DBACOCKPIT` の **[データベース スキーマ]** で、 **[次へ]** を選択します。

1. `SAPHANADB` の **[データベース スキーマ]** で、 **[次へ]** を選択します。

1. **[データベース接続のセキュリティで保護されたストレージ]** で、 **[次へ]** を選択します。

1. AAS インスタンス番号とインスタンス ホストが正しいことを確認します。 次に、 **[次へ]** を選択します。

1. **[ABAP メッセージ サーバーのポート]** で、 **[次へ]** を選択します。

1. **[作業プロセスの構成]** で、 **[次へ]** を選択します。

1. **[SAP Web ディスパッチャーの ICM ユーザー管理]** で、 **[次へ]** を選択します。

1. **[SAP システム OS レベルの SLD 宛先]** で、 **[SLD 宛先なし]** を有効にしたことを確認します。 次に、 **[次へ]** を選択します。

1. **[メッセージ サーバーのアクセス制御リストを作成しない]** を有効にします。 次に、 **[次へ]** を選択します。

1. **[TMS の実行]** を有効にします。

1. **クライアント 000** のユーザー **TMSADM** のパスワードをメイン パスワードに設定します。 次に、 **[次へ]** を選択します。

1. **[SPAM/SAINT 更新アーカイブ]** を `/usr/sap/install/config/KD75371.SAR` に設定します。

1. **[ABAP トランスポートのインポート]** を **[いいえ]** に設定します。 次に、 **[次へ]** を選択します。

1. **[ソフトウェア更新マネージャー画面の準備]** で、 **[SUM*.SAR アーカイブの解凍]** を有効にします。 次に、 **[次へ]** を選択します。

1. **[ソフトウェア パッケージ ブラウザー]** で、 **[検出されたパッケージ]** テーブルを選択します。 **SUM 2.0** の個別のパッケージの場所が空の場合は、パッケージのパスを `usr/sap/install/config` に設定します。 次に、 **[次へ]** を選択します。

1. パッケージの場所が設定されるのを待ちます。 次に、 **[次へ]** を選択します。

1. **[追加の SAP システム言語]** で、 **[次へ]** を選択します。

1. **[はい、オペレーティング システム ユーザーをクリーンアップします]** を有効にしたことを確認します。 次に、 **[次へ]** を選択します。

1. CLI を使用して、一時ディレクトリにパラメーター ファイルのコピーがあることを確認します。 たとえば、`/tmp/sapinst_instdir/S4HANA2020/CORE/HDB/INSTALL/AS/APPS/inifile.params` のようにします。

1. 次のように、そのファイルをコピーし、名前を `aas.inifile.params` に変更して `/tmp/app_template` に保存します。 `<path_to_inifile>` をパラメーター ファイルへのパスで置き換えます。

    ```bash
    cp <path_to_inifile>/inifile.params /tmp/app_template/aas.inifile.params
    ```

1. `aas.inifile.params` のコピーを作成して、コンピューターまたは VM にダウンロードします。

1. SWPM で **[キャンセル]** を選択します。 これで無人モードで AAS インストールを実行できるようになりました。

## <a name="combine-parameter-files"></a>パラメーター ファイルを結合する

インストール処理のために、`inifile.params` で終わるすべてのパラメーター ファイルを 1 つのファイルに結合することができます。 

### <a name="create-combination-file"></a>結合ファイルを作成する

すべてのパラメーターを結合したファイルを作成するには、次の手順を実行します。

1. 作成した各パラメーター ファイル (ASCS、PAS、AAS) をまだダウンロードしていない場合は、ダウンロードします。 これらのファイルは、作業しているコンピューターまたは VM 上にある必要があります。

1. 各パラメーター ファイルのバックアップを作成します。

1. 新しい結合ファイルを作成します。 このファイルに、使用している SAP 製品の名前を付けます。 たとえば、`S4HANA_2020_ISS_v001.inifile.params` のようにします。

1. エディターで ASCS パラメーター ファイル (`scs.inifile.params`) を開きます。

1. ASCS パラメーター ファイルのヘッダーを結合ファイルにコピーします。 次に例を示します。

    ```yml
    #########################################################################################################################
    #                                                                                                                       #
    # Installation service 'SAP S/4HANA Server 2020 > SAP HANA Database > Installation                                      #
    #   > Application Server ABAP > Distributed System > ASCS Instance', product id 'NW_ABAP_ASCS:S4HANA2020.CORE.HDB.ABAP' #
    #                                                                                                                       #
    #########################################################################################################################
    ```

1. 各 `inifile.params` ファイルで、ヘッダーから製品 ID の行をコピーします。 次に、結合ファイルのヘッダーに製品 ID をコピーします。 次に例を示します。

    ```yml
    #############################################################################################################################################
    #                                                                                                                                           #
    # Installation service 'SAP S/4HANA Server 2020 > SAP HANA Database > Installation                                                          #
    #   > Application Server ABAP > Distributed System > ASCS Instance', product id 'NW_ABAP_ASCS:S4HANA2020.CORE.HDB.ABAP'                     #
    #   > Application Server ABAP > Distributed System > Database Instance', product id 'NW_ABAP_DB:S4HANA2020.CORE.HDB.ABAP'                   #
    #   > Application Server ABAP > Distributed System > Primary Application Server Instance', product id 'NW_ABAP_CI:S4HANA2020.CORE.HDB.ABAP' #
    #   > Additional SAP System Instances > Additional Application Server Instance', product id 'NW_DI:S4HANA2020.CORE.HDB.PD'                  #
    #                                                                                                                                           #
    #############################################################################################################################################
    ```

1. エディターで `bom.yml` ファイルを開きます。

1. `product_ids` のセクションを結合ファイルにコピーします。

1. 保持している各 `inifile.params` ファイルについて、製品 ID をヘッダーから `product_ids` の適切な部分にコピーします。 たとえば、次のように ASCS を `scs` にコピーします。

    ```yml
    product_ids:
      scs: "NW_ABAP_ASCS:S4HANA2020.CORE.HDB.ABAP"
      db:  ""
      pas: ""
      aas: ""
      web: ""
    ```

1. コメントアウトした行を削除するか、空白のままにします。

1. 結合ファイルを保存します。

### <a name="improve-readability"></a>読みやすさの向上

次に、[結合ファイル](#create-combination-file)の読みやすさを向上させます。

1. エディターで、結合ファイルを開きます。

1. ヘッダーに含まれていないすべての行を並べ替えます。

1. 重複する行を削除します。

1. すべての等号をそろえます。 次に例を示します。

    ```yml
    archives.downloadBasket                             = /usr/sap/install/download_basket
    HDB_Schema_Check_Dialogs.schemaName                 = SAPHANADB
    HDB_Schema_Check_Dialogs.schemaPassword             = MyDefaultPassw0rd
    HDB_Userstore.doNotResolveHostnames                 = x00dx0000l09d4
    ```

1. プレフィックスで行を区切ります。 たとえば、`NW_CI_Instance.*` や `NW_HDB_DB.*`す。

1. Ansible の変数を使用するように次の行を更新します。

    1. `archives.downloadBasket                             = {{ download_basket_dir }}`

    1. `HDB_Schema_Check_Dialogs.schemaPassword             = {{ main_password }}`
    
    1. `HDB_Userstore.doNotResolveHostnames                 = {{ hdb_hostname }}`
    
    1. `hostAgent.sapAdmPassword                            = {{ main_password }}`
    
    1. `NW_AS.instanceNumber                                = {{ aas_instance_number }}`
    
    1. `NW_checkMsgServer.abapMSPort                        = 36{{ scs_instance_number }}`
    
    1. `NW_CI_Instance.ascsVirtualHostname                  = {{ scs_hostname }}`
    
    1. `NW_CI_Instance.ciInstanceNumber                     = {{ pas_instance_number }}`
    
    1. `NW_CI_Instance.ciMSPort                             = 36{{ scs_instance_number }}`
    
    1. `NW_CI_Instance.ciVirtualHostname                    = {{ pas_hostname }}`
    
    1. `NW_CI_Instance.scsVirtualHostname                   = {{ scs_hostname }}`
    
    1. `NW_DI_Instance.virtualHostname                      = {{ aas_hostname }}`
    
    1. `NW_getFQDN.FQDN                                     = {{ sap_fqdn }}`
    
    1. `NW_GetMasterPassword.masterPwd                      = {{ main_password }}`
    
    1. `NW_GetSidNoProfiles.sid                             = {{ app_sid | upper }}`
    
    1. `NW_HDB_DB.abapSchemaPassword                        = {{ main_password }}`
    
    1. `NW_HDB_getDBInfo.dbhost                             = {{ hdb_hostname }}`
    
    1. `NW_HDB_getDBInfo.dbsid                              = {{ hdb_sid | upper }}`
    
    1. `NW_HDB_getDBInfo.instanceNumber                     = {{ hdb_instance_number }}`
    
    1. `NW_HDB_getDBInfo.systemDbPassword                   = {{ main_password }}`
    
    1. `NW_HDB_getDBInfo.systemid                           = {{ hdb_sid | upper }}`
    
    1. `NW_HDB_getDBInfo.systemPassword                     = {{ main_password }}`
    
    1. `NW_readProfileDir.profileDir                        = /usr/sap/{{ app_sid | upper }}/SYS/profile`
    
    1. `NW_Recovery_Install_HDB.extractLocation             = /usr/sap/{{ hdb_sid | upper }}/HDB{{ hdb_instance_number }}/backup/data/DB_{{ hdb_sid | upper }}`
    
    1. `NW_Recovery_Install_HDB.sidAdmName                  = {{ hdb_sid | lower }}adm`
    
    1. `NW_Recovery_Install_HDB.sidAdmPassword              = {{ main_password }}`
    
    1. `NW_SAPCrypto.SAPCryptoFile                          = {{ download_basket_dir }}/SAPEXE_300-80004393.SAR`
    
    1. `NW_SCS_Instance.instanceNumber                      = {{ scs_instance_number }}`
    
    1. `NW_Unpack.igsExeSar                                 = {{ download_basket_dir }}/igsexe_12-80003187.sar`
    
    1. `NW_Unpack.igsHelperSar                              = {{ download_basket_dir }}/igshelper_17-10010245.sar`
    
    1. `NW_Unpack.sapExeDbSar                               = {{ download_basket_dir }}/SAPEXEDB_300-80004392.SAR`
    
    1. `NW_Unpack.sapExeSar                                 = {{ download_basket_dir }}/SAPEXE_300-80004393.SAR`
    
    1. `NW_SCS_Instance.scsVirtualHostname                  = {{ scs_hostname }}`
    
    1. `nwUsers.sapadmUID                                   = {{ sapadm_uid }}`
    
    1. `nwUsers.sapsysGID                                   = {{ sapsys_gid }}`
    
    1. `nwUsers.sidadmPassword                              = {{ main_password }}`
    
    1. `nwUsers.sidAdmUID                                   = {{ sidadm_uid }}`
    
    1. `storageBasedCopy.hdb.instanceNumber                 = {{ hdb_instance_number }}`
    
    1. `storageBasedCopy.hdb.systemPassword                 = {{ main_password }}`

### <a name="upload-combination-file"></a>結合ファイルをアップロードする

最後に、統合されたテンプレート ファイルを SAP ライブラリにアップロードします。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. **ストレージ アカウント** を選択するか、検索します。

1. SAP ライブラリのストレージ アカウントを選択します。

1. ストレージ アカウントのメニューの **[データ ストレージ]** で **[コンテナー]** を選択します。

1. `sapbits` コンテナーを選択します。

1. `sapbits` で BOM の製品フォルダーに移動します。 たとえば、`boms/S4HANA_2020_ISS_v001` のようにします。

1. **templates** というディレクトリがまだない場合は、このディレクトリを作成します。

1. **templates** ディレクトリを開きます。

1. **[アップロード]** を選択します。

1. ペインで、 **[ファイルの選択]** を選択します。

1. 結合されたテンプレート ファイルを選択します。  たとえば、`S4HANA_2020_ISS_v001.inifile.params` のようにします。

1. **[アップロード]** を選択します。

## <a name="update-bom-with-templates"></a>テンプレートで BOM を更新する

[パラメーター ファイルを結合](#combine-parameter-files)したら、新しいテンプレート ファイルで BOM を更新します。

1. `bom.yml`を開きます。

1. `templates` セクションに新しいテンプレート ファイル名を追加します。  次に例を示します。

    ```yml
    templates:
      - name:     "S4HANA_2020_ISS_v001 ini file"
        file:     S4HANA_2020_ISS_v001.inifile.params
        override_target_location: "{{ target_media_location }}/config"
    ```

1. スクリプト化されたアプリケーション BOM の準備を使用している場合は、テンプレートの前の `#` を削除します。

1. 変更を保存します。

次に、新しい BOM ファイルを SAP ライブラリにアップロードします。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. **ストレージ アカウント** を選択するか、検索します。

1. SAP ライブラリのストレージ アカウントを選択します。

1. ストレージ アカウントのメニューの **[データ ストレージ]** で **[コンテナー]** を選択します。

1. `sapbits` コンテナーを選択します。

1. `sapbits` で BOM の製品フォルダーに移動します。 たとえば、`boms/S4HANA_2020_ISS_v001` のようにします。

1. `boms` ディレクトリを開きます。

1. **[アップロード]** を選択します。

1. ペインで、 **[ファイルの選択]** を選択します。

1. ご使用のコンピューターまたは VM から、BOM ファイル (`bom.yml`) を選択します。

1. **[ファイルが既に存在する場合は上書きする]** を有効にしたことを確認します。

1. **[アップロード]** を選択します。

