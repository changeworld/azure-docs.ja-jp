---
title: Azure Functions を使用して Microsoft Sentinel をデータ ソースに接続する | Microsoft Docs
description: Azure Functions を使用して、データソースから Microsoft Sentinel にデータを取り込むデータ コネクタを構成する方法について説明します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 6e48372706bb5132054b46415fc5b87746758655
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132722470"
---
# <a name="use-azure-functions-to-connect-microsoft-sentinel-to-your-data-source"></a>Azure Functions を使用して Microsoft Sentinel をデータ ソースに接続する

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[Azure Functions](../azure-functions/functions-overview.md) を、[PowerShell](../azure-functions/functions-reference-powershell.md) や Python などのさまざまなコーディング言語と組み合わせて使用して、互換性のあるデータ ソースの REST API エンドポイントへのサーバーレス コネクタを作成することができます。 Azure 関数アプリにより、データ ソースの REST API に Microsoft Sentinel を接続してログをプルすることができます。

この記事では、Azure 関数アプリを使用するために Microsoft Sentinel を構成する方法について説明します。 さらに、ソース システムを構成する必要がある場合もありますが、ポータルの各データ コネクタのページ、または [Microsoft Sentinel データ コネクタのリファレンス](data-connectors-reference.md) ページのお使いのサービスについてのセクションで、ベンダー固有および製品固有の情報リンクを見つけることができます。




