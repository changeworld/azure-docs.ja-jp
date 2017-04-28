---
title: "Cortana Intelligence アプリを AppSource に発行しますか? | Microsoft Docs"
description: "Cortana Intelligence または Microsoft R Services アプリを AppSource に発行するためのステップ バイ ステップ ガイド"
services: marketplace
documentationcenter: 
author: hiavi
manager: judym
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: abathula
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: 62aa3148406068f06d1fbdaf556ff2d526c0e17b
ms.lasthandoff: 04/13/2017


---
# <a name="publish-a-cortana-intelligence-app-to-appsource"></a>Cortana Intelligence アプリを AppSource に発行しますか?
Cortana Intelligence または Microsoft R Services アプリを AppSource に発行するためのステップ バイ ステップ ガイド

## <a name="1-introduction"></a>1.はじめに

Cortana Intelligence/Microsoft R Solutions アプリの Microsoft AppSource への発行に関心をお寄せくださりありがとうございます。 Microsoft は、弊社の専用パートナー (ISV/SI) と協力して、顧客、リセラー、および導入パートナーに、ビジネス ソリューションを見つけ、試行して、それぞれの投資を最大限に活用するための第一級の場所を提供できることを嬉しく思っています。 このガイドでは、ソリューションを公開するための各手順について説明します。

## <a name="2-getting-started"></a>2.使用の開始

手順に従います。

