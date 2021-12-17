---
title: UEBA データを使用してインシデントを調査 | Microsoft Docs
description: 調査する際に UEBA データを使用し、組織内で発生した可能性のある悪意のあるアクティビティに対して、より大きなコンテキストを取得する方法について説明します。
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5cf711abf0373c2c7681d71426c0590b9561708e
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132522880"
---
# <a name="tutorial-investigate-incidents-with-ueba-data"></a>チュートリアル: UEBA データを使用したインシデントの調査

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

この記事では、通常の調査ワークフローで[ユーザー エンティティ行動分析 (UEBA)](identify-threats-with-entity-behavior-analytics.md) を使用する一般的な方法とサンプル手順について説明します。

> [!IMPORTANT]
>
> この記事に記載されている機能は、現在 **プレビュー** 段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。
>

> [!NOTE]
> このチュートリアルでは、上位の顧客タスク (UEBA データを使用した調査) のシナリオベースの手順について説明します。 詳細については、「[Microsoft Azure Sentinel でインシデントを調査する](investigate-cases.md)」を参照してください。
>
## <a name="prerequisites"></a>前提条件

調査時に UEBA データを使用する前に、[Microsoft Azure Sentinel で [ユーザー/エンティティ行動分析 (UEBA)] を有効にする](enable-entity-behavior-analytics.md)必要があります。

UEBA を有効にしてから約 1 週間待ってから、マシン パワーを利用した分析情報の検索を開始してください。

## <a name="run-proactive-routine-searches-in-entity-data"></a>エンティティ データでプロアクティブなルーチン検索を実行する

詳細な調査を行う手がかりを得るため、ユーザー アクティビティについて調べる定期的なプロアクティブ検索を実行することをお勧めします。