> [!NOTE]
> - データは、Microsoft Sentinel に取り込まれると、Microsoft Sentinel を実行しているワークスペースの地理的な場所に保存されます。
>
>     長期間の保有の場合、Azure Data Explorer にデータを保存したいと考える場合もあります。 詳細については、[Azure Data Explorer の統合](store-logs-in-azure-data-explorer.md)に関するページを参照してください。
>
> - Azure Functions を使用して Microsoft Sentinel にデータを取り込むと、追加のデータ インジェスト コストが発生する可能性があります。 詳細については、[Azure Functions の価格](https://azure.microsoft.com/pricing/details/functions/)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

Azure Functions を使用して Microsoft Sentinel をデータ ソースに接続し、そのログを Microsoft Sentinel にプルする前に、次のアクセス許可と資格情報があることを確認してください。

- Microsoft Sentinel ワークスペースに対する読み取りおよび書き込みアクセス許可が必要です。

- ワークスペースの共有キーに対する読み取りアクセス許可が必要です。 ワークスペース キーの詳細については、[こちら](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key)を参照してください。

- 関数アプリを作成するための、Azure Functions に対する読み取りと書き込みのアクセス許可が必要です。 [Azure Functions](../azure-functions/index.yml) の詳細を確認してください。

- また、製品の API にアクセスするための資格情報 (ユーザー名とパスワード、トークン、キー、またはその他の組み合わせ) も必要になります。 さらにエンドポイント URI などの他の API 情報が必要な場合もあります。

    詳細については、接続先のサービスのドキュメントおよび、[Microsoft Sentinel データ コネクタのリファレンス](data-connectors-reference.md) ページのお使いのサービスについてのセクションを参照してください。

## <a name="configure-and-connect-your-data-source"></a>データ ソースを構成して接続する

> [!NOTE]
> - Azure Key Vault には、ワークスペースと API の認可キーまたはトークンを安全に格納できます。 Azure Key Vault には、キー値を格納および取得するためのセキュリティで保護されたメカニズムが用意されています。 Azure 関数アプリで Azure Key Vault を使用するには、[これらの手順に従います](../app-service/app-service-key-vault-references.md)。
>
> - 一部のデータ コネクタは、正常に動作するために、[Kusto 関数](/azure/data-explorer/kusto/query/functions/user-defined-functions)に基づくパーサーに依存しています。 Kusto 関数とエイリアスを作成する手順のリンクについては、[Microsoft Sentinel データ コネクタのリファレンス](data-connectors-reference.md) ページでお使いのサービスのセクションを参照してください。


### <a name="step-1---get-your-source-systems-api-credentials"></a>手順 1 - ソース システムの API 資格情報を取得する

ソース システムの指示に従って、その **API 資格情報/認可キー/トークン** を取得します。 後のために、それらをコピーして、テキスト ファイルに貼り付けます。

必要となる正確な資格情報の詳細と、それらを見つけて作成するための製品の手順のリンクは、ポータルのデータ コネクタ ページと、[Microsoft Sentinel データ コネクタのリファレンス](data-connectors-reference.md) ページのお使いのサービスについてのセクションで参照できます。

また、ソース システムでログまたはその他の設定を構成する必要がある場合もあります。 それらと関連する手順については、前の段落を参照してください。
### <a name="step-2---deploy-the-connector-and-the-associated-azure-function-app"></a>手順 2 - コネクタと関連付けられている Azure 関数アプリをデプロイする

#### <a name="choose-a-deployment-option"></a>配置オプションを選択する

# <a name="azure-resource-manager-arm-template"></a>[Azure Resource Manager (ARM) テンプレート](#tab/ARM)

この方法により、ARM テンプレートを使用して Azure 関数ベースのコネクタを自動的にデプロイできます。

1. Microsoft Sentinel ポータルで **[Data connectors]\(データ コネクタ\)** を選択します。 一覧から Azure Functions ベースのコネクタを選択し、**コネクタ ページを開きます**。

1. **[構成]** で、Microsoft Sentinel の **[ワークスペース ID]** と **[主キー]** をコピーし、控えておきます。

1. **[Azure に配置する]** を選択します。 (ボタンを見つけるために、下へスクロールする必要がある場合があります。)

1. **[カスタム デプロイ]** 画面が表示されます。
    - 関数アプリをデプロイする **サブスクリプション**、**リソース グループ**、**リージョン** を選択します。

    - 上記の[手順 1](#step-1---get-your-source-systems-api-credentials) で保存した API 資格情報/認可キー/トークンを入力します。

    - Microsoft Sentinel の **[ワークスペース ID]** と **[ワークスペース キー]** (主キー) に、先ほどコピーした値を入力します。

        > [!NOTE]
        > 上記のいずれかの値に対して Azure Key Vault シークレットを使用する場合は、文字列値の代わりに `@Microsoft.KeyVault(SecretUri={Security Identifier})` スキーマを使用します。 詳細については、Key Vault のリファレンスのドキュメントを参照してください。

    - **[カスタム デプロイ]** 画面で、フォームのその他のフィールドを入力します。 ポータルのデータ コネクタ ページ、または [Microsoft Sentinel データ コネクタのリファレンス](data-connectors-reference.md) ページでお使いのサービスのセクションを参照してください。

    - **[Review + create]\(レビュー + 作成\)** を選択します。 検証が完了したら、 **[作成]** を選択します。

# <a name="manual-deployment-with-powershell"></a>[PowerShell を使用した手動デプロイ](#tab/MPS)

次の手順を使用して、PowerShell 関数を使用する Azure Functions ベースのコネクタを手動でデプロイします。

1. Microsoft Sentinel ポータルで **[Data connectors]\(データ コネクタ\)** を選択します。 一覧から Azure Functions ベースのコネクタを選択し、**コネクタ ページを開きます**。

1. **[構成]** で、Microsoft Sentinel の **[ワークスペース ID]** と **[主キー]** をコピーし、控えておきます。

1. **関数アプリを作成する**
    1. Azure portal から、**関数アプリ** を検索して選択します。

    1. **[関数アプリ]** ページで、 **[作成]** を選択します。 **関数アプリの作成** ウィザードが開きます。

    1. **[基本]** タブを次のように設定します。
        - **サブスクリプション** と **リソース グループ** を選択します。
        - 関数アプリに名前を付けます。
        - **[ランタイム スタック]** を *[PowerShell Core]* に設定します。
        - 適切なバージョン番号を選択します。
        - デプロイする **リージョン** を選択し、 **[次へ: ホスティング]** を選択します。

    1. **[ホスティング]** タブで:
        - 使用する **ストレージ アカウント** を選択するか、新しいアカウントを作成します。
        - **[プランの種類]** として、 *[重量課金プラン (サーバーレス)]* を選択します。

    1. その他の必要な構成変更を行い、 **[確認および作成]** を選択します。

    1. "検証に成功しました" メッセージを待機してから、 **[作成]** を選択します。

    1. デプロイが完了したら、 **[リソースに移動]** を選択します。

1. **関数アプリ コードをインポートする**
    1. 新しく作成した関数アプリで、ナビゲーションメニューから **[関数]** を選択します。

    1. **[関数]** ページで、 **[+ 追加]** を選択します。
    
    1. **[関数の追加]** パネルで、 **[タイマー]** を選択します。

    1. 関数の一意の名前を入力し、*cron* 式を入力してスケジュールを指定します。 既定の間隔は 5 分ごとです。

    1. 関数が作成されたら、左ウィンドウで **[コードとテスト]** を選択します。

    1. ソース システムのベンダーから提供されている関数アプリ コードをダウンロードし、コピーして、**関数アプリ** *run.ps1* エディターに貼り付けて、既定でそこにある内容を置き換えます。 ダウンロード リンクは、コネクタ ページ、または [Microsoft Sentinel データ コネクタのリファレンス](data-connectors-reference.md) ページでお使いのサービスのセクションにあります。

    1. **[保存]** を選択します。

1. **関数アプリを構成する**
    1. 関数アプリのページで、ナビゲーション メニューの **[設定]** の下の **[構成]** を選択します。

    1. **[アプリケーションの設定]** タブで、 **[+ 新しいアプリケーション設定]** を選択します。

    1. 製品について規定されているアプリケーション設定を、大文字と小文字を区別する各文字列値で個別に追加します。 データ コネクタ ページ、または [Microsoft Sentinel データ コネクタのリファレンス](data-connectors-reference.md) ページでお使いのサービスについてのセクションの製品のセクションを参照してください。

        > [!TIP]
        > 必要に応じて、専用クラウドを使用している場合は、*logAnalyticsUri* アプリケーション設定を使用して、ログ分析 API エンドポイントを上書きします。 そのため、たとえば、パブリック クラウドを使用している場合は、値を空のままにします。Azure GovUS クラウド環境では、`https://<CustomerId>.ods.opinsights.azure.us` の形式で値を指定します。
        >

# <a name="manual-deployment-with-python"></a>[Python を使用した手動デプロイ](#tab/MPY)

次の手順を使用して、Python 関数を使用する Azure Functions ベースのコネクタを手動でデプロイします。 この種類のデプロイには Visual Studio Code が必要です。

1. Microsoft Sentinel ポータルで **[Data connectors]\(データ コネクタ\)** を選択します。 一覧から Azure Functions ベースのコネクタを選択し、**コネクタ ページを開きます**。

1. **[構成]** で、Microsoft Sentinel の **[ワークスペース ID]** と **[主キー]** をコピーし、控えておきます。

1. **関数アプリをデプロイする**

    > [!NOTE]
    > Azure 関数の開発には [Visual Studio Code (VS Code) を準備する](../azure-functions/create-first-function-vs-code-python.md)必要があります。

    1. Azure 関数アプリ ファイルをダウンロードするには、データ コネクタ ページおよび [Microsoft Sentinel データ コネクタのリファレンス](data-connectors-reference.md) ページでお使いのサービスのセクションに提供されているリンクを使用します。 アーカイブをローカル開発用コンピューターに抽出します。

    1. VS Code を起動します。 メニュー バーから、 **[ファイル] > [フォルダーを開く...]** を選択します。

    1. アーカイブから抽出されたファイルから、トップレベル フォルダーを選択します。

    1. VS Code [アクティビティ] バー (左側) の Azure アイコンを選択します。 **[Azure: Functions]** 領域で、 **[関数アプリにデプロイ]** ボタンを選択します。 

        > [!NOTE]
        > まだサインインしていない場合は、[アクティビティ] バーの Azure アイコンを選択します。 **[Azure: Functions]** 領域で、 **[Azure にサインインする...]** を選択します。  
        > 既にサインインしている場合は、次の手順に進みます。

    1. プロンプトで、次の情報を入力します。
        - **フォルダーを選択してください**: ワークスペースのフォルダーを選択するか、関数アプリが格納されているフォルダーを参照します。
        - **Select subscription (サブスクリプションを選択してください)** : 使用するサブスクリプションを選択します。
        - **[Azure で新しい関数アプリを作成する]** を選択します。 ( **[詳細]** オプションを選択しないでください。)
        - **関数アプリのグローバルに一意の名前を入力します**: 関数アプリに URL パスで有効な名前を付けます。 選択した名前が検証され、Azure Functions 全体でそれが一意であることが確認されます。
        - **ランタイムを選択してください**: *[Python 3.8]* を選択します。

    1. デプロイが開始されます。 関数アプリが作成され、展開パッケージが適用されると、通知が表示されます。

    1. 構成のために関数アプリのページに戻ります。

1. **関数アプリを構成する**
    1. 関数アプリのページで、ナビゲーション メニューの **[設定]** の下の **[構成]** を選択します。

    1. **[アプリケーションの設定]** タブで、 **[+ 新しいアプリケーション設定]** を選択します。

    1. 製品について規定されているアプリケーション設定を、大文字と小文字を区別する各文字列値で個別に追加します。 追加するアプリケーション設定については、データ コネクタ ページ、または [Microsoft Sentinel データ コネクタのリファレンス](data-connectors-reference.md) ページでお使いのサービスについてのセクションを参照してください。

        - 必要に応じて、専用クラウドを使用している場合は、*logAnalyticsUri* アプリケーション設定を使用して、ログ分析 API エンドポイントを上書きします。 そのため、たとえば、パブリック クラウドを使用している場合は、値を空のままにします。Azure GovUS クラウド環境では、`https://<CustomerId>.ods.opinsights.azure.us` の形式で値を指定します。

---

## <a name="find-your-data"></a>データの検索

接続が正常に確立されると、[Microsoft Sentinel データ コネクタのリファレンス](data-connectors-reference.md) ページのお使いのサービスについてのセクションに記載されている表で、 *[CustomLogs]* の下の **[ログ]** にデータが表示されます。

データのクエリを実行するには、クエリ ウィンドウで、それらのいずれかのテーブル名 (または関連する Kusto 関数エイリアス) を入力します。

いくつかの便利なサンプル クエリについては、コネクタ ページの **[Next steps]\(次の手順\)** タブを参照してください。

## <a name="validate-connectivity"></a>接続の検証

ログが Log Analytics に表示され始めるまで、最大 20 分かかることがあります。 

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Azure Functions ベースのコネクタを使用して Microsoft Sentinel をデータ ソースに接続する方法について説明しました。 Microsoft Sentinel の詳細については、次の記事を参照してください。

- [データと潜在的な脅威を可視化](./get-visibility.md)する方法についての説明。
- [Microsoft Sentinel を使用した脅威の検出](./detect-threats-built-in.md)の概要。
- [ブックを使用](./monitor-your-data.md)してデータを監視する。
