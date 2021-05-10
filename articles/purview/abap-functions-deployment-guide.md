---
title: SAP R3 のメタデータ抽出 ABAP 関数モジュール - Azure Purview
description: この記事では、SAP サーバーに ABAP 関数モジュールをデプロイする手順の概要について説明します
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/13/2020
ms.openlocfilehash: 9bd3c315fcc15317a9fa483289fdc326ca6aa47f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102614362"
---
# <a name="deploy-the-metadata-extraction-abap-function-module-for-the-sap-r3-family-of-bridges"></a>SAP R3 ブリッジ ファミリ用のメタデータ抽出 ABAP 関数モジュールをデプロイする

この記事では、SAP サーバーに ABAP 関数モジュールをデプロイする手順の概要について説明します。

## <a name="overview"></a>概要

Sap Business Suite 4 HANA (S/4HANA)、ECC、R/3 ERP ブリッジを使用して、SAP サーバーからメタデータを抽出できます。 これを実現するには、ABAP 関数モジュールを SAP サーバーに配置します。 ブリッジからこの関数モジュールにリモート アクセスして、SAP サーバーに含まれるメタデータのクエリと (テキスト ファイルとしての) ダウンロードが行われます。

実行されると、ブリッジによって次のことが行われます。

1. 以前のブリッジの実行によってローカル環境に既にダウンロードされている既存のファイルから、メタデータがインポートされます。

2. ABAP モジュールの API が呼び出され、ダウンロードが完了するまで待機された後、そのファイルからメタデータがインポートされます。

このドキュメントでは、このモジュールをデプロイするために必要な手順について詳しく説明します。

> [!Note]
> 以下の手順は、SAP GUI v.7.2 に基づいてコンパイルされました

## <a name="deployment-of-the-module"></a>モジュールのデプロイ

### <a name="create-a-package"></a>パッケージを作成する

この手順は省略可能であり、既存のパッケージを使用できます。

1. SAP S/4HANA または SAP ECC サーバーにログインし、**オブジェクト ナビゲーター** (SE80 トランザクション) を開きます。

2. 一覧から **[パッケージ]** オプションを選択し、新しいパッケージの名前 (例: Z\_MITI) を入力して、 **[表示]** ボタンを選択します。

3. **[パッケージの作成]** ウィンドウで **[はい]** を選択します。 その結果、 **[Package Builder: パッケージの作成]** ウィンドウが開きます。 **[短い説明]** フィールドに値を入力し、 **[続行]** アイコンを選択します。

4. **[ローカルワークベンチ要求のプロンプト]** ウィンドウで、 **[独自の要求]** を選択します。 **[開発]** 要求を選択します。

### <a name="create-a-function-group"></a>関数グループを作成する

オブジェクト ナビゲーターで、一覧から **[Function Group]\(関数グループ\)** を選択し、下の入力フィールドに名前を入力します (例: Z\_MITI\_FGROUP)。 **[表示]** アイコンを選択します。

1. **[オブジェクトの作成]** ウィンドウで **[はい]** を選択して新しい関数グループを作成します。

2. **[Short text]\(短いテキスト\)** フィールドに適切な説明を入力し、**[Save]\(保存\)** を選択します。

3. 前の手順「**パッケージを作成する**」で準備したパッケージを選択し、 **[保存]** アイコンをクリックします。

4. **[Continue]\(続行\)** アイコンを選択して要求を確認します。

5. 関数グループをアクティブにします。

### <a name="create-the-abap-function-module"></a>ABAP 関数モジュールを作成する

1. 関数グループが作成されたら選択します。

2. リポジトリ ブラウザーで関数グループ名を右クリックし、 **[作成]** を選択して、次に **[関数モジュール]** を選択します。

3. **[関数モジュール]** フィールドに「`Z_MITI_DOWNLOAD`」と入力します。 **[短いテキスト]** の入力項目に適切な説明を入力します。

モジュールが作成されたら、次の情報を指定します。

1. **[Attributes]\(属性\)** タブに移動します。

