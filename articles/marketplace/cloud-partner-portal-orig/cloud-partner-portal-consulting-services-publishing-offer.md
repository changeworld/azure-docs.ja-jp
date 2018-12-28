---
title: Azure と Dynamcis 365 のコンサルティング サービス オファー |Microsoft Docs
description: Cloud パートナー ポータルで Azure または Dynamcis 365 コンサルティング サービス オファーを定義および公開するためのガイド。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: pbutlerm
ms.openlocfilehash: 7ea4a58f072071f60cf87d1e8817ae19658be5fb
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53310421"
---
# <a name="azure-and-dynamics-365-consulting-service-offer"></a>Azure と Dynamics 365 のコンサルティング サービス オファー

<table> <tr> <td>このセクションでは、<a href="https://azuremarketplace.microsoft.com">Azure Marketplace</a> または <a href="https://appsource.microsoft.com">AppSource Marketplace</a> のいずれかにコンサルティング サービスを公開する方法について説明します。 Microsoft <a href="https://dynamics.microsoft.com">Dynamics 365</a>、<a href="https://products.office.com">Office 365</a>、<a href="https://powerbi.microsoft.com">Power BI</a>、<a href="https://powerapps.microsoft.com">PowerApps</a> に基づくソリューションは、AppSource に掲載される資格があります。 その他の Microsoft <a href="https://azure.microsoft.com/services">Azure サービス</a>に基づくその他のオファーは、Azure Marketplace に掲載される資格があります。 </td> <td><img src="./media/consulting-services-publishing-offer/consulting-icon1.png"  alt="Microsoft consulting icon" /></td> </tr> </table>


## <a name="publishing-benefits"></a>公開の利点

Microsoft のいずれかのマーケットプレースに公開することには、大きな利点があります。

- Microsoft のブランドを活用することで、会社の宣伝ができる。
- AppSource で 1 億を超える Office 365 と Dynamics 365 のユーザー、Azure Marketplace を通じて 20 万を超える組織にリーチできる可能性がある。
- これらのマーケットプ レースから質の高い潜在顧客を獲得できる。
- Microsoft のフィールド チームとテレセールス チームによりサービスを宣伝してもらえる。


## <a name="define-your-consulting-services-offer"></a>コンサルティング サービス オファーの定義

パッケージ化されたコンサルティング サービス オファリングを定義します。 単一のお客様を対象とする、スコープや期間が定まった、推定価格または固定価格 (または無料) の、主にプリセールス向けのオファリングに重点を置きます。 新しいビジネスを推進する上で評判が良く、効果的だった、反復可能なパッケージ エンゲージメントを選択します。

## <a name="publish-a-consulting-service-offer"></a>コンサルティング サービス オファーの発行

次のセクションでは、コンサルティング サービス オファーを発行するプロセスについて説明します

1.  新しいオファーを作成する
2.  オファー設定を定義する
3.  ネットショップの詳細を入力し、Azure Marketplace と AppSource のどちらで発行するかを入力します。
4.  オファーを発行する

### <a name="create-a-new-offer"></a>新しいオファーを作成する

新しいオファーを作成するには、次の手順を実行します。

1.  Cloud パートナー ポータルのメイン メニューで、**[新しいプラン]** を選択します。
2.  [新しいプラン] メニューで、**[Consulting Service (コンサルティング サービス)]** を選択します。

    ![新しいコンサルティング サービス オファーの作成](media/consulting-services-publishing-offer/cppselectnewconsultingoffer.png)

### <a name="define-offer-settings"></a>オファー設定を定義する

[新しいプラン] 画面では、まずオファー ID を作成します。  オファー ID は、次の 3 つのパーツで構成されます。**プラン ID**、**パブリッシャー ID**、および**名前**。  次の各のセクションで、これらのパーツについて説明します。

#### <a name="offer-id"></a>プラン ID

この ID は、最初にオファーを送信するときに作成する一意の名前です。 小文字の英数字、ハイフン、アンダースコアのみで構成する必要があります。 オファー ID は URL に表示され、検索エンジンの結果に影響します。 たとえば、*yourcompanyname\_exampleservice* などです

