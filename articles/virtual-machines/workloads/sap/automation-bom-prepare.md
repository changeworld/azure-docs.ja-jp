---
title: 自動化のための部品表を準備する
description: Azure 上の SAP デプロイ自動化フレームワークで使用する完全な SAP 部品表 (BOM) を準備する方法。
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: how-to
ms.service: virtual-machines-sap
ms.openlocfilehash: 772b7c5cb972523cb701778951b45608241b0ee7
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725190"
---
# <a name="prepare-sap-bom"></a>SAP BOM を準備する

[Azure 上の SAP デプロイ自動化フレームワーク](automation-deployment-framework.md)では、部品表 (BOM) が使用されます。 BOM を利用して SAP システムを構成できます。 

自動化フレームワークの GitHub リポジトリには[サンプル BOM](https://github.com/Azure/sap-hana/tree/main/deploy/ansible/BOM-catalog) のセットが含まれていて、これを使用して作業を開始できます。 また、他の SAP アプリケーションやデータベース用の BOM も作成できます。 

固定リンクを含む BOM を生成する場合は、[この種類の BOM を作成する手順に従います](#permalinks)。

> [!NOTE]
> このガイドでは、デプロイに関する詳細なトピックを紹介します。 自動化フレームワークをデプロイする方法の基本的な説明については、代わりに[概要ガイド](automation-get-started.md)のページを参照してください。

## <a name="prerequisites"></a>前提条件

- まだ行っていない場合は、[SAP インストール メディアと関連ファイルをダウンロードして準備します](automation-bom-get-files.md)。
    - Azure ストレージ アカウント内の SAP アプリケーション (DB) または HANA メディア。
- BOM ファイルを操作するための YAML エディター。
- 以下のためのアプリケーション インストール テンプレート: 
    - SAP セントラル サービス (SCS)
    - SAP プライマリ アプリケーション サーバー (PAS)
    - SAP 追加アプリケーション サーバー (AAS)
- 必要なスタック ファイルを、[SAP メディア を取得](automation-bom-get-files.md#acquire-media)するために作成したフォルダーにダウンロードすること。 詳細については、[基本的な BOM の準備に関する攻略ガイド](automation-bom-prepare.md)を参照してください。
- [SAP ダウンロード バスケットのマニフェスト](automation-bom-get-files.md#get-download-basket-manifest) (`DownloadBasket.json`) のコピーを、[SAP メディアを取得するために作成したフォルダー](automation-bom-get-files.md#acquire-media)にダウンロードしていること。
    - [Postman ユーティリティ](https://www.postman.com/downloads/)のインストール。
- Azure サブスクリプション。 Azure サブスクリプションをまだお持ちでない場合は、[無料のアカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成できます。
- 使用するデータベースを操作するためのアクセス許可を持つ SAP アカウント。
- [BOM を検証する](#validate-bom)ための Linux 型のコマンドを実行するシステム。 コマンド `yamllint` と `ansible-lint` をシステムにインストールします。

## <a name="scripted-creation-process"></a>スクリプトによる作成プロセス

このプロセスでは、[手動の BOM 作成プロセス](#manual-creation-process)と同じ手順を自動化します。 このプロセスを使用する前に、[スクリプトの制限事項](#script-limitations)を確認してください。

1. スタック ファイル フォルダーに移動します。

    ```bash
    cd stackfiles
    ```

1. BOM 生成スクリプトを実行します。 サンプル パスをユーティリティ フォルダーへの正しいパスに置き換えます。 次に例を示します。

    ```bash
    cd ~/Azure_SAP_Automated_Deployment/deploy/scripts/generate_bom.sh >../bom.yml
    ```

1. 製品パラメーター (`product`) には、SAP 製品名を入力します。 たとえば、`SAP_S4HANA_1809_SP4` のようにします。 値を入力しない場合、スクリプトでは、スタック XML ファイルから名前を特定します。

1. 生成された `bom.yml` ファイルを開き、確認します。

1. テンプレート セクション (`templates`) を確認します。 `file` と `override_target_location` の値が正しいか確認します。 必要に応じて、それらの行を編集してコメント アウトします。 次に例を示します。

    ```yml
    templates:
      # - name:     "S4HANA_2020_ISS_v001 ini file"
      #   file:     S4HANA_2020_ISS_v001.inifile.params
      #   override_target_location: "{{ target_media_location }}/config"
    ```

1. スタック ファイル セクション (`stackfiles`) を確認します。 項目名とファイルが正しいか確認します。 必要に応じて、それらの行を編集します。

## <a name="script-limitations"></a>スクリプトの制限事項

[スクリプトによる BOM 作成プロセス](#scripted-creation-process)には、次の制限があります。

スクリプトには、HANA2 に対するハードコーディングされた依存関係があります。 必要な依存関係名と一致するように BOM ファイルを手動で編集します。 次に例を示します。

```yml
dependencies:
  - name: "HANA2"
```

メディア パラメーター `override_target_filename:`、`override_target_location`、`version:` には既定値がありません。 これらのパラメーターを変更するには、BOM ファイルを手動で編集します。 次に例を示します。

```yml
   - name:     SAPCAR
     archive:  SAPCAR_1320-80000935.EXE
     override_target_filename: SAPCAR.EXE

   - name: "SWPM20SP07"
     archive: "SWPM20SP07_2-80003424.SAR"
     override_target_filename: SWPM.SAR
     sapurl: "https://softwaredownloads.sap.com/file/0020000001812632020"
```

このスクリプトで生成されるのは、SAP のメンテナンス プランナーで識別されるメディア ファイルのエントリのみです。 この制限は、スタック `.xsl` ファイルを処理するために発生します。 SAP ランチパッドを使用するなどして、ダウンロード バスケットにファイルを個別に追加する場合は、[それらのファイルを BOM に手動で追加](#manual-creation-process)する必要があります。

## <a name="manual-creation-process"></a>手動の作成プロセス

BOM は、次の手動プロセスを使用して作成できます。 もう 1 つの方法として、[スクリプトによる作成プロセス](#scripted-creation-process)を使用して同じ手順を実行します。

1. [SAP メディアを取得](automation-bom-get-files.md#acquire-media)するために作成したダウンロード フォルダーを開きます

1. `bom.yml` という名前の空の YAML ファイルを作成します。

1. エディターで `bom.yml` を開きます。

1. ビルドとターゲットの名前を含む BOM ヘッダーを追加します。 `name` 値は、ストレージ アカウントの BOM フォルダー名と同じであることが必要です。 次に例を示します。

    ```yml
    name:    'S4HANA_2020_ISS_v001'
    target:  'ABAP PLATFORM 2020'
    ```

1. ターゲットの場所を含む defaults セクションを追加します。 インストール ファイルをコピーするターゲット サーバー上のフォルダーへのパスを使用します。 通常は、次のように `{{ target_media_location }}` を使用します。

    ```yml
    defaults:
      target_location: "{{ target_media_location }}/download_basket"
    ```

1. 製品識別子セクションを追加します。 これらの値は、後でテンプレートの準備の一環として設定します。 次に例を示します。

    ```yml
    product_ids:
      scs:
      db:
      pas:
      aas:
      web:
    ```

1. materials セクションを追加して、必要な部品の一覧を指定します。 このセクションに、他の BOM への依存関係を追加します。 次に例を示します。

    ```yml
    materials:
    dependencies:
        - name:     HANA2
    ```

1. BOM に含めるメディアの一覧を取得します。

    1. ダウンロード バスケットのスプレッドシートを開きます。 このファイルは XML としてレンダリングされます。

    1. 必要に応じて、XML コンテンツを人間が判読できる形式に設定します。

    1. ダウンロード バスケット内の項目ごとに、`String` と `Number` のデータを記録します。 `String` のデータは、ファイル名 (たとえば、`igshelper_17-10010245.sar`) とわかりやすい説明 (たとえば、`SAP IGS Fonts and Textures`) を示します。 `Number` のデータは、BOM 内の各エントリの後に記録します。 

1. メディアの一覧を `bom.yml` に追加します。 これらの項目の順序は重要ではありませんが、読みやすくするために関連する項目をグループ化するようにします。 `SAPCAR` は、SAP ダウンロード バスケットにこのユーティリティが含まれている場合でも、別個に追加します。 次に例を示します。

    ```yml
    media:
        - name:     SAPCAR
          archive:  SAPCAR_1320-80000935.EXE
    
        name: "SAP IGS Fonts and Textures"
          archive: "igshelper_17-10010245.sar"
          # 61489
    
        <...>
    ```

1. オプションとして、ターゲット メディアの場所をオーバーライドする必要がある場合は、パラメーター `override_target_location` をメディア項目に追加します。 たとえば、`override_target_location: "{{ target_media_location }}/config"` のようにします。

1. 空のテンプレート セクションを追加します。

    ```yml
    templates:
    ```

1. スタック ファイル セクションを作成します。 次に例を示します。

    ```yml
    stackfiles:
      - name: Download Basket JSON Manifest
         file: downloadbasket.json
    
      - name: Download Basket Spreadsheet
         file: MP_Excel_2001017452_20201030_SWC.xls
    ```

1. 変更を `bom.yml` に保存します。

### <a name="permalinks"></a>固定リンク

正常に機能する基本的な BOM を自動的に生成できます。 ただし、既定では、BOM によって SAP メディアへの永続的な URL (固定リンク) は作成されません。 固定リンクを作成する場合は、[SAP メディアを取得](automation-bom-get-files.md#acquire-media)する前に、追加の手順を実行する必要があります。 

> [!NOTE]
> 固定リンクを含む完全な SAP BOM を手動で生成するには、[基本的な BOM を手動で準備](#manual-creation-process)する場合の約 2 倍の時間が必要です。 

固定リンクを含む BOM を生成するには、次のようにします。

1. エディターで `DownloadBasket.json` を開きます。

1. 結果ごとに、`Value` 行の内容を記録します。 次に例を示します。

    ```json
         "Value": "0020000000703122018|SP_B|SAP IGS Fonts and Textures|61489|1|20201023150931|0"
    ```

1. 縦棒で区切られた 1 番目と 4 番目の値をコピーします。

    1. 最初の値はファイル番号です。 たとえば、`0020000000703122018` のようにします。

    1. 4 番目の値は、メディアの一覧との照合に使用する数値です。 たとえば、`61489` のようにします。

    1. 必要に応じて、ファイルの種類を示す 2 番目の値をコピーします。 たとえば、カーネル バイナリ ファイルの場合は `SP_B`、カーネル以外のバイナリ ファイルの場合は `SPAT`、データベースのエクスポートの場合は `CD` です。

1. 4 番目の値をキーとして使用して、ダウンロード バスケットをメディアの一覧と照合します。 値 (たとえば、`61489`) を、メディア項目のコメントとして追加した値 (たとえば、`# 61489`) と照合します。

1. `bom.yml` の一致するエントリごとに、SAP URL の新しい値を追加します。 URL では、`https://softwaredownloads.sap.com/file/` と、その項目の 3 番目の値 (たとえば、`0020000000703122018`) を使用します。 次に例を示します。

    ```yml
    - name: "SAP IGS Fonts and Textures"
      archive: "igshelper_17-10010245.sar"
      sapurl: "https://softwaredownloads.sap.com/file/0020000000703122018"
    ```

## <a name="example-bom-file"></a>BOM ファイルの例

次のサンプルは、S/4HANA 1909 SP2 のサンプル BOM ファイルのごく一部です。 

[GitHub リポジトリ](https://github.com/Azure/sap-hana)には、使用できる完全な BOM ファイルが複数用意されています。

```yml
step|BOM Content

---

name:    'S4HANA_2020_ISS_v001'
target:  'ABAP PLATFORM 2020'

defaults:
  target_location: "{{ target_media_location }}/download_basket"

product_ids:
  scs:
  db:
  pas:
  aas:
  web:

materials:
dependencies:
    - name:     HANA2

media:
    - name:     SAPCAR
      archive:  SAPCAR_1320-80000935.EXE

    - name:     SWPM
      archive:  SWPM20SP06_6-80003424.SAR

    - name:     SAP IGS HELPER
      archive:  igshelper_17-10010245.sar

    - name:     SAP HR 6.08
      archive:  SAP_HR608.SAR

    - name:     S4COREOP 104
      archive:  S4COREOP104.SAR

templates:
    - name:     "S4HANA_2020_ISS_v001 ini file"
      file:     S4HANA_2020_ISS_v001.inifile.params
      override_target_location: "{{ target_media_location }}/config"

stackfiles:
    - name: Download Basket JSON Manifest
      file: downloadbasket.json
      override_target_location: "{{ target_media_location }}/config"

    - name: Download Basket Spreadsheet
      file: MP_Excel_2001017452_20201030_SWC.xls
      override_target_location: "{{ target_media_location }}/config"

    - name: Download Basket Plan doc
      file: MP_Plan_2001017452_20201030_.pdf
      override_target_location: "{{ target_media_location }}/config"

    - name: Download Basket Stack text
      file: MP_Stack_2001017452_20201030_.txt
      override_target_location: "{{ target_media_location }}/config"

    - name: Download Basket Stack XML
      file: MP_Stack_2001017452_20201030_.xml
      override_target_location: "{{ target_media_location }}/config"

    - name: Download Basket permalinks
      file: myDownloadBasketFiles.txt
      override_target_location: "{{ target_media_location }}/config"
```

## <a name="validate-bom"></a>BOM を検証する

BOM 構造は、Linux 型のコマンドを実行する任意の OS から検証できます。 Windows では、Linux 用 Windows サブシステム (WSL) を使用します。 別の方法として、BOM ファイルのコピーがある Deployer から検証を実行します。


1. BOM が格納されているディレクトリから検証スクリプト `check_bom.sh` を実行します。 次に例を示します。

    ```bash
    cd ~/Azure_SAP_Automated_Deployment/deploy/scripts/check_bom.sh bom.yml
    ```

1. 出力結果を確認します。 

### <a name="successful-validation"></a>検証が成功した場合

検証に成功すると、次の出力が表示されます。 [前提条件](#prerequisites)で既に `yamllint` および `ansible-lint` コマンドをインストールしています。 

```output
... yamllint [ok]
... ansible-lint [ok]
... bom structure [ok]
```

### <a name="unsuccessful-validation"></a>検証が失敗した場合

検証に失敗すると、エラー情報が含まれます。 次に例を示します。

```output
../documentation/ansible/system-design-deployment/examples/S4HANA_2020_ISS_v001/bom_with_errors.yml
  178:16    error    too many spaces after colon  (colons)
  179:16    error    too many spaces after colon  (colons)
  180:16    error    too many spaces after colon  (colons)
    
... yamllint [errors]
... ansible-lint [ok]
  - Expected to find key 'defaults' in 'bom' (Check name: S4HANA_2020_ISS_v001)
  - Unexpected key 'default in 'bom' (Check name: S4HANA_2020_ISS_v001)
  - Unexpected key 'overide_target_location in 'bom.materials.stackfiles' (Check name: Download Basket Stack text)
... bom structure [errors]
```

## <a name="upload-your-bom"></a>BOM をアップロードする

固定リンクを含む BOM を使用するには、次のようにします。

1. [BOM を検証します](#validate-bom)。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. **[Azure サービス]** の **[リソース グループ]** を選択します。 または、検索バーに `resource groups` と入力します。 

1. SAP ライブラリのリソース グループを選択します。

1. リソース グループ ページで、 **[リソース]** テーブルから `saplib` ストレージ アカウントを選択します。

1. ストレージ アカウント ページのメニューで、 **[データ ストレージ]** の下の **[コンテナー]** を選びます。

1. `sap bits` コンテナーを選択します。

1. コンテナー ページで、アーカイブとツールをアップロードします。

    1. **[アップロード]** ボタンを選択します。

    1. **[ファイルの選択]** を選択します。

    1. 前に作成したダウンロード ディレクトリに移動します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [SAP アプリケーション BOM を生成する方法](automation-bom-templates-db.md)
