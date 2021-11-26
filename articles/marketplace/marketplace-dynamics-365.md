---
title: Microsoft AppSource 用に Dynamics 365 オファーを計画する - Azure
description: Microsoft AppSource 用に Dynamics 365 オファーを計画する
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: vamahtan
ms.author: vamahtan
ms.date: 04/30/2021
ms.openlocfilehash: 0d31c44a5daa21c16cd7c598f407501104dc53c6
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132546312"
---
# <a name="plan-a-microsoft-dynamics-365-offer"></a>Microsoft Dynamics 365 オファーを計画する

この記事では、コマーシャル マーケットプレースの Microsoft AppSource に含まれる Dynamics 365 オファーのさまざまなオプションと機能について説明します。 AppSource には、Microsoft 365、Dynamics 365、Power Apps、Power BI 上に構築される、またはそれらを拡張するオファーが含まれています。

最初に、[パートナー センター](./create-account.md)でコマーシャル マーケットプレース アカウントを作成してコマーシャル マーケットプレース プログラムに登録します。 また、[公開プロセスとガイドライン](/office/dev/store/submit-to-appsource-via-partner-center)を確認します。

## <a name="licensing-options"></a>ライセンス オプション

新しいオファーを公開する準備を行う際、選択するライセンス オプションを決定する必要があります。 これにより、後でパートナー センターでオファーを作成する際に提供する必要がある追加情報が決まります。

Dynamics 365 オファー タイプで使用可能なライセンス オプションは次のとおりです。

| プランの種類 | リスト オプション |
| --- | --- |
| Dynamics 365 for Operations | [Contact me (お問い合わせ)] |
| Dynamics 365 Customer Engagement および PowerApps | 今すぐ入手する<br>Get it now (今すぐ入手する) (無料)<br>Free trial (無料試用版) (一覧)<br>[Contact me (お問い合わせ)] |
| Dynamics 365 Business Central | Get it now (今すぐ入手する) (無料)<br>Free trial (無料試用版) (一覧)<br>[Contact me (お問い合わせ)] |
|||

次の表では、各リスト オプションのトランザクション プロセスについて説明します。

| ライセンス オプション | トランザクション プロセス |
| --- | --- |
| [Contact me (お問い合わせ)] | 顧客関係管理 (CRM) システムに接続して、顧客の連絡先情報を収集します。 顧客は、自分の情報を共有する許可を求められます。 これらの顧客の詳細は、オファーの名前と ID のほか、顧客がオファーを見つけたマーケットプレース ソースと一緒に、お客様が構成した CRM システムに送信されます。 CRM の構成の詳細については、お客様のオファーの種類の **[オファーのセットアップ]** ページの **[潜在顧客]** セクションを参照してください。 |
| Free trial (無料試用版) (一覧) | 1、3、または 6 か月の無料試用版を顧客に提供します。 オファー登録情報の無料試用版がご利用のサービスによって作成、管理、および構成され、Microsoft によって管理されるサブスクリプションはありません。 |
| Get it now (今すぐ入手する) (無料) | 無料のオファーの一覧を顧客に提示します。 |
| 今すぐ入手する | パートナー センターで ISV アプリのライセンスを管理できます。<br>現在、次のオファーの種類でのみ使用できます。<ul><li>Dynamics 365 for Customer Engagement & Power Apps</li></ul><br>このオプションの詳細については、「[ISV アプリのライセンス管理](isv-app-license.md)」を参照してください。 |
|||

## <a name="test-drive"></a>体験版

[!INCLUDE [Test drives section](includes/test-drives.md)]

## <a name="customer-leads"></a>潜在顧客

パートナー センターを使用してオファーをコマーシャル マーケットプレースに公開している場合、それをカスタマー リレーションシップ マネジメント (CRM) システムに接続することができます。 これにより、自社の製品に顧客が関心を示したり、製品を使用したりした場合はすぐにその顧客の連絡先情報を受信できるようになります。 体験版を有効にする場合は、CRM に接続する必要があります。それ以外の場合、CRM への接続は任意です。 パートナー センターでは、Azure テーブル、Dynamics 365 Customer Engagement、HTTPS エンドポイント、Marketo、および Salesforce がサポートされています。

## <a name="legal"></a>法的情報

**ご契約条件** を作成します。 顧客は、オファーを試す前にこれらに同意する必要があります。 Microsoft には標準契約がありますが、これは Dynamics 365 オファーには適用されません。

## <a name="offer-listing-details"></a>オファー登録情報の詳細

Microsoft AppSource でのオファー情報の表示例を次に示します (表示されている価格は例示のみを目的としており、実際のコストを反映していません)。

:::image type="content" source="media/dynamics-365/example-dynamics-365-operations.png" alt-text="Microsoft AppSource でこのオファーがどのように表示されるかを示しています。":::

> [!NOTE]
> オファーの説明が「このアプリケーションは、[英語以外の言語] でのみ利用可能です」という文言で始まっている場合、オファー登録情報は英語である必要はありません。