例に示すように、オファー ID がパブリッシャー ID に追加されて、一意識別子が作成されます。 これは予約可能な固定リンクとして公開され、検索エンジンによってインデックス付けされます。 

*オファーが運用開始になると、その識別子を更新することはできません*

#### <a name="publisher-id"></a>パブリッシャー ID

この識別子は、ご自分のアカウントに関連しています。 組織アカウントでログインすると、ドロップダウン メニューにパブリッシャー ID が表示されます。

#### <a name="name"></a>Name

この文字列が、AppSource または Azure Marketplace 上で、オファー名として表示されます。

**重要:** ここでは実際のサービスの名前のみを入力してください。 サービスの期間と種類は含めないでください。

Edgewater Fullscope による次の例は、オファー名がどのように組み立てられるかを示しています。 オファー名は次のように表示されます。

![新しいコンサルティング サービス オファーの作成](media/consulting-services-publishing-offer/cppsampleconsultingoffer.png)

オファー名は、4 つのパーツで構成されます。

-   **期間**: エディターの **[ネットショップの詳細]** タブで定義します。 期間は、時間、日、週で表現できます。
-   **サービスの種類**: エディターの **[ネットショップの詳細]** タブで定義します。 サービスの種類は、`Assessment`、`Briefing`、`Implementation`、`Proof of concept`、`Workshop` です。
-   **前置詞**: レビュー担当者によって挿入されます。
-   **名前**: **[プランの設定]** ページで定義します。

以下の一覧で適切なオファー名を示します。

-   プロフェッショナル サービスの要点: 1 時間のブリーフィング
-   クラウド移行プラットフォーム: 1 時間のブリーフィング
-   PowerApps および Microsoft Flow: 1 日のワークショップ
-   Azure Machine Learning サービス: 3 週間の PoC
-   ブリック アンド クリック小売りソリューション: 1 時間のブリーフィング
-   データ持ち込み: 1 週間のワークショップ
-   クラウド分析:3 日間のワークショップ
-   Power BI トレーニング: 3 日間のワークショップ
-   セールス管理ソリューション: 1 週間の実装
-   CRM クイック スタート: 1 日のワークショップ
-   Dynamics 365 for Sales: 2 日間の評価

**[プラン設定]** タブの入力が完了したら、送信内容を保存できます。 オファー名はエディターの上に表示されるようになり、[All Offers (すべてのオファー)] に戻るとそれが表示されます。

### <a name="enter-storefront-details"></a>ネットショップの詳細を入力する

次に、ネットショップの詳細を入力する必要があります。 [ネットショップの詳細] は、次のセクションで構成されます。

-   オファーの詳細
-   パブリッシャー情報
-   一覧の詳細
-   マーケティングの成果物

#### <a name="offer-details"></a>オファーの詳細

オファーの詳細セクションには、次のフィールドが含まれています。

-   Offer summary (オファーの概要)
-   Offer description (オファーの説明)

##### <a name="offer-summary"></a>オファーの概要

オファーの概要は、オファー名のすぐ下に表示されるオファーの簡単な説明です。 オファーの概要を入力するときは、プレーン テキストを使用します。また、改行は入れないでください。 以下に、オファーの概要と、それに対応するオファー名の良い例を挙げます。

*例 1*

-   **プラン名:** クラウド分析:3 日間のワークショップ
-   **オファーの概要:** Microsoft Azure と Power BI の概要、現在の環境の評価、およびミニ POC。

*例 2*

-   **プラン名:** 産業用 Azure IoT:30 日間の概念実証
-   **オファーの概要:** 現場の機器をダッシュボード、レポート機能、通知機能を備えた Azure IoT Hub ソリューションに安全に接続する、産業用の接続型製品のパイロット版を作成します。

*例 3*

-   **プラン名:** プロフェッショナル サービス:1 時間のブリーフィング
-   **オファーの概要:** 専門サービスのプロジェクト、課金、およびリソースの管理強化を実現する、事前に構成された拡張型の Dynamics 365 for Operations ソリューションの概要とデモ。

*例 4*

-   **プラン名:** 特定業界向け Power BI: 4 時間のワークショップ
-   **オファーの概要:** 最初のダッシュボードを稼働させて、ベスト プラクティスについて学習します。 受講者は最大 12 名、オンサイトで実施。

