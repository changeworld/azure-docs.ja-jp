---
title: Microsoft Sentinel にセキュリティ アラートを接続する
description: Microsoft Defender for Cloud からのセキュリティ アラートを接続し、Microsoft Sentinel にストリーミングする方法について説明します。
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.topic: how-to
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: b30d3080b5c8e024f9266e6fbacc8a3c98e7457c
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132733825"
---
# <a name="connect-security-alerts-from-microsoft-defender-for-cloud"></a>Microsoft Defender for Cloud からのセキュリティ アラートを接続する

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

## <a name="background"></a>バックグラウンド

[Defender for Cloud](../security-center/security-center-introduction.md) の統合クラウド ワークロード保護プラットフォーム (CWPP) である [Microsoft Defender for Cloud](../security-center/azure-defender.md) は、ハイブリッド クラウド ワークロード全体の脅威を検出して迅速に対応できるセキュリティ管理ツールです。

このコネクタを使用すると、Defender for Cloud からのセキュリティ アラートを Azure Sentinel にストリーミングできるので、セキュリティ アラートとそれらによって生成されるインシデントをより広範な組織の脅威コンテキストで表示、分析、対応できます。

Defender for Cloud 自体がサブスクリプションごとに有効にされるため、Defender for Cloud コネクタもサブスクリプションごとに個別に有効または無効にすることができます。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

### <a name="alert-synchronization"></a>アラートの同期

- Microsoft Defender for Cloud を Microsoft Sentinel に接続すると、Microsoft Sentinel に取り込まれたセキュリティ アラートの状態が 2 つのサービス間で同期されます。 たとえば、 Defender for Cloud でアラートが閉じられた場合、そのアラートは Microsoft Sentinel でも閉じられたものとして表示されます。

- Defender for Cloud でアラートの状態を変更しても、Microsoft Sentinel アラートを含む Microsoft Sentinel **インシデント** の状態には影響 "せず"、アラート自体の状態にのみ影響します。

### <a name="bi-directional-alert-synchronization"></a>双方向アラート同期

- **双方向同期** を有効にすると、元のセキュリティ アラートの状態と、それらのアラートを含む Microsoft Sentinel インシデントの状態が自動的に同期されます。 そのため、たとえば、セキュリティ アラートを含む Microsoft Sentinel インシデントが閉じられると、Microsoft Defender for Cloud で対応する元のアラートが自動的に閉じられます。

## <a name="prerequisites"></a>前提条件

- Microsoft Sentinel ワークスペースに対する読み取りおよび書き込みアクセス許可が必要です。

- ストリーミングするログのサブスクリプションでのセキュリティ閲覧者ロールが必要です。

- コネクタを有効にするサブスクリプションごとに、Microsoft Defender for Cloud 内で少なくとも 1 つのプランを有効にする必要があります。 サブスクリプションで Microsoft Defender プランを有効にするには、そのサブスクリプションの **セキュリティ管理者** ロールを持っている必要があります。

- 双方向同期を有効にするには、その関連するサブスクリプションで **共同作成者** か **セキュリティ管理者** のロールを持っている必要があります。

## <a name="connect-to-microsoft-defender-for-cloud"></a>Microsoft Defender for Cloud に接続する

1. Microsoft Sentinel で、ナビゲーション メニューから **[データ コネクタ]** を選択します。

1. データ コネクタ ギャラリーで、**[Microsoft Defender for Cloud]** を選択し、詳細ペインで **[コネクタ ページを開く]** を選択します。

1. **[構成]** に、テナント内のサブスクリプションの一覧と、サブスクリプションと Microsoft Defender for Cloud との接続の状態が表示されます。 アラートを Microsoft Sentinel にストリーミングする各サブスクリプションの横にある **[状態]** トグルを選択します。 一度に複数のサブスクリプションを接続する場合は、関連するサブスクリプションの横にあるチェック ボックスをオンにし、一覧の上にあるバーの **[接続]** ボタンを選択します。

    > [!NOTE]
    > - チェック ボックスと **[接続]** トグルは、必要なアクセス許可を持っているサブスクリプションのものだけがアクティブになります。
    > - **[接続]** ボタンは、少なくとも 1 つのサブスクリプションのチェック ボックスがマークされている場合にのみアクティブになります。

