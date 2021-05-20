---
title: UEBA データを使用してインシデントを調査 | Microsoft Docs
description: 調査する際に UEBA データを使用し、組織内で発生した可能性のある悪意のあるアクティビティに対して、より大きなコンテキストを取得する方法について説明します。
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2021
ms.author: bagol
ms.openlocfilehash: 1a3f199276d5c9b04ab5ac117c022a63fd3fb866
ms.sourcegitcommit: 19dfdfa85e92c6a34933bdd54a7c94e8b00eacfd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2021
ms.locfileid: "109665302"
---
# <a name="investigate-incidents-with-ueba-data"></a>UEBA データを使用したインシデントの調査

この記事では、通常の調査ワークフローで[ユーザー エンティティ行動分析 (UEBA)](identify-threats-with-entity-behavior-analytics.md) を使用する一般的な方法とサンプル手順について説明します。

## <a name="prerequisites"></a>前提条件

調査時に UEBA データを使用する前に、[Azure Sentinel で [ユーザー エンティティ行動分析 (UEBA)] を有効にする](enable-entity-behavior-analytics.md)必要があります。 

UEBA を有効にしてから約 1 週間待ってから、マシン パワーを利用した分析情報の検索を開始してください。

## <a name="run-proactive-routine-searches-in-entity-data"></a>エンティティ データでプロアクティブなルーチン検索を実行する

詳細な調査を行う手がかりを得るため、ユーザー アクティビティについて調べる定期的なプロアクティブ検索を実行することをお勧めします。

Azure Sentinel の[ユーザーおよびエンティティ行動分析ブック](identify-threats-with-entity-behavior-analytics.md#hunting-queries-and-exploration-queries)を使用し、次のようなデータに対してクエリを実行できます。

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

擬陽性の一般的な例として、あるユーザーが同じ時間帯にニューヨークとロンドンの両方からアプリケーションまたはポータルにサインインしたなど、不可能な移動アクティビティが検出された場合があります。 Azure Sentinel では不可能な移動を異常として警告されたものの、そのユーザーについて調査すると、ユーザーが実際にいた場所とは別の場所で VPN が使用されていたことが判明することがあります。

### <a name="analyze-a-false-positive"></a>擬陽性を分析する

たとえば、**不可能な移動** インシデントの場合、そのユーザーについて VPN が使用されたことを確認した後、インシデントからユーザー エンティティのページに移動します。 そこに表示されるデータを使用して、キャプチャされた場所が、ユーザーが普段使用している場所として認識されている場所に含まれているかどうかを判断します。

次に例を示します。

[ ![インシデントのユーザー エンティティ ページを開きます。](media/ueba/open-entity-pages.png) ](media/ueba/open-entity-pages.png#lightbox)

ユーザー エンティティ ページは、[[インシデント] ページ](tutorial-investigate-cases.md#how-to-investigate-incidents)自体と[調査グラフ](tutorial-investigate-cases.md#use-the-investigation-graph-to-deep-dive)からもリンクされています。

> [!TIP]
> インシデントに関連付けられている特定のユーザーのユーザー エンティティ ページでデータを確認した後、Azure Sentinel の **[ハンティング]** 領域にアクセスして、ユーザーのピアがいつもは同じ場所から接続しているかどうかも把握できます。 そうするならば、これらの情報によって、擬陽性について、より明確な根拠に基づいて判断を下せます。
>
> **[ハンティング]** 領域で、**異常な地理的な場所でのログオン** に関するクエリを実行します。 詳細については、「[Azure Sentinel で脅威を検出する](hunting.md)」を参照してください。
>

## <a name="identify-password-spray-and-spear-phishing-attempts"></a>パスワード スプレーとスピア フィッシングの試行を識別する

多要素認証 (MFA) が有効になっていない場合、ユーザーの資格情報は、[パスワード スプレー](https://www.microsoft.com/security/blog/2020/04/23/protecting-organization-password-spray-attacks/)または[スピア フィッシング](https://www.microsoft.com/security/blog/2019/12/02/spear-phishing-campaigns-sharper-than-you-think/)の試行によって攻撃しようとしている攻撃者に対して脆弱になります。

### <a name="investigate-a-password-spray-incident-with-ueba-insights"></a>UEBA 分析情報でパスワード スプレー インシデントを調査する

たとえば、UEBA 分析情報でパスワード スプレー インシデントを調査するには、次の手順を実行して詳細を確認できます。

1. インシデントの左下にある **[調査]** を選択して、攻撃の対象となる可能性があるアカウント、マシン、およびその他のデータ ポイントを表示します。

    データを参照すると、ログオン エラーの数が比較的多い管理者アカウントが表示される場合があります。 これは疑わしいですが、詳細を確認せずにそのアカウントを制限したくないと思うかもしれません。

1. マップで管理ユーザー エンティティを選択し、右側にある **[分析情報]** を選択して、時間のかかるサインインのグラフなどの詳細を確認します。

1. 右側の **[情報]** を選択し、 **[詳細の表示]** を選択して[ユーザー エンティティ ページ](identify-threats-with-entity-behavior-analytics.md#entity-pages)に移動し、さらに詳しく調べます。 

    たとえば、これがそのユーザーの初めての潜在的パスワード スプレー インシデントであるかどうか、またユーザーのサインイン履歴を監視してエラーが異常だったかどうかを確認します。

> [!TIP]
> **異常な失敗ログオン** の[ハンティング クエリ](hunting.md)を実行して、組織の異常な失敗ログインすべてを監視することもできます。 クエリの結果を使用して、パスワード スプレー攻撃の可能性について調査を開始します。
>

## <a name="next-steps"></a>次のステップ

UEBA、調査、ハンティングの詳細については、以下をご覧ください。

- [Azure Sentinel のユーザーとエンティティの行動分析 (UEBA) を使用して高度な脅威を特定する](identify-threats-with-entity-behavior-analytics.md)
- [Azure Sentinel UEBA エンリッチメント リファレンス](ueba-enrichments.md)
- [チュートリアル:Azure Sentinel でインシデントを調査する](tutorial-investigate-cases.md)
- [Azure Sentinel で脅威を検出する](hunting.md)