*例 5*

-   **プラン名:** Dynamics とプロジェクト: 3 日間の評価
-   **オファーの概要:** 専門サービス企業やプロジェクト指向の企業向けに設計された、ERP ソリューションの要件の収集と評価。

##### <a name="offer-description"></a>オファーの説明

コンサルティング サービス オファーの説明です。 優れたオファーの説明には、エンゲージメントの内容や、お客様が手にする最終成果物についての正確な詳細情報が含まれます。 お客様が得られる成果を理解する上で助けになるに違いありません。

オファーの説明には、連絡用の電子メールのリンクや電話番号を含めないでください。 オファーには [Contact Me (お問い合わせ)] ボタンが表示され、これによってリードはオファーで特定されているリード管理ターゲットにアップロードされます。

オファーの説明は Markdown 形式で入力します。 Markdown や HTML の書式設定に慣れていない場合は、[Microsoft ドキュメント サイト](https://docs.microsoft.com/contribute/how-to-write-use-markdown)でリソースを確認できます。

これらの形式を使用すると、オファーをお客様にとって最大限読みやすいものにできます。

ユーザーは長いテキストを読むことを好まないので、オファーの説明を簡潔にし、文字数制限を守ってください。 マーケティング パンフレット、ファクト シート、オファーを詳細に説明するその他のドキュメントをアップロードすることもできます。

次の例では、適切に構成されたオファーの説明と、それに関連する名前および概要を示しています。

**プラン名:** クラウド分析:3 日間のワークショップ

**オファーの概要:** Microsoft Azure と Power BI の概要、現在の環境の評価、およびミニ POC。

**オファーの説明:** この 3 日間のワークショップは技術リーダーおよびビジネス リーダー向けであり、お客様の施設でオンサイトで開催されます。

***予定***

Day 1

-   Azure Data Lake、HDInsight、Azure SQL Data Warehouse を使用して、Microsoft クラウド内のデータを保護、拡張、整理する方法に焦点を当てます。

Day 2

-   Microsoft R と Azure Machine Learning を使用して高度な分析ソリューションを構成およびデプロイする方法について説明します。

Day 3

-   Power BI を使用して実用的な分析情報を抽出し、分析を操作する方法を説明します。Power BI ダッシュボードを共同構築するための共同作業のセッションを含みます。

ワークショップが終わるまでに、お客様は、Microsoft クラウドにおけるデータと分析のソリューションの大まかな計画と実装のロードマップを定義できるようになります。

*この形式に従ったオファーのサンプル Markdown ファイル:*

    This 3-day workshop is for technical and business leaders and is held on-site at the client’s facility.

      ### Agenda

      **Day 1**

      * Focuses on how to secure, scale, and organize data within the Microsoft cloud using Azure Data Lake, HDInsight, or Azure SQL Data Warehouse

      **Day 2**

      * Covers how to configure and deploy advanced analytics solutions with Microsoft R and Azure Machine Learning

      **Day 3**

      * Covers how to draw actionable insights and operationalize analytics with Power BI, including a collaborative session to co-build a Power BI dashboard.


      ### Deliverables
      By the end of the workshop, the client will be able to define a high-level plan and an implementation roadmap for data and analytics solutions in the Microsoft cloud.


#### <a name="publisher-information"></a>**パブリッシャー情報**

**MPN ID**

9 桁の Microsoft Partner Network (MPN) ID。 MPN ID がない場合は、Microsoft パートナー センターにアクセスして取得できます。

**パートナー センター ID**

新しいパートナー センター ID (ある場合)。

**MPN ID**

運用を開始する前に AppSource でオファーのプレビューを実行するための秘密鍵を入力します。
この識別子はパスワードではありません。

#### <a name="listing-details"></a>一覧の詳細

**コンサルティング サービスの種類**

Microsoft は、単一のお客様を対象とする、スコープや期間が定まった、推定価格または固定価格 (または無料) の、主にプリセールス向けのコンサルティング サービス オファリングに専ら重点を置いています。その内容は、評価、実装、概念実証、ブリーフィング、ワークショップのオファーであり、オンサイトで実施することも仮想的に実施することもできます (ブリーフィングはオンサイトで実施する必要があります)。

次の 5 種類のオファリングが含まれます。

-   **評価:** ソリューションの適用性を確認し、コストとタイミングの見積もりを提供するためのお客様の環境の評価。
-   **ブリーフィング:** フレームワーク、デモ、および顧客事例を使用してお客様の関心を引くためのソリューションまたはコンサルティング サービスの概要。 ブリーフィングは、オンサイトで実施する必要があります。
-   **実装:** 完全に機能するソリューションを生成する完全なインストール。 このパイロットの場合、1 週間以内に実装できるソリューションに限定することをお勧めします。
-   **概念実証:** ソリューションがお客様の要件を満たすかどうかを判定するための限られた範囲の実装。
-   **ワークショップ:** お客様の施設で実施される対話形式のセッションであり、トレーニング セッション、ブリーフィング、評価、またはお客様のデータまたは環境に基づいて構築されたデモが含まれることがあります。

**国/リージョンの可用性**

このコンサルティング サービス オファーを利用可能にする国とリージョンを選択します。 1 つのオファーを複数の国やリージョンで発行することはできません。 国またはリージョンごとに新しいオファーを作成する必要があります。

**業界**

コンサルティング サービス オファーに最も適した業界を選択します。

**Duration**

[期間] の下の数字 (たとえば、3、4 など) を選択し、次いで [時間]、[曜日]、[週] を選択します。

**主要製品**

Azure Marketplace に発行するには、主要製品として **Azure** を選択し、関連するソリューション分野を選択します。

AppSource に発行するには、主要製品として **Dynamics 365**、**Power BI**、**PowerApps** を選択します。 その他の関連する適用製品を選択することもでき、コンサルティング サービス オファーは AppSource のこれらの各製品に関連付けられている一覧に表示されます。

**関連するコンピテンシー**

このオファーに関連するコンピテンシーを選択し、オファーの詳細と一緒に表示されるようにします。

#### <a name="marketing-artifacts"></a>マーケティングの成果物

**会社のロゴ (.png 形式、48 x 48 ピクセル)**

オファーのギャラリー ビュー ページでオファーのタイルに表示される画像をアップロードします。 画像は、解像度が 48 x 48 ピクセルの .png イメージである必要があります。

**会社のロゴ (.png 形式、216 x 216 ピクセル)**

オファーの詳細ページに表示される画像をアップロードします。 画像は、解像度が 216 x 216 ピクセルの .png イメージである必要があります。

**ビデオ (最大 4 つ)**

顧客ケース スタディのビデオまたは顧客リファレンス ビデオを最大 4 つアップロードします。 そのようなビデオがない場合は、オファーに関連する会社の得意分野を説明するビデオをアップロードします。 Power BI または PowerApps のソリューション ショーケースがある場合は、そのショーケース ビデオをここにアップロードします。 ビデオのリンクは、YouTube または Vimeo 用でなければなりません。

**ドキュメント (最大 3 つ)**

コンサルティング サービス オファーの詳細を説明するマーケティング パンフレットをアップロードします。 あるいは、会社概要、ファクト シート、ケース スタディをアップロードすることもできます。

**スクリーンショット (最大 5 つ)**

オファー、オファーの成果物、会社の詳細情報を示す最大 5 つの画像をアップロードします。 たとえば、マーケティング パンフレットのスニペット、プレゼンテーションからの関連スライド、会社の勢いや得意分野を示す画像などです。

### <a name="publish-your-offer"></a>オファーを発行する

[プラン設定]、[ネットショップの詳細]、[連絡先] を入力したら、**[Publish (発行)]** を選択し、電子メール アドレスを指定します。 Microsoft がオファーを発行する準備ができたら、運用開始になる前に、オファーをプレビューするための電子メールが届きます。 ポータルに戻り、いつでもオファーの状態を確認することができます。

発行処理中にオファーが "Publish canceled (発行取り消し)" または "Publish failed (発行失敗)" の状態で表示されることがあります。 この状態は処理の通常の部分であり、Microsoft はオファーを編集することができます。 オファーが "Publish canceled (発行取り消し)" で表示された場合は、その状態のままにしてください。