2. **[処理の種類]** で **[リモート対応関数モジュール]** を選択します。

   :::image type="content" source="media/abap-functions-deployment-guide/processing-type.png" alt-text="ソースの登録オプション - リモート対応関数モジュール" border="true":::

3. **[Source code]\(ソース コード\)** タブに移動します。関数のコードをデプロイするには 2 つの方法があります。

   a. メイン メニューから、テキスト ファイル [Z\_MITI\_DOWNLOAD](https://github.com/Azure/Purview-Samples/tree/master/connectors/sap) ファイルをアップロードします。 これを行うには、 **[ユーティリティ]** 、 **[その他のユーティリティ]** 、 **[アップロード/ダウンロード]** 、 **[アップロード]** の順に選択します。

   b. または、ファイルを開き、その内容をコピーして、**[Source code]\(ソース コード\)** 領域に貼り付けます。

4. **[Import]\(インポート\)** タブに移動し、次のパラメーターを作成します。

   a.  P\_AREA TYPE DD02L-TABNAME ([Optional]\(省略可能\) = オン)

   b.  *P\_LOCAL\_PATH TYPE STRING* ([Optional]\(省略可能\) = オン)

   c.  *P\_LANGUAGE TYPE L001TAB-DATA DEFAULT \'E\'*

   d.  *ROWSKIPS TYPE SO\_INT DEFAULT 0*

   e.  *ROWCOUNT TYPE SO\_INT DEFAULT 0*

   > [!Note]
   > **[Pass Value]\(値渡し\)** はすべてでオンにします

   :::image type="content" source="media/abap-functions-deployment-guide/import.png" alt-text="ソースの登録オプション - インポート パラメーター" border="true":::

5. [Tables]\(テーブル\) タブに移動し、次のように定義します。

   `EXPORT_TABLE LIKE TAB512`

   :::image type="content" source="media/abap-functions-deployment-guide/export-table.png" alt-text="ソースの登録オプション - [テーブル] タブ" border="true":::

6. **[例外]** タブに移動し、次の例外を定義します: `E_EXP_GUI_DOWNLOADFAILED`

   :::image type="content" source="media/abap-functions-deployment-guide/exceptions.png" alt-text="ソースの登録オプション - [例外] タブ" border="true":::

7. 関数を保存します (Ctrl + S キーを押すか、メイン メニューの **[関数モジュール]** を選択して **[保存]** を選択します)。

8. ツール バーの **[アクティブ化]** アイコンをクリックし (Ctrl + F3 キー)、ダイアログ ウィンドウの **[続行]** ボタンを選択します。 プロンプトが表示されたら、メイン関数モジュールと共にアクティブにする、生成されたインクルードを選択する必要があります。

### <a name="testing-the-function"></a>関数のテスト

前の手順をすべて完了したら、次の手順のようにして関数をテストします。

1. Z\_MITI\_DOWNLOAD 関数モジュールを開きます。

2. メイン メニューから **[関数モジュール]** を選択し、 **[テスト]** を選択してから、 **[関数モジュールのテスト]** を選択します (または、F8 キーを押します)。

3. ローカル ファイル システム上のフォルダーへのパスをパラメーター P\_LOCAL\_PATH に入力し、ツール バーの **[Execute]\(実行\)** アイコンを選択します (または、F8 キーを押します)。

4. メタデータが含まれるファイルをダウンロードまたは更新する必要がある場合は、対象となる領域の名前を P\_AREA フィールドに入力します。 関数の動作が終了すると、P\_LOCAL\_PATH パラメーターで指定したフォルダーに、メタデータが含まれる複数のファイルが格納されているはずです。 ファイルの名前は、P\_AREA フィールドで指定できる領域に似たものになります。

SAP S/4HANA または ECC サーバーとの間に高速のネットワーク接続があるマシンで関数を開始した場合、関数の実行が完了すると、メタデータがずっと速くダウンロードされます。

## <a name="next-steps"></a>次のステップ

- [SAP ECC ソースを登録してスキャンする](register-scan-sapecc-source.md)
- [SAP S/4HANA ソースを登録してスキャンする](register-scan-saps4hana-source.md)
