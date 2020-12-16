---
title: Teradata ソースを登録し、スキャンを設定する (プレビュー)
description: この記事では、Azure Purview に Teradata ソースを登録し、スキャンを設定する方法について、概要を説明します。
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 12/06/2020
ms.openlocfilehash: 170ece293f8d24f3a29774c64c14f9334fe0930c
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/07/2020
ms.locfileid: "96841685"
---
# <a name="register-and-scan-teradata-source-preview"></a>Teradata ソースを登録し、スキャンする (プレビュー)

この記事では、Purview に Teradata ソースを登録し、スキャンを設定する方法について、概要を説明します。

> [!IMPORTANT]
> 現在、このデータ ソースはプレビュー段階にあります。 実際にお試しいただき、フィードバックをお寄せください。

## <a name="supported-capabilities"></a>サポートされる機能

Teradata ソースでは **フル スキャン** がサポートされており、Teradata データベースからメタデータを抽出し、データ資産間の **系列** をフェッチできます。

## <a name="prerequisites"></a>前提条件

- コネクタは、オンプレミス ネットワーク内、Azure 仮想ネットワーク内、または Amazon Virtual Private Cloud 内だけに配置されているデータ ストアをサポートします。 したがって、これに接続するには、[セルフホステッド統合ランタイム](manage-integration-runtimes.md)を設定する必要があります。

- セルフホステッド統合ランタイムがインストールされている仮想マシンに Java Runtime Environment (JRE) がインストールされていることを確認します。
 
