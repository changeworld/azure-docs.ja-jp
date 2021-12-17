---
title: erwin Mart サーバーに接続して管理する
description: このガイドでは、Azure Purview で erwin Mart サーバーに接続する方法と、Purview の機能を使用して erwin Mart サーバー ソースをスキャンおよび管理する方法について説明します。
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 0a6aef0cdbf55772ada02bef7090ccc3165b5658
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131472449"
---
# <a name="connect-to-and-manage-erwin-mart-servers-in-azure-purview-preview"></a>Azure Purview で erwin Mart サーバーに接続して管理する (プレビュー)

この記事では、Azure Purview で、erwin Mart サーバーを登録する方法と、erwin Mart サーバーを認証して操作する方法について説明します。 Azure Purview の詳細については、[概要の記事](overview.md)を参照してください。

> [!IMPORTANT]
> ソースとしての erwin Mart サーバーは現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

## <a name="supported-capabilities"></a>サポートされる機能

|**メタデータの抽出**|  **フル スキャン**  |**増分スキャン**|**スコープ スキャン**|**分類**|**アクセス ポリシー**|**系列**|
|---|---|---|---|---|---|---|
| [あり](#register)| [あり](#scan)| いいえ | いいえ | いいえ | いいえ| [はい](how-to-lineage-erwin.md)|

erwin ソースを使用すると、erwin Mart サーバーからメタデータを抽出するためのフル スキャンがサポートされます。 メタデータには次のものが含まれます。

1. エンティティ、属性、ドメインを持つ論理限定モデル、または
1. テーブル、列、データ型を持つ物理限定モデル、または
1. 論理または物理モデル

> [!Important]
> サポートされている erwin Mart のバージョンは 9.x から 2021 までです。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* アクティブな [Purview リソース](create-catalog-portal.md)。

* Purview Studio でソースを登録して管理するには、データ ソース管理者およびデータ閲覧者である必要があります。 詳細については、[Azure Purview のアクセス許可](catalog-permissions.md)に関するページを参照してください。

* 最新の[セルフホステッド統合ランタイム](https://www.microsoft.com/download/details.aspx?id=39717)を設定します。 詳細については、[セルフホステッド統合ランタイムの作成および構成ガイド](../data-factory/create-self-hosted-integration-runtime.md)に関する記事を参照してください。

    > [!Note]
    > セルフホステッド統合ランタイムは、必ず erwin Mart インスタンスが実行されている VM で実行してください。

* セルフホステッド統合ランタイムがインストールされている仮想マシンに [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) がインストールされていることを確認します。

* セルフホステッド統合ランタイム マシンに Visual Studio 2012 Update 4 の Visual C++ 再頒布可能パッケージがインストールされていることを確認します。 この更新プログラムがインストールされていない場合は、[ここからダウンロードできます](https://www.microsoft.com/download/details.aspx?id=30679)。

## <a name="register"></a>登録

このセクションでは、[Purview Studio](https://web.purview.azure.com/) を使用して Azure Purview に erwin Mart サーバーを登録する方法について説明します。

erwin Mart ソースでサポートされている認証は、ユーザー名とパスワードの形式での **サーバー認証** のみです。

### <a name="steps-to-register"></a>登録する手順

1. [Purview Studio](https://web.purview.azure.com/) で Purview アカウントに移動します。
1. 左側のナビゲーションで **[Data Map]** を選択します。
1. **[登録]** を選択します
1. [Register sources]\(ソースの登録\) で、 **[erwin]** を選択します。 **[続行]** を選択します。
    :::image type="content" source="media/register-scan-erwin-source/register-sources.png" alt-text="erwin ソースの登録" border="true":::

ソースの登録 (erwin) 画面で、次のようにします。

1. データ ソースがカタログに一覧表示されるときの **名前** を入力します。
1. erwin Mart の **サーバー名** を入力します。 これは、erwin Mart サーバーへの接続に使用されるネットワーク ホスト名です。 たとえば、localhost です
1. erwin Mart に接続するときに使用される **ポート** 番号を入力します。 この値は既定では 18170 です。
1. **アプリケーション名** を入力します

    >[!Note]
    > 上記の詳細は、erwin Data Modeler に移動することで確認できます。 [Mart]、[接続] の順にクリックし、サーバー名、ポート、アプリケーション名に関連した詳細を確認します。

    :::image type="content" source="media/register-scan-erwin-source/erwin-details.png" alt-text="erwin の詳細の確認" border="true":::

1. コレクションを選択するか、新しいものを作成します (省略可能)

1. データ ソースの登録を終了します。

    :::image type="content" source="media/register-scan-erwin-source/register-erwin.png" alt-text="ソースの登録" border="true":::

## <a name="scan"></a>スキャン

次の手順に従って、erwin Mart サーバーをスキャンし、自動的に資産を識別してデータを分類します。 スキャン全般の詳細については、[スキャンとインジェストの概要](concept-scans-and-ingestion.md)に関するページを参照してください。

### <a name="create-and-run-scan"></a>スキャンの作成と実行

新しいスキャンを作成して実行するには、次の操作を行います。

1. 管理センターで、[統合ランタイム] を選択します。 erwin Mart インスタンスが実行されている VM にセルフホステッド統合ランタイムが設定されている必要があります。 設定されていない場合は、[こちら](./manage-integration-runtimes.md)に記載されている手順に従って、セルフホステッド統合ランタイムを設定します。
1. **[ソース]** に移動します。

1. 登録した **erwin** Mart を選択します。

1. **[+ 新しいスキャン]** を選択します。

1. 次の詳細を指定します。

    1. **[名前]** : スキャンの名前

    1. **[統合ランタイム経由で接続]** : 構成済みのセルフホステッド統合ランタイムを選択します。

    1. **[サーバー名]、[ポート]** および **[アプリケーション名]** は、登録時に入力した値に基づいて自動的に設定されます。

    1. **資格情報:** erwin Mart サーバーに接続するように構成された資格情報を選択します。 資格情報を作成する際は、次の点にご注意ください。
        * 認証方法として **[基本認証]** を選択します
        * [ユーザー名] フィールドに、erwin Mart サーバーのユーザー名を入力します。
        * サーバー認証用のユーザー パスワードをキー コンテナーのシークレットに保存します。

        資格情報の詳細については、[こちら](manage-credentials.md)のリンクを参照してください。

    1. **インターネット インフォメーション サービス (IIS) を使用する** - erwin Mart サーバーに接続するときに Microsoft インターネット インフォメーション サービス (IIS) を使用する必要があるかどうかを通知するために、[True] または [False] を選択します。 既定では、この値は False に設定されます。

    1. **Secure Sockets Layer (SSL) を使用する** - erwin Mart サーバーに接続するときに Secure Sockets Layer (SSL) を使用する必要があるかどうかを通知するために、[True] または [False] を選択します。 既定では、この値は Falseに設定されます。

        > [!Note]
        > このパラメーターは、erwin Mart バージョン 9.1 以降にのみ適用されます。

    1. **ブラウズ モード** - erwin Mart を参照するモードを選択します。 使用できるオプションは、"ライブラリとモデル" または "ライブラリのみ" です。

    1. **モデル** - セミコロンで区切られた erwin モデル ロケーター文字列のリストを指定して、スキャンのスコープを設定します。 例: mart://Mart/Samples/eMovies;mart://Mart/Erwin_Tutorial/AP_Physical

    1. **[Maximum memory available]\(使用可能な最大メモリ\):** スキャン プロセスで使用される、顧客の VM で使用可能な最大メモリ (GB 単位)。 これは、スキャンする erwin Mart のサイズによって異なります。

    :::image type="content" source="media/register-scan-erwin-source/setup-scan.png" alt-text="スキャンのトリガー" border="true":::

1. **[テスト接続]** を選択します。

1. **[続行]** を選択します。

1. **スキャン トリガー** を選択します。 スケジュールを設定することも、1 回限りのスキャンを実行することもできます。

1. 自分のスキャンを確認し、 **[保存および実行]** を選択します。

[!INCLUDE [create and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>次のステップ

ソースの登録が完了したので、以下のガイドに従って Azure Purview とご利用のデータの詳細について学習します。

- [Azure Purview のデータ分析情報](concept-insights.md)
- [Azure Purview のデータ系列](catalog-lineage-user-guide.md)
- [Data Catalog の検索](how-to-search-catalog.md)
