---
title: Microsoft AppSource (Azure Marketplace) での Power BI アプリ オファーの計画ガイド
description: この記事は、Microsoft AppSource (Azure Marketplace) での Power BI アプリ オファーの計画ガイドです
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 05/26/2021
ms.openlocfilehash: 7f21e3b7e95bf4d735d394a9dfebe04023218faa
ms.sourcegitcommit: 23040f695dd0785409ab964613fabca1645cef90
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/14/2021
ms.locfileid: "112061238"
---
# <a name="plan-a-power-bi-app-offer"></a>Power BI アプリ オファーを計画する

この記事では、Power BI アプリを Microsoft [AppSource](https://appsource.microsoft.com/) に発行するために準備または完了しておく必要がある、コンテンツと要件について説明します。 Power BI アプリでは、データセット、レポート、およびダッシュボードを含むカスタマイズ可能なコンテンツがパッケージ化されます。 その後、AppSource を使ってアプリを他の Power BI プラットフォームで使用し、開発者によって許可されている調整とカスタマイズを実行し、それをご自身のデータに接続できます。

開始する前に、テンプレート、ヒント、サンプルを提供する次のリンクをご確認ください。

- [Power BI アプリを作成する](/power-bi/service-template-apps-create)
- [Power BI アプリの作成に関するヒント](/power-bi/service-template-apps-tips)
- [サンプル](/power-bi/service-template-apps-samples)


## <a name="publishing-benefits"></a>公開の利点

コマーシャル マーケットプレースに公開する利点:

- Microsoft のブランドを使用して、会社の宣伝ができる。
- AppSource 上では 1 億を超える Microsoft 365 および Dynamics 365 のユーザーに、さらに Azure Marketplace を通じて 20 万を超える組織に接触できる可能性がある。
- これらのマーケットプレースから質の高いリードを得ることができる。
- Microsoft のフィールドおよびテレセールス チームによりサービスを宣伝してもらえる。

## <a name="overview"></a>概要

:::image type="content" source="media/power-bi/power-bi-app-publishing-steps.png" alt-text="Power BI アプリ オファーを発行する手順の概要。" border="false":::

ここでオファーを作成する準備ができたら、以下の「[次の手順](#next-steps)」をご覧ください。 それ以外の場合は、引き続き以下をお読みになり、オファー作成プロセスを開始する前の準備を確実かつ適切に行ってください。

以降のトピックでは、主な発行手順について説明します。

1. Power BI でアプリケーションを作成します。 パッケージのインストール リンクを受け取ります。これは、オファーの主な技術資産です。 パートナー センターでオファーを作成する前に、テスト パッケージを運用前環境に送信します。 詳細については、[Power BI アプリ](/power-bi/service-template-apps-overview)に関するページをご覧ください
2. 公式名、説明、ロゴなどのマーケティング資料を追加します。
3. 使用条件、プライバシー ポリシー、サポート ポリシー、ユーザー ヘルプなど、オファーの法的およびサポート ドキュメントを含めます。
4. オファーを作成する - パートナー センターを使用して、オファーの説明、マーケティング資料、法的情報、サポート情報、資産の仕様などの詳細を編集します。
5. それを公開するために送信します。
6. AppSource オンボーディング チームによって、アプリのテスト、検証、および認定が行われる、パートナー センターでプロセスを監視します。
7. 認定された後、アプリをそのテスト環境で確認してからリリースします。 これで、AppSource に一覧表示されます ("一般公開" される)。
8. Power BI で、パッケージを運用環境に送信します。 詳細については、[Power BI アプリ リリースの管理](/power-bi/service-template-apps-create#manage-the-template-app-release)に関する記述を参照してください。

## <a name="requirements"></a>必要条件

商用マーケットプレースで公開するには、Power BI アプリ オファーが次の技術的およびビジネス要件を満たしている必要があります。

### <a name="technical-requirements"></a>技術的な要件

必要となる主な技術資産は [Power BI アプリ](/power-bi/connect-data/service-template-apps-overview)です。 これは、プライマリ データセット、レポート、またはダッシュボードのコレクションです。 オプションの接続済みサービスと埋め込みデータセットも含まれています。これは、以前は[コンテンツ パック](/power-bi/service-organizational-content-pack-introduction)と呼ばれていました。 この種類のアプリの開発について詳しくは、[Power BI アプリ](/power-bi/connect-data/service-template-apps-overview)に関するページを参照してください。

#### <a name="get-an-installation-web-address"></a>インストール Web アドレスを取得する

Power BI アプリをビルドできるのは、[Power BI](https://powerbi.microsoft.com/) 環境内のみです。

1. [Power BI Pro ライセンス](/power-bi/service-admin-purchasing-power-bi-pro)でサインインします。
2. Power BI でアプリを作成してテストします。
3. アプリのインストール Web アドレスを受け取ったら、パートナー センターの **[技術的な構成]** ページに追加します。

Power BI でアプリが作成され、テストされた後、アプリケーションのインストール Web アドレスを保存します。これは [Power BI アプリ オファーを作成する](power-bi-app-offer-setup.md)際に必要になるためです。

### <a name="business-requirements"></a>ビジネス要件

ビジネス要件には、手続き、契約、法律に関する義務が含まれます。 次の手順が必要です。

- 登録済みの商用マーケットプレース パブリッシャーになります。 登録していない場合は、「[パートナー センターでコマーシャル マーケットプレース アカウントを作成する](create-account.md)」の手順に従ってください。
- AppSource にオファーが掲載されるための条件を満たすコンテンツを提供します。 詳細については、[AppSource にアプリを掲載する方法](https://appsource.microsoft.com/blogs/have-an-app-to-list-on-appsource-here-s-how)に関するページをご覧ください。
- [Microsoft のプライバシーに関する声明](https://privacy.microsoft.com/privacystatement)に同意して従います。

## <a name="licensing-options"></a>ライセンス オプション

Power BI アプリ オファーで使用できるのは、次のライセンス オプションだけです。

| ライセンス オプション | トランザクション プロセス |
| --- | --- |
| Get it now (今すぐ入手する) (無料) | 無料のオファーの一覧を顧客に提示します。 |
|

\* 公開元は、ソフトウェア ライセンス トランザクションのすべての側面 (注文、フルフィルメント、使用状況測定、課金、請求、支払い、収集を含みますが、これらに限定されません) をサポートします。

## <a name="customer-leads"></a>潜在顧客

パートナー センターを使用してオファーをコマーシャル マーケットプレースに公開している場合、それをカスタマー リレーションシップ マネジメント (CRM) システムに接続することができます。 これにより、自社の製品に顧客が関心を示したり、製品を使用したりした場合はすぐにその顧客の連絡先情報を受信できるようになります。 体験版を有効にする場合は、CRM に接続する必要があります。それ以外の場合、CRM への接続は任意です。 パートナー センターでは、Azure テーブル、Dynamics 365 Customer Engagement、HTTPS エンドポイント、Marketo、および Salesforce がサポートされています。

## <a name="legal-contracts"></a>法的契約

オファーを試す前にお客様が同意しなければならないご契約条件や、その条件が記載されたリンクが必要になります。

## <a name="offer-listing-details"></a>オファー登録情報の詳細

> [!NOTE]
> オファーの説明が「このアプリケーションは、[英語以外の言語] でのみ利用可能です」という文言で始まっている場合、オファー登録情報は英語である必要はありません。

オファーの作成をより簡単にするには、これらの項目をあらかじめ準備します。 特に明記されている場合を除き、すべて必須です。

- **名前** - 名前は、コマーシャル マーケットプレースでオファー登録情報のタイトルとして表示されます。 この名前は商標登録されている場合があります。 これは、絵文字 (商標および著作権マークの場合を除く) を使用できず、50 文字に制限されています。
- **Search results summary (検索結果の概要)** - オファーの目的または機能を、100 文字以内で改行のない単一文として記載します。 これは、コマーシャル マーケットプレースの登録情報の検索結果で使用されます。
- **説明** - この説明は、コマーシャル マーケットプレースの登録情報の概要に表示されます。 価値提案、主なメリット、対象となるユーザー ベース、カテゴリまたは業界との関連性、アプリ内の購入機会、必要な情報開示、詳細情報へのリンクを含めることを検討してください。 このテキスト ボックスには、説明をより魅力的にするリッチ テキスト エディター コントロールが用意されています。 必要に応じて、書式設定に HTML タグを使用します。
- **検索キーワード** (省略可能) – 顧客がオファーを見つけるために使用できる最大 3 つの検索キーワード。 オファーの **名前** と **説明** は含めないでください。そのテキストは、検索に自動的に含まれます。
- **アプリが連携する製品** (省略可能) – オファーが連携する最大 3 つ製品の名前。
- **ヘルプ/プライバシー ポリシーのリンク** – 会社のヘルプとプライバシー ポリシーの URL。 ご自身でアプリがプライバシーに関する法律および規則に準拠するようにする必要があります。
- **連絡先情報**
  - **サポートの連絡先** – 顧客がチケットを開いたときに Microsoft パートナーが使用する名前、電話番号、およびメール アドレス。 サポート Web サイトの URL を含めます。
  - **エンジニアリングの連絡先** – オファーに問題がある場合に直接使用する、Microsoft の名前、電話番号、メール アドレス。 この連絡先情報は、コマーシャル マーケットプレースには表示されません。
  - **サポート ドキュメント** (省略可能) – PDF 形式の最大 3 つの顧客向けドキュメント (ホワイトペーパー、パンフレット、チェックリスト、PowerPoint プレゼンテーションなど)。
- **メディア**
    - **ロゴ** – **大** ロゴ用の PNG ファイル。 パートナー センターではこれを使用して、その他の必要なロゴ サイズを作成します。 必要に応じて、別の画像に置き換えることもできます。
    - **スクリーンショット** – オファーがどのように機能するかを示す最小 1 つ、最大 5 つのスクリーンショット。 画像は、1280 x 720 ピクセル (PNG 形式) で、キャプションが含まれている必要があります。
    - **ビデオ** (省略可能) - オファーをデモンストレーションする最大 4 つのビデオ。 名前、YouTube または Vimeo の URL、1280 x 720 ピクセルの PNG サムネイルを含めます。

> [!Note]
> コマーシャル マーケットプレースに公開するには、オファーが一般的な[コマーシャル マーケットプレースの認定ポリシー](/legal/marketplace/certification-policies#100-general)を満たしている必要があります。

## <a name="additional-sales-opportunities"></a>その他の営業案件

Microsoft がサポートするマーケティングおよびセールス チャネルのオプトインを選択できます。 パートナー センターでオファーを作成すると、プロセスの終盤で、次の 2 つのタブが表示されます。

- **Microsoft との共同販売** - Microsoft セールス チームが顧客のニーズを評価するときに、IP の共同販売対象ソリューションを検討できます。 共同販売の資格の詳細については、[共同販売の状態の要件](/legal/marketplace/certification-policies)に関する記事を参照してください。 評価のためにオファーを準備する方法の詳細については、[パートナー センターの [共同販売] オプション](./co-sell-configure.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

- [Power BI アプリ オファーを作成する](power-bi-app-offer-setup.md)