- セルフホステッド統合ランタイム マシンに "Visual C++ 再頒布可能パッケージ 2012 Update 4" がインストールされていることを確認します。 まだインストールしていない場合は、[こちら](https://www.microsoft.com/download/details.aspx?id=30679)からダウンロードしてください。

- オンプレミス仮想マシンに Teradata JDBC Driver という名前のドライバーを手動でインストールする必要があります。 実行可能 JAR ファイルは、[Teradata の Web サイト](https://downloads.teradata.com/)からダウンロードできます。

    > [!Note] 
    > このドライバーは、VM 内のすべてのアカウントからアクセスできる必要があります。 これをユーザー アカウントにインストールしないようにしてください。

- サポートされている Teradata データベースのバージョンは **12.x から 16.x** です。 スキャン対象の Teradata ソースに対する読み取りアクセス権があることを確認します。

### <a name="feature-flag"></a>機能フラグ

Teradata ソースの登録とスキャンは、機能フラグの後ろで使用できます。 URL の末尾に *?feature.ext.datasource=%7b"teradata":"true"%7d* を追加します 

例: 完全な URL [https://web.purview.azure.com/?feature.ext.datasource=%7b"metadata":"true"%7d](https://web.purview.azure.com/?feature.ext.datasource=%7b"teradata":"true"%7d)

## <a name="setting-up-authentication-for-a-scan"></a>スキャンのための認証を設定する
Teradata ソースの認証を設定するためにサポートされている唯一の方法は、**基本データベース** 認証です

## <a name="register-a-teradata-source"></a>Teradata ソースを登録する

新しい Teradata ソースをデータ カタログに登録するには、次の手順を実行します。

1. 自分の Purview アカウントに移動します
2. 左側のナビゲーションで **[ソース]** を選択します
3. **[登録]** を選択します
4. **[Register sources]\(ソースの登録\)** で、 **[Teradata]** を選択します
5. **[続行]** を選択します

**[Register sources (Teradata)]\(ソースの登録 (Teradata)\)** 画面で、次の手順を実行します。

1. データ ソースがカタログに表示される際の **名前** を入力します。
2. Teradata ソースに接続するための **ホスト** 名を入力します。 サーバーの IP アドレスまたは完全修飾接続文字列を指定することもできます。
3. コレクションを選択するか、新しいものを作成します (省略可能)
4. **[完了]** を選択して、データ ソースを登録します。

    :::image type="content" source="media/register-scan-teradata-source/register-sources.png" alt-text="ソースの登録のオプション" border="true":::

## <a name="creating-and-running-a-scan"></a>スキャンを作成し、実行する

新しいスキャンを作成して実行するには、次の操作を行います。
1. 管理センターで、 *[統合ランタイム]* をクリックします。 セルフホステッド統合ランタイムが設定されていることを確認してください。 設定されていない場合は、[ここ](manage-integration-runtimes.md)に記載されている手順を使用して、オンプレミス上で、またはオンプレミス ネットワークにアクセスできる Azure VM 上でスキャンするためのセルフホステッド統合ランタイムを作成します。

2. 次に、 **[ソース]** に移動します

3. 登録されている Teradata ソースを選択します。

4. [+ 新しいスキャン] を選択します
 
5. 次の詳細を指定します。

    - 名前:スキャンの名前

    - [統合ランタイム経由で接続]: 構成済みのセルフホステッド統合ランタイムを選択します

    - [認証方法]: 現在サポートされているオプションはデータベース認証のみです。 これが既定で選択されます

    - ユーザー名: データベース サーバーに接続するためのユーザー名。 このユーザー名には、サーバーへの読み取りアクセス権が必要です

    - Password (パスワード):データベース サーバーへの接続に使用されるユーザー パスワード

    - スキーマ:インポートするスキーマのサブセットをセミコロン区切りのリストとして指定します。 例: schema1;schema2。 リストが空の場合は、すべてのユーザー スキーマがインポートされます。 既定では、すべてのシステム スキーマ (SysAdmin など) とオブジェクトが無視されます。

        SQL LIKE 式の構文を使用したスキーマ名のパターンとして、% の使用も許容されます (例: A%; %B; %C%; D)
        - A で始まる、または    
        - B で終わる、または    
        - C を含む、または    
        - D と等しい

        NOT および特殊文字の使用は許容されません

    - [Driver location]\(ドライバーの場所\): 顧客の VM 上の Teradata ドライバーの場所を示す完全なパス。 Teradata JDBC ドライバー名は、com.teradata.jdbc.TeraDriver である必要があります

    - [Maximum memory available]\(使用可能な最大メモリ\): スキャン プロセスで使用される、顧客の VM で使用可能な最大メモリ (GB 単位)。 これは、スキャンする Teradata ソースのサイズによって異なります。

    > [!Note] 
    > 経験則として、テーブル 1,000 個ごとに 2 GB のメモリを用意してください

    :::image type="content" source="media/register-scan-teradata-source/setup-scan.png" alt-text="スキャンを設定する" border="true":::

6. *[続行]* をクリックします。

7. スキャン トリガーを選択します。 スケジュールを設定することも、1 回限りのスキャンを実行することもできます。

    :::image type="content" source="media/register-scan-teradata-source/scan-trigger.png" alt-text="スキャン トリガー" border="true":::

8. スキャンを確認し、 *[保存および実行]* をクリックします。

## <a name="viewing-your-scans-and-scan-runs"></a>スキャンとスキャンの実行を確認する

1. 管理センターに移動します。 **[Sources and scanning]\(ソースとスキャン\)** セクションの **[データ ソース]** を選択します。

2. 目的のデータ ソースを選択します。 そのデータ ソースに対する既存のスキャンの一覧が表示されます。

3. 結果を表示するスキャンを選択します。

4. このページには、前回のすべてのスキャン実行と、各スキャン実行のメトリックと状態が表示されます。 また、そのスキャンがスケジュールされたスキャンと手動スキャンのどちらかであるか、分類が適用された資産の数、検出された資産の合計数、スキャンの開始時刻と終了時刻、スキャンの実行時間の合計も表示されます。

## <a name="manage-your-scans"></a>スキャンを管理する

スキャンを管理または削除するには、次の操作を行います。

1. 管理センターに移動します。 **[Sources and scanning]\(ソースとスキャン\)** セクションの **[データ ソース]** を選択し、目的のデータ ソースを選択します。

2. 管理するスキャンを選択します。 スキャンを編集するには、 **[編集]** を選択します。

3. スキャンを削除するには、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

- [Azure Purview データ カタログを参照する](how-to-browse-catalog.md)
- [Azure Purview データ カタログを検索する](how-to-search-catalog.md)
