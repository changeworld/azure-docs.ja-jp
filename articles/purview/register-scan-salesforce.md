---
title: Salesforce に接続して管理する
description: このガイドでは、Azure Purview で Salesforce に接続する方法と、Purview の機能を使用して Salesforce ソースをスキャンおよび管理する方法について説明します。
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to
ms.openlocfilehash: 81f4775771c162dce061bc1ad8901bd9b9542d40
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132554942"
---
# <a name="connect-to-and-manage-salesforce-in-azure-purview-preview"></a>Azure Purview で Salesforce に接続して管理する (プレビュー)

この記事では、Azure Purview で Salesforce を登録する方法、および Salesforce を認証して操作する方法について説明します。 Azure Purview の詳細については、[概要の記事](overview.md)を参照してください。

> [!IMPORTANT]
> ソースとしての Salesforce は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

## <a name="supported-capabilities"></a>サポートされる機能

|**メタデータの抽出**|  **フル スキャン**  |**増分スキャン**|**スコープ スキャン**|**分類**|**アクセス ポリシー**|**系列**|
|---|---|---|---|---|---|---|
| [あり](#register)| [はい](#scan)| いいえ | いいえ | いいえ | いいえ| いいえ|

Salesforce をスキャンする場合、Purview では、Salesforce の組織、オブジェクト、フィールド、外部キー、unique_constraints を含むメタデータの抽出がサポートされます。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* アクティブな [Purview リソース](create-catalog-portal.md)。

* Purview Studio でソースを登録して管理するには、データ ソース管理者およびデータ閲覧者である必要があります。 詳細については、[Azure Purview のアクセス許可](catalog-permissions.md)に関するページを参照してください。

* 最新の[セルフホステッド統合ランタイム](https://www.microsoft.com/download/details.aspx?id=39717)を設定します。 詳細については、[セルフホステッド統合ランタイムの作成および構成ガイド](../data-factory/create-self-hosted-integration-runtime.md)に関する記事を参照してください。

* セルフホステッド統合ランタイムがインストールされている仮想マシンに [JDK 11](https://www.oracle.com/java/technologies/javase/jdk11-archive-downloads.html) がインストールされていることを確認します。

* セルフホステッド統合ランタイム マシンに Visual Studio 2012 Update 4 の Visual C++ 再頒布可能パッケージがインストールされていることを確認します。 この更新プログラムがインストールされていない場合は、[ここからダウンロードできます](https://www.microsoft.com/download/details.aspx?id=30679)。

* セルフホステッド統合ランタイム マシンの IP が、Salesforce に設定されている[組織の信頼された IP 範囲](https://help.salesforce.com/s/articleView?id=sf.security_networkaccess.htm&type=5)内に含まれていることを確認してください。 そうでない場合、信頼されていないネットワークから Salesforce に対する認証を行うためにセキュリティ トークンを追加で指定する必要があります。 詳細については、「[スキャン](#scan)」セクションの資格情報の構成に関する説明を参照してください。

* ユーザーが Salesforce ドキュメントを送信する場合は、標準オブジェクトとカスタム オブジェクトに対してこのアクセスを許可するように特定のセキュリティ設定を構成する必要があります。 権限を構成するには、次の手順に従います。
    - Salesforce 内で、[設定] をクリックし、[ユーザーの管理] をクリックします。
    - [ユーザーの管理] ツリーで、[プロファイル] をクリックします。
    - 右側に [プロファイル] が表示されたら、編集するプロファイルを選択し、対応するプロファイルの横にある [編集] リンクをクリックします。
    
    標準オブジェクトの場合は、[ドキュメント] セクションで読み取り権限が選択されていることを確認します。 カスタム オブジェクトの場合は、各カスタム オブジェクトに対して読み取り権限が選択されていることを確認します。

## <a name="register"></a>登録

このセクションでは、[Purview Studio](https://web.purview.azure.com/) を使用して Azure Purview に Salesforce を登録する方法について説明します。

### <a name="steps-to-register"></a>登録する手順

新しい Salesforce ソースをデータ カタログに登録するには、次の手順に従います。

1. [Purview Studio](https://web.purview.azure.com/resource/) で Purview アカウントに移動します。
1. 左側のナビゲーションで **[Data Map]** を選択します。
1. **[登録]** を選択します
1. [ソースの登録] で、 **[Salesforce]** を選択します。 **[続行]** をクリックします。

**[Register sources (Salesforce)]\(ソースの登録 (Salesforce)\)** 画面で、次のようにします。

1. データ ソースがカタログに一覧表示されるときの **名前** を入力します。

1. **ドメイン URL** として、Salesforce のログイン エンドポイントの URL を入力します。 たとえば、「 `https://login.salesforce.com` 」のように入力します。 会社のインスタンス URL (`https://na30.salesforce.com` など) やマイ ドメイン URL (`https://myCompanyName.my.salesforce.com/` など) を使用できます。

1. コレクションを選択するか、新しいものを作成します (省略可能)

1. データ ソースの登録を終了します。

    :::image type="content" source="media/register-scan-salesforce/register-sources.png" alt-text="ソースの登録のオプション" border="true":::

## <a name="scan"></a>スキャン

次の手順に従って、Salesforce をスキャンし、自動的に資産を識別してデータを分類します。 スキャン全般の詳細については、[スキャンとインジェストの概要](concept-scans-and-ingestion.md)に関するページを参照してください。

Azure Purview は、Salesforce REST API バージョン 41.0 を使用してメタデータを抽出します。これには、"Describe Global" URI (/v41.0/sobjects/)、"sObject Basic Information" URI (/v41.0/sobjects/sObject/)、"SOQL Query" URI (/v41.0/query?) などの REST 要求が含まれます。

### <a name="create-and-run-scan"></a>スキャンの作成と実行

新しいスキャンを作成して実行するには、次の操作を行います。

1. 管理センターで、[統合ランタイム] を選択します。 セルフホステッド統合ランタイムが設定されていることを確認してください。 設定されていない場合は、[こちら](./manage-integration-runtimes.md)に記載されている手順を使用して、セルフホステッド統合ランタイムを作成します。

1. **[ソース]** に移動します。

1. 登録した Salesforce ソースを選択します。

1. **[+ 新しいスキャン]** を選択します。

1. 次の詳細を指定します。

    1. **[名前]** : スキャンの名前

    1. **[統合ランタイム経由で接続]** : 構成済みのセルフホステッド統合ランタイムを選択します

    1. **[資格情報]** : 対象のデータ ソースに接続するための資格情報を選択します。 次のことを確認します。
        * 資格情報の作成時に **コンシューマー キー** を選択します。
        * 接続アプリが模倣しているユーザーのユーザー名を [ユーザー名] 入力フィールドに入力します。
        * 接続アプリが模倣しているユーザーのパスワードを Azure Key Vault シークレットに保管します。 
            * セルフホステッド統合ランタイム マシンの IP が、Salesforce に設定されている[組織の信頼された IP 範囲](https://help.salesforce.com/s/articleView?id=sf.security_networkaccess.htm&type=5)内に含まれている場合は、ユーザーのパスワードのみ指定します。
            * そうでない場合は、シークレットの値としてパスワードとセキュリティ トークンを連結します。 セキュリティ トークンは、自動的に生成されるキーであり、信頼されていないネットワークから Salesforce にログインするときにパスワードの末尾に追加する必要があります。 [セキュリティ トークンを取得またはリセット](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm)する方法の詳細を確認してください。
        * 接続アプリの定義にあるコンシューマー キーを指定します。 これは、接続アプリの [接続アプリケーションを管理する] ページまたは接続アプリの定義で確認できます。
        * 接続アプリの定義にあるコンシューマー シークレットを Azure Key Vault シークレットに保管します。 これはコンシューマー キーと一緒に見つかります。

    1. **オブジェクト**: スキャンの範囲を指定するために、オブジェクト名のリストを指定します。 たとえば、「 `object1; object2` 」のように入力します。 リストが空の場合は、使用可能なすべてのオブジェクトが取得されます。 オブジェクト名をワイルドカード パターンとして指定できます。 たとえば、「`topic?`」、「`*topic*`」、「`topic_?,*topic*`」のように指定します。

    1. **[Maximum memory available]\(使用可能な最大メモリ\):** スキャン プロセスで使用される、顧客の VM で使用可能な最大メモリ (GB 単位)。 これは、スキャンする Salesforce ソースのサイズによって異なります。

        > [!Note]
        > 経験則として、テーブル 1,000 個ごとに 1 GB のメモリを用意してください

        :::image type="content" source="media/register-scan-salesforce/scan.png" alt-text="Salesforce のスキャン" border="true":::

1. **[続行]** を選択します。

1. **スキャン トリガー** を選択します。 スケジュールを設定することも、1 回限りのスキャンを実行することもできます。

1. 自分のスキャンを確認し、 **[保存および実行]** を選択します。

[!INCLUDE [create and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>次のステップ

ソースの登録が完了したので、以下のガイドに従って Azure Purview とご利用のデータの詳細について学習します。

- [Azure Purview のデータ分析情報](concept-insights.md)
- [Azure Purview のデータ系列](catalog-lineage-user-guide.md)
- [Data Catalog の検索](how-to-search-catalog.md)
