---
title: Azure Sentinel を Azure、Windows、および Microsoft サービスに接続する
description: Azure Sentinel を Azure と Microsoft 365 クラウド サービス、および Windows Server イベント ログに接続する方法について学習します。
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 08/18/2021
ms.author: yelevin
ms.openlocfilehash: c13e2e5ad14ada3c867682382b334242881ddb78
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/31/2021
ms.locfileid: "123260928"
---
# <a name="connect-azure-sentinel-to-azure-windows-microsoft-and-amazon-services"></a>Azure Sentinel を Azure、Windows、Microsoft、および Amazon サービスに接続する

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Azure Sentinel では Azure 基盤を使用して、多くの Azure と Microsoft 365 サービス、アマゾン ウェブ サービス、およびさまざまな Windows Server サービスからのデータ インジェスト用の組み込みのサービス間サポートを提供します。 これらの接続を行ういくつかの異なる方法があります。この記事では、これらの接続を行う方法について説明します。

この記事では、次の種類のコネクタについて説明します。

- **API ベース** の接続
- **診断設定** の接続。そのうちの一部は Azure Policy によって管理されます
- **Log Analytics エージェント** ベースの接続

この記事では、コネクタのグループに共通する情報を示します。 各コネクタに固有の情報については、付随の[データ コネクタのリファレンス](data-connectors-reference.md) ページを参照してください。これには、データ ストレージのライセンス前提条件や Log Analytics テーブルなどがあります。

次の統合は、より固有なものでもあり、より一般的なものでもあります。これらは、それぞれの記事で個別に扱われます。

- [Microsoft 365 Defender](connect-microsoft-365-defender.md)
- [Azure Defender](connect-azure-security-center.md)
- [Azure Active Directory](connect-azure-active-directory.md)
- [Windows セキュリティ イベント](connect-windows-security-events.md)
- [アマゾン ウェブ サービス (AWS) CloudTrail](connect-aws.md)


## <a name="api-based-connections"></a>API ベースの接続

### <a name="prerequisites"></a>前提条件

- Log Analytics ワークスペースに対する読み取りおよび書き込みアクセス許可が必要です。
- Azure Sentinel ワークスペースのテナントに対するグローバル管理者またはセキュリティ管理者ロールが必要です。

### <a name="instructions"></a>Instructions

1. Azure Sentinel のナビゲーション メニューから、 **[Data connectors]\(データ コネクタ\)** を選択します。

1. データ コネクタ ギャラリーからご利用のサービスを選び、プレビュー ペインで **[コネクタ ページを開く]** を選択します。

1. **[接続]** を選択して、サービスから Azure Sentinel にイベントやアラートのストリーミングを開始します。

1. コネクタ ページに **[Create incidents - recommended!]\(インシデントの作成 - 推奨\)** というタイトルのセクションがある状態で、アラートからインシデントを自動的に作成する場合は **[有効]** を選択します。

[データ コネクタのリファレンス](data-connectors-reference.md) ページで、サービスのコネクタのセクションに表示されるテーブル名を使用することで、各サービスのデータを検索してクエリを実行できます。

## <a name="diagnostic-settings-based-connections"></a>診断設定ベースの接続

この種類の一部のコネクタの構成は、Azure Policy によって管理されます。 手順については、下の **[Azure Policy]** タブを選択してください。 この種類の他のコネクタの場合は、 **[スタンドアロン]** タブを選択します。

# <a name="standalone"></a>[スタンドアロン](#tab/SA)

### <a name="prerequisites"></a>前提条件

Azure Sentinel にデータを取り込むには

- Azure Sentinel ワークスペースに対する読み取りおよび書き込みアクセス許可が必要です。

### <a name="instructions"></a>Instructions

1. Azure Sentinel のナビゲーション メニューから、 **[Data connectors]\(データ コネクタ\)** を選択します。

1. データ コネクタ ギャラリーからリソースの種類を選び、プレビュー ペインで **[コネクタ ページを開く]** を選択します。

1. コネクタ ページの **[構成]** セクションで、リンクを選択してリソース構成ページを開きます。

    目的の種類のリソースの一覧が表示されたら、ログを取り込むリソースのリンクを選択します。

1. リソースのナビゲーション メニューから **[診断設定]** を選択します。

1. 一覧の下部にある **[+ 診断設定を追加する]** を選択します。

1. **[診断設定]** 画面で、 **[診断設定の名前]** フィールドに名前を入力します。

    **[Log Analytics への送信]** チェック ボックスにマークを付けます。 2 つの新しいフィールドが下に表示されます。 関連する **[サブスクリプション]** と **[Log Analytics ワークスペース]** (Azure Sentinel が存在する場所) を選択します。

1. 収集するログとメトリックの種類のチェック ボックスをオンにします。 [データ コネクタのリファレンス](data-connectors-reference.md) ページで、リソースのコネクタのセクションにある各リソースの種類に推奨される選択肢を確認してください。

1. ページの最上部で **[保存]** を選択します。

# <a name="azure-policy"></a>[Azure Policy](#tab/AP)

### <a name="prerequisites"></a>前提条件

Azure Sentinel にデータを取り込むには

- Azure Sentinel ワークスペースに対する読み取りおよび書き込みアクセス許可が必要です。

- Azure Policy を使用してリソースにログ ストリーミング ポリシーを適用するには、ポリシー割り当てスコープの所有者ロールが必要です。

### <a name="instructions"></a>Instructions

この種類のコネクタでは Azure Policy を使用して、スコープとして定義された単一の種類のリソースのコレクションに、単一の診断設定構成を適用します。 特定のリソースの種類から取り込まれたログの種類は、そのリソースのコネクタ ページの左側にある **[データ型]** で確認できます。

