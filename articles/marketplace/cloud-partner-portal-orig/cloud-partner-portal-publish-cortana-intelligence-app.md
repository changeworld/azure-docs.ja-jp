---
title: Cortana Intelligence | Microsoft Docs
description: Cortana Intelligence オファーを発行する。
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
ms.date: 09/18/2018
ms.author: pbutlerm
ms.openlocfilehash: f12a15a0d739ae6e98be3871fa8bb4104f49565c
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2018
ms.locfileid: "48808552"
---
# <a name="publish-a-cortana-intelligence-offer-using-the-cloud-partner-portal"></a>Cloud パートナー ポータルを使用して Cortana Intelligence オファーを発行する

この記事では、Cloud パートナー ポータルを使用して Cortana Intelligence オファーを発行する方法を説明します。

## <a name="prerequisites"></a>前提条件

Cloud パートナー ポータルはポータルへのロールベース アクセスをサポートしているため、共同作成者は協力してオファーを発行することができます。 詳細は、「[Cloud ポータルでのユーザーの管理](./cloud-partner-portal-manage-users.md)」を参照してください。

パブリッシャー アカウントを代表してオファーを発行する前に、\"所有者\"ロールを持つ人の 1 人が、[使用条件](https://azure.microsoft.com/support/legal/website-terms-of-use/)、[Microsoft のプライバシーに関する声明](http://www.microsoft.com/privacystatement/default.aspx)、[Microsoft Azure 認定プログラム契約](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/)を遵守することに同意する必要があります。

## <a name="to-start-creating-a-cortana-inteligence-offer"></a>Cortana Intelligence オファーの作成を開始する

すべての前提条件が満たされると、Cortana Intelligence オファーの作成を開始できるようになります。