Microsoft Azure Sentinel の[ユーザー/エンティティ行動分析ブック](identify-threats-with-entity-behavior-analytics.md#hunting-queries-and-exploration-queries)を使用し、次のようなデータに対してクエリを実行できます。

- **危険度の高い上位のユーザー** (異常または付随するインシデントに関係)
- **特定のユーザーに関するデータ**。対象が実際に侵害されたかどうか、またはユーザーのプロファイルから逸脱したアクションによって内部関係者による脅威が発生したかどうかを判断します。

さらに、UEBA ブックで非定型的なアクションをキャプチャし、それらを使用して異常なアクティビティやコンプライアンスに準拠していない可能性のある活動を見つけます。

### <a name="investigate-an-anomalous-sign-in"></a>異常なサインインを調査する

たとえば、次の手順では、それまでに使用したことがない VPN に接続したユーザー (異常なアクティビティ) の調査を行います。

1. Sentinel の **[ブック]** 領域で、**ユーザーおよびエンティティ行動分析** ブックを検索して開きます。
1. 調査する特定のユーザー名を検索し、 **[調査する上位ユーザー]** テーブルでそれらのユーザーの名前を選択します。
1. **[インシデントの詳細]** と **[異常の詳細]** テーブルを下にスクロールして、選択したユーザーに関連付けられているインシデントと異常を表示します。
1. 表示した異常 ( **[異常なログイン成功]** のような名前) で、表に示されている調査すべき詳細を確認します。 次に例を示します。

    |手順  |説明  |
    |---------|---------|
    |**右側の説明を確認する**     |    各異常には説明と、詳細を参照できる [MITRE ATT&CK ナレッジ ベース](https://attack.mitre.org/)へのリンクがあります。 <br>次に例を示します。 <br><br>        **_最初のアクセス_* _ <br>_敵対者がネットワークに侵入しようとしています。*<br>* 最初のアクセスでは、ネットワーク内で最初の足掛かりを得るためのさまざまな入力ベクトルが利用されます。 足掛かりを得るために使用される手法には、標的型スピア フィッシングや、公開されている Web サーバー上の脆弱性の悪用などがあります。 最初のアクセスで得た足掛かりをもとに、有効なアカウントや外部リモート サービスを利用して継続的にアクセスできるようにしたり、パスワードを変更することによって制限付きで使用できるようにしたりします。*     |
    |**[説明] 列のテキストを確認する**     |   [異常] 行で、右にスクロールして追加の説明を表示します。 リンクを選択して、すべてのテキストを表示します。 次に例を示します。 <br><br> *敵対者は、資格情報にアクセスする手法を用いて、特定のユーザーまたはサービス アカウントの資格情報を盗むことがあります。また、最初のアクセスの手段としてソーシャル エンジニアリングを通じて事前に仕込ませておいた偵察プロセスで資格情報を取得することもあります。たとえば、APT33 では、最初のアクセスに有効なアカウントが使用されてきました。次のクエリで、ユーザーがそれまでに接続したことがない新しい地理的な場所から行ったサインインの成功に関する出力が生成されます。*     |
    |**UsersInsights データを確認する**     |  [異常] 行をさらに右にスクロールすると、アカウントの表示名やアカウント オブジェクト ID などのユーザーに関する分析情報データが表示されます。 テキストを選択すると、右側に完全なデータが表示されます。         |
    |**証拠データを確認する**     |  [異常] 行をさらに右にスクロールすると、異常に関する証拠データが表示されます。 テキストを選択すると、以下のフィールドのように、右側の完全なデータが表示されます。 <br><br>-  **ActionUncommonlyPerformedByUser** <br>- **UncommonHighVolumeOfActions** <br>- **FirstTimeUserConnectedFromCountry** <br>- **CountryUncommonlyConnectedFromAmongPeers** <br>- **FirstTimeUserConnectedViaISP** <br>- **ISPUncommonlyUsedAmongPeers** <br>- **CountryUncommonlyConnectedFromInTenant** <br>- **ISPUncommonlyUsedInTenant** |
    |     |         |

**ユーザーおよびエンティティ動作分析** ブックにあるデータを使用して、ユーザー アクティビティが疑わしいものかどうか、さらにアクションが必要かどうかを判断します。

## <a name="use-ueba-data-to-analyze-false-positives"></a>UEBA データを使用して擬陽性を分析する

調査でキャプチャされたインシデントが擬陽性であることがあります。

擬陽性の一般的な例として、あるユーザーが同じ時間帯にニューヨークとロンドンの両方からアプリケーションまたはポータルにサインインしたなど、不可能な移動アクティビティが検出された場合があります。 Microsoft Azure Sentinel では不可能な移動を異常として警告されたものの、そのユーザーについて調査すると、ユーザーが実際にいた場所とは別の場所で VPN が使用されていたことが判明することがあります。

### <a name="analyze-a-false-positive"></a>擬陽性を分析する

たとえば、**不可能な移動** インシデントの場合、そのユーザーについて VPN が使用されたことを確認した後、インシデントからユーザー エンティティのページに移動します。 そこに表示されるデータを使用して、キャプチャされた場所が、ユーザーが普段使用している場所として認識されている場所に含まれているかどうかを判断します。

次に例を示します。

[ ![インシデントのユーザー エンティティ ページを開きます。](media/ueba/open-entity-pages.png) ](media/ueba/open-entity-pages.png#lightbox)

ユーザー エンティティ ページは、[[インシデント] ページ](investigate-cases.md#how-to-investigate-incidents)自体と[調査グラフ](investigate-cases.md#use-the-investigation-graph-to-deep-dive)からもリンクされています。

> [!TIP]
> インシデントに関連付けられている特定のユーザーのユーザー エンティティ ページでデータを確認した後、Microsoft Azure Sentinel の **[ハンティング]** 領域にアクセスして、ユーザーのピアがいつもは同じ場所から接続しているかどうかも把握できます。 そうするならば、これらの情報によって、擬陽性について、より明確な根拠に基づいて判断を下せます。
>
> **[ハンティング]** 領域で、**異常な地理的な場所でのログオン** に関するクエリを実行します。 詳細については、「[Microsoft Azure Sentinel で脅威を検出する](hunting.md)」を参照してください。
>

### <a name="embed-identityinfo-data-in-your-analytics-rules-public-preview"></a>分析ルールに IdentityInfo データを埋め込む (パブリック プレビュー)

攻撃者は多くの場合、組織独自のユーザーとサービス アカウントを使用するため、アナリストにとっては、ユーザー ID や特権など、それらのユーザー アカウントに関するデータが調査中に重要です。

**IdentityInfo テーブル** のデータを埋め込み、ユース ケースに合わせて分析ルールを微調整し、擬陽性を減らし、場合によっては調査プロセスを高速化します。

次に例を示します。

- **IT** 部門外の誰かがサーバーにアクセスした場合にトリガーされるアラートで、セキュリティ イベントを **IdentityInfo** テーブルに関連付けるには:

    ```kusto
    SecurityEvent
    | where EventID in ("4624","4672")
    | where Computer == "My.High.Value.Asset"
    | join kind=inner  (
        IdentityInfo
        | summarize arg_max(TimeGenerated, *) by AccountObjectId) on $left.SubjectUserSid == $right.AccountSID
    | where Department != "IT"
    ```

- 特定のセキュリティ グループのメンバーではないユーザーがアプリケーションにアクセスした場合にトリガーされるアラートで、Azure AD サインイン ログを **IdentityInfo** テーブルに関連付けるには:

    ```kusto
    SigninLogs
    | where AppDisplayName == "GithHub.Com"
    | join kind=inner  (
        IdentityInfo
        | summarize arg_max(TimeGenerated, *) by AccountObjectId) on $left.UserId == $right.AccountObjectId
    | where GroupMembership !contains "Developers"
    ```

**IdentityInfo** テーブルでは、Azure AD ワークスペースと同期して、ユーザー メタデータ、グループ情報、各ユーザーに割り当てられた Azure AD ロールなど、ユーザー プロファイル データのスナップショットを作成します。 詳細については、『UEBA エンリッチメント リファレンス』で「[IdentityInfo テーブル](ueba-enrichments.md#identityinfo-table-public-preview)」を参照してください。

## <a name="identify-password-spray-and-spear-phishing-attempts"></a>パスワード スプレーとスピア フィッシングの試行を識別する

多要素認証 (MFA) が有効になっていない場合、ユーザーの資格情報は、[パスワード スプレー](https://www.microsoft.com/security/blog/2020/04/23/protecting-organization-password-spray-attacks/)または[スピア フィッシング](https://www.microsoft.com/security/blog/2019/12/02/spear-phishing-campaigns-sharper-than-you-think/)の試行によって攻撃しようとしている攻撃者に対して脆弱になります。

### <a name="investigate-a-password-spray-incident-with-ueba-insights"></a>UEBA 分析情報でパスワード スプレー インシデントを調査する

たとえば、UEBA 分析情報でパスワード スプレー インシデントを調査するには、次の手順を実行して詳細を確認できます。

1. インシデントの左下にある **[調査]** を選択して、攻撃の対象となる可能性があるアカウント、マシン、およびその他のデータ ポイントを表示します。

    データを参照すると、ログオン エラーの数が比較的多い管理者アカウントが表示される場合があります。 これは疑わしいですが、詳細を確認せずにそのアカウントを制限したくないと思うかもしれません。

1. マップで管理ユーザー エンティティを選択し、右側にある **[分析情報]** を選択して、時間のかかるサインインのグラフなどの詳細を確認します。

1. 右側の **[情報]** を選択し、 **[詳細の表示]** を選択して [ユーザー エンティティ ページ](identify-threats-with-entity-behavior-analytics.md#entity-pages)に移動し、さらに詳しく調べます。 

    たとえば、これがそのユーザーの初めての潜在的パスワード スプレー インシデントであるかどうか、またユーザーのサインイン履歴を監視してエラーが異常だったかどうかを確認します。

> [!TIP]
> **異常な失敗ログオン** の [ハンティング クエリ](hunting.md)を実行して、組織の異常な失敗ログインすべてを監視することもできます。 クエリの結果を使用して、パスワード スプレー攻撃の可能性について調査を開始します。
>

## <a name="url-detonation-public-preview"></a>zURL デトネーション (パブリック プレビュー)

Microsoft Azure Sentinel に取り込まれたログに URL がある場合、これらの URL はトリアージ プロセスを加速するために自動的にデトネーションされます。 

調査グラフには、デトネーションされた URL のノードと、次の詳細が含まれています。

- **DetonationVerdict**。 デトレーションからの高レベルのブール値の決定。 たとえば、**Bad** は、サイドがマルウェアまたはフィッシング コンテンツのホストとして分類されたとします。
- **DetonationFinalURL**。 元の URL からすべてのリダイレクトが行われた後の、観測された最後のランディング ページ URL。
- **DetonationScreenshot**。 アラートがトリガーされた時点でのページの外観のスクリーンショット。 拡大するスクリーンショットを選択します。

次に例を示します。

:::image type="content" source="media/investigate-with-ueba/url-detonation-example.png" alt-text="調査グラフに示されているサンプル URL デトレーション。":::

> [!TIP]
> ログに URL が表示されていない場合は、セキュリティで保護された Web ゲートウェイ、Web プロキシ、ファイアウォール、またはレガシ IDS/IPS に対して URL ログ (脅威ログとも呼ばれる) が有効になっていることを確認します。
>
> さらに調査するために、カスタム ログを作成して、関心のある特定の URL を Microsoft Azure Sentinel にチャネル化することもできます。
>

## <a name="next-steps"></a>次のステップ

UEBA、調査、ハンティングの詳細については、以下をご覧ください。

- [Microsoft Azure Sentinel のユーザー/エンティティ行動分析 (UEBA) を使用して高度な脅威を特定する](identify-threats-with-entity-behavior-analytics.md)
- [Microsoft Azure Sentinel UEBA エンリッチメント リファレンス](ueba-enrichments.md)
- [チュートリアル: Microsoft Azure Sentinel でインシデントを調査する](investigate-cases.md)
- [Microsoft Sentinel を使用して脅威を追求する](hunting.md)