1. Azure Sentinel のナビゲーション メニューから、 **[Data connectors]\(データ コネクタ\)** を選択します。

1. データ コネクタ ギャラリーからリソースの種類を選び、プレビュー ペインで **[コネクタ ページを開く]** を選択します。

1. コネクタ ページの **[構成]** セクションで、表示されている任意の展開コントロールを展開し、 **[Azure Policy の割り当てウィザードの起動]** ボタンを選択します。

    ポリシーの割り当てウィザードが開き、事前に設定されたポリシー名で新しいポリシーを作成できるようになります。

    1. **[基本]** タブで、 **[スコープ]** の下にある 3 つのドットがあるボタンを選択し、ご利用のサブスクリプション (および必要に応じてリソース グループ) を選びます。 説明を追加することもできます。

    1. **[パラメーター]** タブで、次の操作を行います。
       - **[Only show parameters that require input]\(入力が必要なパラメーターのみを表示する\)** チェック ボックスをオフにします。
       - **[効果]** と **[設定名]** のフィールドが表示されている場合は、そのままにしておきます。
       - **[Log Analytics ワークスペース]** ドロップダウン リストからお使いの Azure Sentinel ワークスペースを選択します。
       - 残りのドロップダウン フィールドは、使用可能な診断ログの種類を表します。 取り込み対象のすべてのログの種類を "True" とマークされたままにします。

    1. ポリシーは、今後追加されるリソースに適用されます。 ポリシーを既存のリソースにも適用するには、 **[修復]** タブを選択し、 **[修復タスクを作成する]** チェック ボックスにチェックを付けます。

    1. **[確認と作成]** タブで、 **[作成]** をクリックします。 これで、選択したスコープにポリシーが割り当てられました。

---

> [!NOTE]
>
> この種類のデータ コネクタを使用すると、過去 14 日間のある時点でデータが取り込まれた場合にのみ、接続状態インジケーター (データ コネクタ ギャラリーのカラー ストライプとデータ型の名前の横にある接続アイコン) が "*接続済み*" (緑色) と表示されます。 データ インジェストがないまま 14 日が経過すると、コネクタは切断済みと表示されます。 さらに多くのデータが入ってくると、"*接続済み*" 状態が返されます。

[データ コネクタのリファレンス](data-connectors-reference.md) ページで、リソースのコネクタのセクションに表示されるテーブル名を使用することで、各リソースの種類のデータを検索してクエリを実行できます。

## <a name="log-analytics-agent-based-connections"></a>Log Analytics エージェントベースの接続

### <a name="prerequisites"></a>前提条件

- Log Analytics ワークスペースに対する読み取りと書き込みのアクセス許可と、ログの収集元となるマシンを含むワークスペースが必要です。
- Azure Sentinel ロールに加えて、それらのワークスペースの SecurityInsights (Azure Sentinel) ソリューションに対する **Log Analytics 共同作成者** ロールが必要です。

### <a name="instructions"></a>Instructions

1. Azure Sentinel のナビゲーション メニューから、 **[Data connectors]\(データ コネクタ\)** を選択します。

1. ご利用のサービス ( **[DNS]** または **[Windows ファイアウォール]** ) を選んでから、 **[コネクタ ページを開く]** を選択します。

1. ログを生成するデバイスにエージェントをインストールし、オンボードします。

    | マシンの種類  | Instructions  |
    | --------- | --------- |
    | **Azure Windows VM の場合** | 1. **[エージェントのインストール先を選択してください]** で、 **[Azure Windows 仮想マシンにエージェントをインストールする]** を展開します。 <br><br>2. **[Download & install agent for Azure Windows Virtual machines >]\(Azure Windows 仮想マシン用のエージェントをダウンロードしてインストールする >\)** リンクを選択します。 <br><br>3. **[仮想マシン]** ブレードで、エージェントのインストール先となる仮想マシンを選んでから、 **[接続]** を選択します。 接続する各 VM に対してこの手順を繰り返します。 |
    | **その他の Windows マシンの場合** | 1. **[エージェントのインストール先を選択してください]** で、 **[Azure 以外の Windows マシンにエージェントをインストールする]** を展開します <br><br>2. **[Download & install agent for non-Azure Windows machines >]\(Azure 以外の Windows マシン用のエージェントをダウンロードしてインストールする >\)** リンクを選択します。  <br><br>3. **[エージェントの管理]** ブレードの **[Windows サーバー]** タブで、32 ビットまたは 64 ビット システム用の **[Windows エージェントのダウンロード]** リンクを適宜選択します。      |

1. DNS または Windows ファイアウォールのいずれかの **[ソリューションのインストール]** ボタンを選択します。

**DnsEvents**、**DnsInventory**、および **WindowsFirewall** のテーブル名をそれぞれ使用して、DNS と Windows ファイアウォールのデータを検索してクエリを実行できます。 これら 2 つのサービス コネクタに関するこれとその他の情報については、[データ コネクタのリファレンス](data-connectors-reference.md) ページのそれぞれのセクションを参照してください。


## <a name="next-steps"></a>次の手順

このドキュメントでは、Azure、Microsoft、および Windows サービスと、アマゾン ウェブ サービスを Azure Sentinel に接続する方法について学習しました。 
- 一般的な [Azure Sentinel データ コネクタ](connect-data-sources.md)について学習する。
- [Azure Sentinel データ コネクタを見つける](data-connectors-reference.md)。
- [データと潜在的な脅威を可視化](get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](detect-threats-built-in.md)の概要。