1.  [Cloud パートナー ポータル](http://cloudpartner.azure.com/)にサインインします。
2.  左側のナビゲーション バーで、**[+新しいオファー]** を選択します。
3. **[Cortana Intelligence]** を選択します。
4. **[新しいオファー]** ビューで **[エディター]** タブを選択し、次のオプションを構成します。
    -   プラン設定
    -   技術情報
    -   ネットショップの詳細
    -   連絡先

    上記の各オプションは、入力するフォームを示しています。各フォームの必須フィールドには赤いアスタリスク (\*) が付いています。

## <a name="to-configure-offer-settings"></a>オファーの設定を構成する

オファーの設定には、オファー ID、パブリッシャー ID、オファー名など、オファーの基本的な情報が含まれています。

### <a name="offer-id"></a>プラン ID

これは、パブリッシャー プロファイル内で一意のプラン識別子です。
この ID は製品 URL に含まれます。 小文字の英数字またはハイフン (-) のみで構成できます。 この ID はハイフンで終えることはできず、長さは最大で 50 文字です。 
>[!Note]
>このフィールドは、オファーの運用が開始されるとロックされます。

**例:**

**contoso** というパブリッシャーが *sample-Cortana Intelligence* というオファー ID を持つオファーを作成した場合、AppSource では "https://appsource.microsoft.com/marketplace/apps/**contoso**.*sample-Cortana Intelligence*?tab=Overview" と表示されます。

### <a name="publisher-id"></a>パブリッシャー ID

パブリッシャー プロファイルのドロップダウン リストです。 このオファーを発行するパブリッシャー プロファイルを選択するために使用します。

>[!Note]
>このフィールドは、オファーの運用が開始されるとロックされます。

### <a name="name"></a>Name

オファーの表示名です。 この名前は [AppSource](https://appsource.microsoft.com) で表示されます。 最大で 50 文字の長さにできます。

[プラン設定] で必要な情報をすべて入力したら、**[保存]** を選択してオファーの [技術情報] の部分に移ります。 オプション。

## <a name="to-configure-technical-info"></a>技術情報を構成する

このビューを使用して、オファー ページで表示される技術情報を指定します。 以下のフィールドがこのビューに含まれます。

### <a name="partner-mpn-id"></a>パートナーの MPN Id

登録された Microsoft パートナーの場合は、[パートナーの Web サイト](https://partners.microsoft.com/) に移動してサインインし、組織のパートナー ID を取得します。その ID を **[Partner MPN Id]** に入力します。

### <a name="analytics-components-used"></a>使用する Analytics コンポーネント

オファーで使用するコンポーネントをすべて選択します。 少なくとも 1 つのコンポーネントを選択します。 SQL 2016 R Services、HDInsight Premium、VM 上で実行されている MRS のうちいずれかの MRS ライセンスを使用している場合のみ、**[Microsoft R]** を選択します。

### <a name="additional-components-used"></a>使用する追加のコンポーネント

ソリューションで使用されるその他のコンポーネントをすべて選択します。

### <a name="customer-name-using-solution"></a>ソリューションを使用している顧客名

このソリューションを運用環境で使用している顧客名を指定します。 

>[!Note]
>この情報は AppSource で公開されません。 ソリューションの評価のみに使用されます。

### <a name="azure-consumption-requirement-met"></a>Azure の消費要件が満たされているか?

ソリューションが Azure Suite コンポーネントの 1 顧客当たり毎月少なくとも 1,000 ドルを生み出しているか、またはソリューションが Microsoft R を使用しているか。

>[!Note]
>この情報は AppSource で公開されません。 ソリューションの評価のみに使用されます。

### <a name="demo-video-url"></a>デモ ビデオの URL

プリセールス チームが顧客に見せるソリューション/アプリのデモ ビデオの URL を指定します。 

>[!Note]
>この情報は AppSource で公開されません。 ソリューションの評価のみに使用されます。

#### <a name="demo-video-guidelines"></a>デモ ビデオのガイドライン

- ビデオの長さは 15 分未満にします。
- ビデオはソリューションの機能を録画したものを、最小限に編集したものであるべきです。 
- ビデオはユーザーが使用する機能の重要な特徴を目立たせ、高度な分析との統合に重点を置いたものにします。 
- デモ ビデオは、顧客向けに一般に公開**されることはありません**が、見込顧客がソリューションを確認*できる*代表的な方法であると想定されています。 そのため、デモ ビデオはスクリプト化され、繰り返し可能になっている必要があります。
- ビデオの作成には、標準的なビデオ形式 (たとえば、MPEG) をエクスポートする任意の画面録画ツールを使用できます。 

次の手順は、Skype for Business を使用してビデオを作成する方法を示しています。 

1. [会議を開始します](https://support.office.com/article/Start-a-Skype-for-Business-conference-call-8dc8ac52-91ac-4db9-8672-11551fdaf997)
2. [デスクトップを共有します](https://support.office.com/article/Share-your-screen-in-Skype-for-Business-2d436dc9-d092-4ef1-83f1-dd9f7a7cd3fc)
3. [録画を開始します](https://support.office.com/article/Share-sites-or-documents-with-people-outside-your-organization-80e49744-e30f-44db-8d51-16661b1d4232)
4. 録画を停止後、[録画マネージャーを使用して、録画を発行します](https://support.office.com/article/Recording-Manager-save-and-publish-59a3beb7-c700-40cf-ab21-bc82a2b06351)

共有 URL を生成できるサービスに、録画したビデオをアップロードします。 たとえば、[OneDrive/SharePoint のゲスト リンク](https://support.office.com/article/Share-sites-or-documents-with-people-outside-your-organization-80e49744-e30f-44db-8d51-16661b1d4232)を使用できます。

### <a name="supported-regions"></a>サポートされているリージョン

ソリューションがサポートするすべてのリージョンを選択します。 少なくとも 1 つのリージョンを選択します。

### <a name="power-bi-desktop-file-pbix"></a>Power BI Desktop ファイル (.pbix)

該当する場合、.pbix ファイルをアップロードします。 外部を参照するのではなく、データがファイルにインポートされているようにします。 埋め込みレポートは弊社が作成します。

### <a name="solution-architecture"></a>ソリューションのアーキテクチャ

ソリューションのアーキテクチャを詳細に説明するドキュメントをアップロードします。 ソリューション アーキテクチャの図をアップロードする場合のガイドラインについては、「[高度な分析ソリューションのワークフロー テンプレート](https://partnersprofilesint.blob.core.windows.net/partner-assets/documents/AppSource%20Solution%20Publishing%20Guide%20Docs/Advanced%20Analytics%20Solution%20Workflow%20Template%20V.2017.3.23.pptx)」を参照してください。

>[!Note]
>この情報は AppSource で公開されません。 ソリューションの評価のみに使用されます。

### <a name="select-segments"></a>セグメントの選択

関連する業界セグメントをすべて選択します。 アプリが対象としているセグメントがリストにない場合、**[その他]** フィールドにセグメント名を入力します。 セグメントとして入力できるのは 3 単語までです。

### <a name="select-business-processes"></a>ビジネス プロセスの選択

ソリューションに最も適しているビジネス プロセスを選択します。 アプリに該当するプロセスがリストにない場合、**[その他]** フィールドにプロセス名を入力します。 プロセスとして入力できるのは 3 単語までです。

### <a name="trial-info"></a>試用に関する情報

-   **[SaaS Trial URL (SaaS 試用版 URL)]:** アプリの試用版の URL を入力します。
-   **[Test Drive Trial URL (体験版 URL)]:** アプリの体験版の URL を入力します。

試用版の詳細については、この記事の次のセクションにある「**アプリの種類**」を参照してください。

[技術情報] で必要な情報をすべて入力したら、**[保存]** を選択してオファーの [ネットショップの詳細] の部分に移ります。 

## <a name="to-configure-storefront-details"></a>ネットショップの詳細を構成する

### <a name="offer-summary"></a>オファーの概要

これは、オファーの価値提案の概要です。 オファーの検索ページに表示されます。 この概要の長さは最大で 100 文字です。

### <a name="offer-description"></a>オファーの説明

アプリの詳細ページに表示される説明です。
この説明の長さは最大で 1,300 文字です。

このフィールドでは \<p\>、\<h1\>、\<h2\>、\<li\> タグなどの HTML コンテンツを使用できるので、内容を見栄えの良いものにできます。 発行ポータル チームは、ストアフロントの詳細をプレビュー表示できる機能の追加に取り組んでいます。この機能により、コンテンツを何度も変更してより魅力的にすることができるようになります。それまでの間、たとえば [http://htmledit.squarefree.com](http://htmledit.squarefree.com/) のような任意のオンライン リアルタイム html ツールを使用して、説明がどのように見えるかを確認できます。

説明として推奨される形式は、提供する価値に基づいてテキストをサブセクションに分割することです。各サブセクションはサブ見出しで強調します。 閲覧者は通常「オファーの概要」フィールドとサブ見出しにざっと目を通すことで、アプリの目的について要点を理解し、なぜそのアプリの購入を検討するかをほんの一目で判断します。 このため、ユーザーの注意を引き、詳細を読む動機付けを与えることが重要です。

#### <a name="partner-examples"></a>パートナーの例

- [Neal Analytics Inventory Optimization](https://appsource.microsoft.com/product/web-apps/neal_analytics.8066ad01-1e61-40cd-bd33-9b86c65fa73a?tab=Overview)
- [Plexure Retail Personalization](https://appsource.microsoft.com/product/web-apps/plexure.c82dc2fc-817b-487e-ae83-1658c1bc8ff2?tab=Overview)
- [AvePoint Citizen Services](https://appsource.microsoft.com/product/web-apps/avepoint.7738ac97-fd40-4ed3-aaab-327c3e0fe0b3?tab=Overview)

### <a name="industries"></a>業界

アプリに最も適している業界を選択します。 アプリが複数の業界に関連しているなら、このフィールドを空のままにします。

### <a name="categories"></a>Categories

アプリに関連するカテゴリを選択します。 最大で 2 つのカテゴリを選択します。

### <a name="app-type"></a>アプリの種類

AppSource でアプリがサポートする試用版の種類を選択します。 以下のいずれかの試用版を選択します。
- **[無料]** は、アプリが無料であることを意味します。
- **[試用版]** は、顧客が一定の期間アプリを試すことができることを意味します。
- **[Request for trial (試用のリクエスト)]** は、顧客がアプリの試用をリクエストできることを意味します。

パートナーは AppSource で 2 種類の試用版を提供できます。

- **[試用版]** オプションは **顧客主導の試用 (CLT)** とも呼ばれ、以下の種類のうちのいずれかです。 
    - SaaS 試用版
        - 顧客は、お客様またはお客様のパートナーが提供する URL にアクセスできます。 顧客は、AAD フェデレーション SSO を使用して一定期間試用できます。
        - これはマルチテナントの SaaS アプリで、ユーザーの構成/データを他のユーザーから分離します。 または、この試用では読み取り専用操作を使うサブセットの提供だけを行います。

        **例:**

        - [AFS POP Retail Execution](https://appsource.microsoft.com/product/web-apps/afs.fa9fc926-3bc3-43dd-becd-3ef41b52c10b?tab=Overview)
        - [AvePoint Citizen Services](https://appsource.microsoft.com/product/web-apps/avepoint.7738ac97-fd40-4ed3-aaab-327c3e0fe0b3?tab=Overview)。 (このアプリは、限定的なユーザー ペルソナに明確にアプローチするための、よくまとめられた試用版を提供しています)

     - 体験版
        - お客様 (またはお客様のパートナー) のソリューションまたはそのサブセットは、Azure Resource Manager テンプレートを使用してパッケージ化でき、AppSource はそれをインスタンス化できます。 AppSource はパートナー サブスクリプション内のアプリを、タイム ボックス化や、インスタンスのホット/コールド プールの維持などにより管理できます。
        - このオプションを選択する場合、役立つテンプレートとサンプル コードを活用できます。

        **例:**

        - [Neal Analytics Inventory Optimization](https://appsource.microsoft.com/product/web-apps/neal_analytics.8066ad01-1e61-40cd-bd33-9b86c65fa73a?tab=Overview&tag=CISHome)

- **[Request for trial (試用のリクエスト)]** (**パートナー主導の試用/PLT**) オプションでは、パートナーがフォローアップできるように顧客が連絡先情報フォームに入力することが必要です。 パートナーがフォローアップし、アプリのデモを見せるか、試用版を提供します。 概要については、「[AppSource 試用版チュートリアル](http://aka.ms/trialexperienceforwebapps)」ビデオを参照してください。

>[!Note]
>データによれば、**カスタマー主導の試用**は**パートナー主導の試用**に比べてリードが見つかる可能性が高くなります。


### <a name="help-link-for-your-app"></a>アプリのヘルプ リンク

アプリのヘルプ情報があるページへの URL を指定します。

### <a name="supported-countriesregions"></a>サポートされている国/地域

このフィールドには、オファーの試用版が利用できる国/地域を指定します。

![サポートされている国/地域](./media/cloud-partner-portal-publish-cortana-intelligence-app/CISScreenshot3.png)

### <a name="supported-languages"></a>サポートされている言語

アプリがサポートする言語を選択します。 このリストにない言語をアプリがサポートする場合、オファーを発行し、弊社にメールで他の言語のサポートについてお知らせください (<appsource@microsoft.com>)。

### <a name="app-version"></a>アプリのバージョン

アプリのバージョン番号を入力します。

### <a name="products-your-app-works-with"></a>アプリが対応する製品

アプリが対応する特定の製品を列挙します。 最大 3 製品を列挙できます。 製品を列挙するには、**[+] サイン** ([新規] の隣) を選択して、テキスト フィールドを作成します。 アプリが対応する製品の名前を入力します。

### <a name="hide-key"></a>非表示キー

オファーをパブリック ビューから非表示にするために、オファーのプレビュー URL と組み合わせるキーです。 パスワードではありません。 任意の英数字の文字列を入力できます。

### <a name="offer-logo-small"></a>オファーのロゴ (小)

このロゴは、アプリの検索ページに表示されます。 使用できるのは png 画像形式のみです。 画像サイズは 48 x 48 ピクセルです。

### <a name="offer-logo-large"></a>オファーのロゴ (大)

このロゴは、アプリの詳細ページに表示されます。 使用できるのは png 画像形式のみです。 画像サイズは 48 x 48 ピクセルです。

### <a name="video"></a>ビデオ

最大で 4 つのビデオをアップロードできます。 アップロードするビデオのそれぞれに、以下を実行します。
- ビデオ名を指定する
- URL を指定する (YouTube または Vimeo のみ)
- ビデオに関連付けるためのサムネイルを指定します。 サムネイルは png 画像形式を使用し、サイズは 1280 x 720 ピクセルにする必要があります。 

新しいビデオを追加するには、次のスクリーン キャプチャで示されている **[+ 新規]** を選択します。

![新しいビデオを追加する](./media/cloud-partner-portal-publish-cortana-intelligence-app/CISScreenshot4.png)

### <a name="document"></a>ドキュメント

最大で 3 つのドキュメントをアップロードできます。 各ドキュメントは、PDF ファイル形式である必要があります。 新しいドキュメントを追加する方法。

- **[+新規]** を選択します
- ドキュメント名を入力します
- **[アップロード]** を選択してドキュメントをアップロードします。

![新しいドキュメントを追加する](./media/cloud-partner-portal-publish-cortana-intelligence-app/CISScreenshot5.png)

### <a name="privacy-policy"></a>プライバシー ポリシー

アプリのプライバシー ポリシーに関する URL を入力します

### <a name="terms-of-use"></a>使用条件

アプリの使用条件を入力します。 AppSource の顧客は、アプリを試す前にこれらの条件を承諾する必要があります。

>[!Note]
>このフィールドには HTML タグを使用するコンテンツを入力できます。html コンテンツ <p\>、<h1\>、<li\> などです。 これらのタグを使用してコンテンツの書式を設定できます。 

### <a name="lead-destination"></a>リードの宛先

リードを保存する CRM システムを選択します。 

次のうちいずれかの CRM システムをご使用の場合、**[Azure テーブル]** を選択します。Salesforce、Marketo、Microsoft Dynamics CRM。 

使用する CRM システムに関する詳細については、サポートされているシステムに対応する次のリンクを選択してください。

-   [Azure テーブル](./cloud-partner-portal-lead-management-instructions-azure-table.md)
-   [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md)
-   [Microsoft Dynamics CRM](./cloud-partner-portal-lead-management-instructions-dynamics.md)
-   [Salesforce](./cloud-partner-portal-lead-management-instructions-salesforce.md)