1.  [マイクロソフトで紹介してもらうためのスターター ガイド](https://www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20Documents/CESIPartnerJourneyOverview_1232017.pdf)で Advanced Analytics パートナーのリストに掲載してもらいます。

2.  AppSource で、[[アプリをリストに追加する]](https://appsource.microsoft.com/partners/list-an-app) フォームに入力します。
    - *"アプリがビルドされた製品を選択してください"* という質問で、**[その他]** チェックボックスをオンにして、編集コントロールに「Cortana Intelligence」を一覧表示します。

## <a name="3-solution-evaluation-criteria"></a>手順 3.ソリューションの評価基準

アプリが満たす必要がある条件の一覧を次に示します。

1.  アプリは、特定の機能領域内の特定のビジネス ユース ケースの問題に反復可能な方法で対応する必要があります。 最小限の構成/カスタマイズにより、定義済みの価値提案を短時間で提供できる必要があります。

2.  ソリューションは、次の分析コンポーネントの少なくとも 1 つを使用する必要があります。

    1. HDInsight
    2. 機械学習
    3. Data Lake Analytics
    4. Stream Analytics
    5. Cognitive Services
    6. Bot Framework
    7. スタンドアロンの Microsoft R Server または SQL 2016/HDInsight Premium 上の R Services

3.  ソリューションは、デジタル指名パートナー (DPOR) またはクラウド ソリューション プロバイダー (CSP) プログラムを使用して、少なくとも \$1 K/月/顧客を生み出す必要があります。

4.  ソリューションは、ユーザー認証およびリソース アクセス制御が有効になる同意の下で、Azure Active Directory フェデレーション シングル サインオン (AAD フェデレーション SSO) を使用する必要があります。 アプリが有効になっていない場合は、次を参照してください。

    1. [使用の開始](https://identity.microsoft.com/)
    2. [Azure Active Directory とアプリケーションの統合](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#adding-an-application)

5.  ソリューションで Power BI を使用し、共有するには Power BI Desktop ファイル (PBIX) を使用します。任意ですが、より多くの潜在顧客を生み出すことが実証されているため、強くお勧めします。

    1. すべてのデータが PBIX ファイルにインポートされていることを確認します。
    2. 外部から参照されるデータはありません。弊社が組み込みレポートを作成します。

## <a name="4-decide-on-the-type-of-offer-you-would-like-to-publish-on-appsource"></a>4.AppSource 上で発行するプランの種類を決定する

AppSource では、パートナーは 2 種類の試用版をビューアーに提供することができます。
1.  顧客主導試用版の場合、顧客は自力でアプリを体験することができます。
    1.  無料試用版
        1.  AppSource は、パートナーによって指定され、AAD フェデレーション SSO を使用してユーザーを導く URL にユーザーをナビゲートし、期間が指定された試用版を提供します。
        2.  ここでは、アプリが 1 人のユーザーの構成/データを他のユーザーの構成/データから分離できるマルチ テナント対応の SaaS アプリであるか、または試用版の場合は、読み取り専用操作だけが必要になる試用版のサブセットが提供されるだけです。

        例: [AFS POP Retail Execution](https://appsource.microsoft.com/product/web-apps/afs.fa9fc926-3bc3-43dd-becd-3ef41b52c10b?tab=Overview)、[AvePoint Citizen Services](https://appsource.microsoft.com/product/web-apps/avepoint.7738ac97-fd40-4ed3-aaab-327c3e0fe0b3?tab=Overview) (このアプリは、選択したユーザー ペルソナのセットへの明確なパスを持つ、カスタマイズされたエクスペリエンスを提供します) などを参照してください。
    2.  体験版
        1.  パートナー ソリューションまたはそのサブセットは、AppSource がインスタンス化し、パートナー Azure サブスクリプションでの期間指定およびインスタンスのホット/コールド プールの保守によって管理できる Azure Resource Manager テンプレートを使用してパッケージ化することができます。
        2.  このルートを使用するように選択した場合に役立つテンプレートとサンプル コードを提供できます。
        
        例: Neal Analytics の[在庫の最適化](https://appsource.microsoft.com/product/web-apps/neal_analytics.8066ad01-1e61-40cd-bd33-9b86c65fa73a?tab=Overview&tag=CISHome)を参照してください。

2.    パートナー主導試用版の場合、パートナーは顧客をフォロー アップし、顧客にデモ/試用版などを提供できるように、連絡先情報フォームの記入を顧客に依頼する必要があります。

概要については、[AppSource 試用版チュートリアル](http://aka.ms/trialexperienceforwebapps)ビデオを参照してください。

顧客主導試用版とパートナー主導仕様版を比較したときに、顧客主導試用版の方がより多数の潜在顧客を生成することがデータによって明確に示されています。これに基づき、誰がフォームに入力するか考えてみてください。😊

AppSource に追加するプランの種類を決定します。

## <a name="5-getting-approved-to-use-azure-marketplace-publishing-portal"></a>5.Azure Marketplace 発行ポータルを使用して承認を得る

現時点では、Azure Marketplace 発行ポータルにアクセスして発行を開始できるようになるには、AAD/MSA 電子メール ID が承認される必要があります。 次の詳細情報を以下のメール アドレス宛てに送信して共有してください。[appsourcecissupport@microsoft.com](mailto:appsourcecissupport@microsoft.com?subject=Request%20publisher%20account%20creation%20for%20%3cPartner%20Name%3e%20and%20whitelist%20owner/contributer%20AAD/MSA%20email%20IDs)

-   発行元表示名: &lt;アプリの発行元として AppSource に表示する会社の名前&gt;

-   発行元の一意の名前: &lt;小文字の文字とハイフン '-' のみを使用した一意の名前&gt;

-   Web サイト: &lt;会社の Web サイト&gt;

-   所有者の電子メール: &lt;AAD/MSA 電子メール ID&gt;

-   共同作成者 2 の電子メール: &lt;AAD/MSA 電子メール ID&gt;

 必要な場合、共同作成者をさらに追加します。

## <a name="6-get-info-about-your-companys-lead-management"></a>6.会社の潜在顧客の管理に関する情報を取得する

訪問者にはソリューションに明白な関心があるので、これらの潜在顧客はパートナーの潜在顧客管理システムに直接渡されます。 AppSource は、複数のタイプの潜在顧客管理システム (Dynamics CRM、Salesforce、Marketo など) をサポートします。

全体的なプロセスの詳細と AppSource の潜在顧客をセットアップするための適切な情報を表示する方法については、[AppSource マーケティング潜在顧客管理](./cloud-partner-portal-get-customer-leads.md)を参照してください。    

## <a name="7-publish-your-app-on-the-publishing-portal"></a>7.発行ポータルでアプリを発行する

スキップされたフィールドに関する情報が必要な場合は、次のアドレスに電子メールでお問い合わせください。<appsourcecissupport@microsoft.com>

次の手順に従います。

1.  Google Chrome ブラウザーを使用して、[クラウド パートナー ポータル](https://cloudpartner.azure.com)を開きます。

2.  ホワイトリストされている AAD/MSA 電子メール ID を使用してサインインします。

3.  [新しいプラン] &gt; [Cortana Intelligence] を選択します。

    ![publishaaapp1][1]
4.  プラン設定の詳細を入力します。

    ![publishaaapp2][2]

    1. プラン ID は AppSource に固有の URL の一部として AppSource でアプリを一意に識別するために使用されます。 例: “appsource-saas-app"。 顧客は、Azure Resource Manager テンプレートや請求レポートなどの場所でプラン ID を確認できます。 プラン ID および発行元の情報は、いったん保存すると変更できないことに注意してください。

    2. プラン名の場合、ソリューションの名前を使用します。 ソリューション名の下に自動的に追加されるので、会社名を追加しないでください。

5.  [技術情報の入力] セクション - 大部分の情報は見ればすぐにわかるものであり、詳細情報を表示するためのツール ヒントが用意されています。

    1.  デモ ビデオでは、実際のソリューション機能の録画を最低限に編集し、AAD を使用したユーザー認証、ユーザー向け機能の主要な側面、および Cortana Intelligence プラットフォームとの統合を強調したものである必要があります。  デモ ビデオは、顧客向けに一般に公開**されることはありません**が、見込顧客がソリューションを確認*できる*代表的な方法であると想定されています。  そのため、デモ ビデオはスクリプト化され、繰り返し可能になっている必要があります。

        デモ ビデオの準備には、標準的なビデオ形式 (たとえば、MPEG) をエクスポートする任意の画面録画ツールを使用できます。 Skype for Business をお持ちの場合の手順を以下に示します。

        1.  [会議を開始します](https://support.office.com/article/Start-a-Skype-for-Business-conference-call-8dc8ac52-91ac-4db9-8672-11551fdaf997)
        2. [デスクトップを共有します](https://support.office.com/article/Share-your-screen-in-Skype-for-Business-2d436dc9-d092-4ef1-83f1-dd9f7a7cd3fc)
        3. [録画を開始します](https://support.office.com/article/Share-sites-or-documents-with-people-outside-your-organization-80e49744-e30f-44db-8d51-16661b1d4232)
        4. 録画を停止後、[録画マネージャーを使用して、録画を発行します](https://support.office.com/article/Recording-Manager-save-and-publish-59a3beb7-c700-40cf-ab21-bc82a2b06351)

        共有 URL を生成できるサービスに、録画したビデオをアップロードする必要があります。  たとえば、[OneDrive/Sharepoint のゲスト リンク](https://support.office.com/article/Share-sites-or-documents-with-people-outside-your-organization-80e49744-e30f-44db-8d51-16661b1d4232)を使用することができます。 

    2.  ソリューション アーキテクチャの図をアップロードする場合のガイドラインについては、[高度な分析ソリューションのワークフロー テンプレート](https://partnersprofilesint.blob.core.windows.net/partner-assets/documents/AppSource%20Solution%20Publishing%20Guide%20Docs/Advanced%20Analytics%20Solution%20Workflow%20Template%20V.2017.3.23.pptx)を参照してください。

6.  [店舗の詳細] セクションに入力します。

    1.  注: [プランの説明] および [利用規約] フィールドは html コンテンツを使用できます。 任意のオンライン html エディターを使用して、最適な見た目になるまで html コンテンツを調整できます。 プランの説明では、プランが解決するビジネス ケースを説明する宣伝文句、顧客が期待できる ROI、および宣伝文句を裏付ける関連資料を、きわめて明確かつ簡潔に示すようにします。

    2.  アプリの種類: 「AppSource 上で発行するプランの種類を決定する」というタイトルの前のセクションに基づき、次のオプションを選択します。
        1.  無期限の完全無料エクスペリエンスを顧客に提供する場合、[無料]。
        2.  期限付きの試用版/体験版を顧客に提供する場合、[試用版]。
        3.  パートナー主導の試用版を顧客に提供する場合、[試用版を要求]。

    3.  ステージング環境で、プランをテストするキーを非表示にする: ステージングされ、シンプルにするためにパスワードなしに設定した後に、アプリのプレビュー URL を作成するために使用されます。 例: "AppSourceHideKey"

    4.  プラン発行の承認を得るためには、アップロードされたイメージが高品質の読み取り可能なイメージである必要があります。

    5.  ケース スタディ: 以下の詳細に基づいて、ケース スタディ/ストーリーを提供します。

        1.  運用可能な状態の顧客の実装
        2. 実際の顧客または匿名化された顧客に対して明確に定義された問題の説明
        3. 問題を解決するために講じる手順
        4. 問題の解決に役立った、リファレンスの CIS/Microsoft Advance Analytics コンポーネント
        5.  ソリューション策定の正味メリットまたは ROI
        6. *望ましくないもの*: 上記の要件を満たさない、単にプラットフォーム全体を説明するだけのマーケティング パンフレットまたはビデオ

    6.  潜在顧客を増加させるため強く推奨: AppSource に表示するケース スタディと同じ要件を満たすビデオ。 ビデオの URL を入力するとき、Youtube ビデオまたは Vimeo ビデオを指定できます。 ビデオが次のいずれかの形式であることを確認してください。
        1. https://vimeo.com/########## または
        2. https://www.youtube.com/watch?v=##########

7.  [連絡先] セクションの記入: 使用状況の通知、 マーケットプレイスのアラートなどを送信するために使用されます。

8.  プランを発行します。 ソリューションが認定プロセスを通過すると、電子メール通知が送信されます。 プラン発行 UI の [状態] タブで状態の詳細を確認できます。

    1.  最初にソリューションがステージングされます。
    2.  電子メールで送信される AppSource リンクをクリックして、ステージングされたコンテンツを確認できます。
    3.  必要な変更を行い、再送信します。 最終的なコンテンツに問題がなければ、実稼働環境にアプリをプッシュします。
    4.  この時点で、弊社はアプリのメタデータを検証し、アプリを AppSource に一覧表示するか、アプリを拒否する場合は、拒否理由に基づいてフォロー アップを行います。

[1]: ./media/cloud-partner-portal-publish-cortana-intelligence-app/publishaaapp1.png
[2]: ./media/cloud-partner-portal-publish-cortana-intelligence-app/publishaaapp2.png    