1. サブスクリプションで双方向同期を有効にするには、一覧でそのサブスクリプションを見つけて、**[双方向同期]** 列のドロップダウン リストから **[有効]** を選択します。 一度に複数のサブスクリプションで双方向同期を有効にするには、サブスクリプションのチェック ボックスをオンにし、一覧の上のバーにある **[双方向同期を有効にする]** ボタンを選択します。

    > [!NOTE]
    > - チェック ボックスとドロップダウン リストは、[必要なアクセス許可](#prerequisites)を持っているサブスクリプションのものだけがアクティブになります。
    > - **[双方向同期を有効にする]** ボタンは、少なくとも 1 つのサブスクリプションのチェック ボックスがマークされている場合にのみアクティブになります。

1. 一覧の **[Microsoft Defender プラン]** 列で、Microsoft Defender プランがサブスクリプションで有効になっているかどうか (コネクタを有効にするための前提条件) を確認できます。 この列の各サブスクリプションの値は、空白である (Defender プランが有効になっていない) か、[すべて有効]、または [一部が有効] になります。 [一部が有効] と表示されている場合は、**[すべて有効にする]** リンクも表示されます。これを選択すると、そのサブスクリプションの Microsoft Defender for Cloud 構成ダッシュボードに移動します。そこで、有効にする Defender プランを選択することができます。 一覧の上のバーにある **[すべてのサブスクリプションに対して Microsoft Defender を有効にする]** リンク ボタンをクリックすると、Microsoft Defender for Cloud の [作業の開始] ページに移動します。そこで、Microsoft Defender for Cloud を有効にするサブスクリプションをすべて選択することができます。

    :::image type="content" source="./media/connect-defender-for-cloud/azure-defender-config.png" alt-text="Microsoft Defender for Cloud コネクタの構成のスクリーンショット":::

1. Microsoft Defender for Cloud からのアラートによって Microsoft Sentinel でインシデントが自動的に生成されるようにするかどうかを選択できます。 **[インシデントの作成]** で、 **[有効化]** を選択して、[アラートからインシデントを自動的に作成する](create-incidents-from-alerts.md)既定の分析ルールを有効にします。 次に、 **[Active rules]\(アクティブなルール\)** タブの **[分析]** でこのルールを編集します。

    > [!TIP]
    > アラートの[カスタム分析ルール](detect-threats-custom.md)を Microsoft Defender for Cloud から構成する場合は、アラートの重要度を考慮して、情報アラートに対してインシデントを開かないようにします。 
    >
    > Microsoft Defender for Cloud の情報アラート自体は、セキュリティ リスクを表すものではありません。情報アラートは、既存のオープン インシデントのコンテキストのみに関連します。 詳細については、[Microsoft Defender for Cloud のセキュリティ アラートとインシデント](../security-center/security-center-alerts-overview.md)に関するページを参照してください。
    > 
    

## <a name="find-and-analyze-your-data"></a>データを検索して分析する

> [!NOTE]
> *双方向* のアラート同期には、数分かかる場合があります。 アラートの状態の変更は、すぐには表示されない場合があります。

- セキュリティ アラートは、Log Analytics ワークスペースの *SecurityAlert* テーブルに格納されます。

- Log Analytics でセキュリティ アラートに対してクエリを実行するには、最初に、次の情報をクエリ ウィンドウにコピーします。

    ```kusto
    SecurityAlert 
    | where ProductName == "Azure Security Center"
    ```

- その他の便利なサンプル クエリ、分析ルール テンプレート、推奨されるブックについては、コネクタ ページの **[次のステップ]** タブを参照してください。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Microsoft Defender for Cloud を Microsoft Sentinel に接続し、それらの間でアラートを同期する方法について学習しました。 Microsoft Sentinel の詳細については、次の記事を参照してください。

- [データと潜在的な脅威を可視化](get-visibility.md)する方法についての説明。
- [Microsoft Sentinel を使用した脅威の検出](detect-threats-built-in.md)の概要。
- [脅威を検出](detect-threats-custom.md)するための独自の規則を作成する。