オファーの作成をより簡単にするには、これらの項目をあらかじめ準備します。 特に明記されている場合を除き、すべて必須です。

- **名前** - 名前は、コマーシャル マーケットプレースでオファー登録情報のタイトルとして表示されます。 この名前は商標登録されている場合があります。 これは、絵文字 (商標および著作権マークの場合を除く) を使用できず、50 文字に制限されています。
- **Search results summary (検索結果の概要)** - オファーの目的または機能を、100 文字以内で改行のない単一文として記載します。 これは、コマーシャル マーケットプレースの登録情報の検索結果で使用されます。
- **説明** - この説明は、コマーシャル マーケットプレースの登録情報の概要に表示されます。 価値提案、主なメリット、対象となるユーザー ベース、カテゴリまたは業界との関連性、アプリ内の購入機会、必要な情報開示、詳細情報へのリンクを含めることを検討してください。 このテキスト ボックスには、説明をより魅力的にするリッチ テキスト エディター コントロールが用意されています。 必要に応じて、書式設定に HTML タグを使用します。
- **検索キーワード** (省略可能) – 顧客がオファーを見つけるために使用できる最大 3 つの検索キーワード。 オファーの **名前** と **説明** は含めないでください。そのテキストは、検索に自動的に含まれます。
- **Products your app works with (アプリが連携する製品)** (省略可能) – オファーが連携する最大 3 つ製品の名前。
- **Help and privacy policy links (ヘルプおよびプライバシー ポリシーのリンク)** - オファーのヘルプと会社のプライバシー ポリシーの URL。 ご自身でアプリがプライバシーに関する法律および規則に準拠するようにする必要があります。
- **連絡先情報**
  - **サポートの連絡先** – 顧客がチケットを開いたときに Microsoft パートナーが使用する名前、電話番号、およびメール アドレス。 サポート Web サイトの URL を含めます。
  - **エンジニアリングの連絡先** – オファーに問題がある場合に直接使用する、Microsoft の名前、電話番号、メール アドレス。 この連絡先情報は、コマーシャル マーケットプレースには表示されません。
- **サポート ドキュメント** - PDF 形式の最大 3 つの顧客向けドキュメント (ホワイトペーパー、パンフレット、チェックリスト、PowerPoint プレゼンテーションなど)。
- **メディア**
    - **ロゴ** – **大** ロゴ用の PNG ファイル。 パートナー センターではこれを使用して、その他の必要なロゴ サイズを作成します。 必要に応じて、別の画像に置き換えることもできます。
    - **スクリーンショット** – オファーがどのように機能するかを示す最小 1 つ、最大 5 つのスクリーンショット。 画像は、1280 x 720 ピクセル (PNG 形式) で、キャプションが含まれている必要があります。
    - **ビデオ** (省略可能) - オファーをデモンストレーションする最大 4 つのビデオ。 名前、YouTube または Vimeo の URL、1280 x 720 ピクセルの PNG サムネイルを含めます。

> [!Note]
> コマーシャル マーケットプレースに公開するには、オファーが一般的な[コマーシャル マーケットプレースの認定ポリシー](/legal/marketplace/certification-policies#100-general)を満たしている必要があります。

## <a name="additional-sales-opportunities"></a>その他の営業案件

Microsoft がサポートするマーケティングおよびセールス チャネルのオプトインを選択できます。 パートナー センターでオファーを作成しているとき、プロセスの終わり頃に **[Microsoft との共同販売]** に関するタブが表示されます。 このオプションを使用すると、Microsoft セールス チームは、顧客のニーズを評価するときに、IP の共同販売対象ソリューションを検討できます。 評価のためにオファーを準備する方法の詳細については、「[パートナー センターの [共同販売] オプション](./co-sell-configure.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

前述の計画項目を検討したら、次のいずれかを選択して (左側の目次にもあります)、オファーの作成を開始します。

| 公開ガイド    | Notes  |
| :------------------- | :-------------------|
| [Dynamics 365 for Operations](./dynamics-365-operations-offer-setup.md) | Enterprise Edition 向けに作成している場合は、最初に、こちらの追加の[公開プロセスとガイドライン](/dynamics365/fin-ops-core/dev-itpro/lcs-solutions/lcs-solutions-app-source)を確認します。 製品の種類には、Commerce、Finance、Human Resources、Project Operations、Supply Chain Management が含まれます。 |
| [Dynamics 365 for Business Central](dynamics-365-business-central-offer-setup.md) | N/A |
| [Dynamics 365 for Customer Engagement & Power Apps](dynamics-365-customer-engage-offer-setup.md) | 最初に、こちらの追加の[公開プロセスとガイドライン](/dynamics365/customer-engagement/developer/publish-app-appsource)を確認します。 製品の種類には、Customer Service、Customer Voice、Project Operations、Field Service、Marketing、Mixed Reality、Power Apps、Power Automate、Power Virtual Agents、Project Service Automation、Sales が含まれます。 |
|
