---
title: Azure に接続された Azure Video Analyzer for Media (旧 Video Indexer) アカウントを作成する
titleSuffix: Azure Media Services
description: Azure に接続された Azure Video Analyzer for Media (旧 Video Indexer) アカウントを作成する方法について説明します。
services: media-services
author: Juliako
manager: femila
ms.topic: article
ms.date: 01/14/2021
ms.author: juliako
ms.openlocfilehash: 28f8bb6a610e13aa0e7cf76adf52f13972215365
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110387752"
---
# <a name="create-a-video-analyzer-for-media-account"></a>メディア アカウント用の Video Analyzer を作成する

Azure Video Analyzer for Media (旧称 Video Indexer) アカウントを作成する際は、無料試用アカウント (一定分数の無料インデックス作成を利用可能) または有料オプション (クォータによる制限なし) を選択することができます。 無料試用アカウントで Video Analyzer for Media 使用する場合、Web サイト ユーザーは最大 600 分間の無料インデックス作成を利用でき、API ユーザーは最大 2,400 分間の無料インデックス作成を利用できます。 有料オプションでは、Azure サブスクリプションに接続する Video Analyzer for Media アカウントを作成します。 インデックス作成にかかった時間 (分) に対して支払います。詳細については、「[Media Services の価格](https://azure.microsoft.com/pricing/details/media-services/)」を参照してください。

この記事では、Azure サブスクリプションと Azure Media Services アカウントにリンクされた Video Analyzer for Media アカウントを作成する方法について説明します。 このトピックでは、自動 (既定) フローを使用して Azure に接続する手順について説明します。 また、手動で Azure に接続する方法 (上級) についても説明します。

"*試用版*" から "*有料版*" の Video Analyzer for Media アカウントに移行する場合、ビデオとモデルのカスタマイズをすべて新しいアカウントにコピーすることを選択できます。詳細は「[試用版アカウントからコンテンツをインポートする](#import-your-content-from-the-trial-account)」セクションにあります。

この記事では、[Azure Government への Video Analyzer for Media アカウントのリンク](#video-analyzer-for-media-in-azure-government)についても説明します。

## <a name="prerequisites-for-connecting-to-azure"></a>Azure に接続するための前提条件

* Azure サブスクリプション。

    Azure サブスクリプションがまだない場合は、[Azure 無料試用版](https://azure.microsoft.com/free/)にサインアップしてください。
* Azure Active Directory (Azure AD) ドメイン。

    Azure AD ドメインがない場合は、Azure サブスクリプションを使用して作成してください。 詳細については、[Azure AD のカスタム ドメイン名の管理](../../active-directory/enterprise-users/domains-manage.md)に関する記事を参照してください。
* **アプリケーション管理者** のロールを持つ、Azure AD ドメイン内のユーザー。 このメンバーは、Video Analyzer for Media アカウントを Azure に接続するときに使用します。

    このユーザーは、職場または学校アカウントを持つ Azure AD ユーザーである必要があります。 個人アカウント (outlook.com、live.com、hotmail.com など) は使用しないでください。

    ![すべての Azure AD ユーザー](./media/create-account/all-aad-users.png)

### <a name="additional-prerequisites-for-automatic-flow"></a>自動フローのその他の前提条件

* Azure AD ドメインのユーザーとメンバー。

    このメンバーは、Video Analyzer for Media アカウントを Azure に接続するときに使用します。

    このユーザーは、**所有者** ロール、または **共同作成者** ロールと **ユーザー アクセス管理者** ロールの両方が割り当てられた Azure サブスクリプションのメンバーである必要があります。 1 人のユーザーが 2 つのロールを持つ場合は 2 回追加できます。 共同作成者ロールで 1 回、ユーザー アクセス管理者ロールで 1 回です。 詳細については、「[Azure リソースに対するユーザーのアクセス権を表示する](../../role-based-access-control/check-access.md)」を参照してください。

    ![アクセスの制御](./media/create-account/access-control-iam.png)

### <a name="additional-prerequisites-for-manual-flow"></a>手動フローのその他の前提条件

* Azure portal を使用して EventGrid リソース プロバイダーを登録します。

    [Azure portal](https://portal.azure.com/) で **[サブスクリプション]** 、[subscription]、 **[ResourceProviders]** に移動します。

    **Microsoft.Media** と **Microsoft.EventGrid** を探します。 "登録済み" 状態でない場合は、 **[登録]** をクリックします。 登録には数分かかります。

    ![EventGrid](./media/create-account/event-grid.png)

## <a name="create-a-new-account-on-azure"></a>Azure で新しいアカウントを作成する 

> [!NOTE]
> Azure サブスクリプションが証明書ベースの多要素認証を使用する場合、必要な証明書がインストールされているデバイスで次の手順を実行することが重要です。

1. [Video Analyzer for Media](https://www.videoindexer.ai/) Web サイトに移動して、サインインします。
1. **[Create unlimited account]\(無制限アカウントの作成\)**  ボタンを選択します。

    ![新しい Video Analyzer for Media アカウントの作成](./media/create-account/create-unlimited-account.png)
1. サブスクリプション一覧が表示されたら、使用するサブスクリプションを選択します。

    ![Video Analyzer for Media の Azure への接続](./media/create-account/new-account-on-azure-subscription.png)
1. サポートされている場所から Azure リージョンを選択します。米国西部 2、北ヨーロッパ、東アジアです。
1. **[Azure Media Services アカウント]** で、次のいずれかのオプションを選択します。

    * 新しい Media Services アカウントを作成するには、 **[新規リソース グループの作成]** を選択します。 リソース グループの名前を入力します。

        新しい Azure Storage アカウントを含め、新しいアカウントが Azure のサブスクリプションに作成されます。  
    * 既存の Media Services アカウントを使用するには、 **[既存のリソースの使用]** を選択します。 アカウント一覧からアカウントを選択します。

        Media Services アカウントは Video Analyzer for Media アカウントと同じリージョンになっている必要があります。

        > [!NOTE]
        > インデックス作成時間を最小限に抑え、スループットを低くするために、Media Services アカウントの [予約ユニット](../../media-services/previous/media-services-scale-media-processing-overview.md )の種類と数を **10 個の S3 予約ユニット** に調整することを強くお勧めします。 [ポータルを使用して予約ユニットを変更する方法](../../media-services/previous/media-services-portal-scale-media-processing.md)に関する記事を参照してください。 予約ユニットは自分のアカウントに課金されます。[価格の詳細](https://azure.microsoft.com/pricing/details/media-services/#analytics)を参照してください。
    * 接続を手動で構成するには、 **[手動構成に切り替える]** リンクを選択します。

        詳細については、後述する「[Azure に手動で接続する](#connect-to-azure-manually-advanced-option) (上級者向けオプション)」を参照してください。
1. 操作が完了したら、 **[作成]** を選択します。 この操作が完了するまでに数分かかる場合があります。

    Azure への接続が完了すると、新しい Video Analyzer for Media アカウントがアカウント一覧に表示されます。

    ![新しいアカウント](./media/create-account/new-account.png)
1. Video Analyzer for Media Web アプリで動画を再生する前に、Media Services アカウントのストリーミング エンドポイントが実行されていることを確認します (停止状態の場合は [開始] をクリックします)。

> [!TIP]
> アカウントにわかりやすい表示名を付けるには、 **[設定]** にアクセスします。

## <a name="connect-to-azure-manually-advanced-option"></a>Azure に手動で接続する (上級者向けオプション)

Azure への接続に失敗する場合は、手動で接続して問題の解決を試みることができます。

> [!NOTE]
> Video Analyzer for Media アカウント、それと接続している Media Services アカウント、同じ Media Services アカウントに接続されている Azure Storage アカウントの 3 つのアカウントは、同じリージョン内に置くことを強くお勧めします。

### <a name="create-and-configure-a-media-services-account"></a>Media Services アカウントの作成と構成

1. [アカウントの作成](../../media-services/previous/media-services-portal-create-account.md)に関するページに従って、[Azure](https://portal.azure.com/) portal を使用して Azure Media Services アカウントを作成します。

     Media Services アカウントがクラシック API で作成されていることを確認します。 
 
    ![Media Services クラシック API](./media/create-account/enable-classic-api.png)


    Media Services アカウント用にストレージ アカウントを作成する場合は、アカウントの種類に **StorageV2**、レプリケーション フィールドに **geo 冗長** (GRS) を選択します。

    ![新しい AMS アカウント](./media/create-account/create-new-ams-account.png)

    > [!NOTE]
    > Media Services リソースとアカウント名を書き留めてください。 次のセクションの手順で必要になります。
1. 作成した Media Services アカウントの [予約ユニット](../../media-services/previous/media-services-scale-media-processing-overview.md )の種類と数を **10 個の S3 予約ユニット** に調整します。 [ポータルを使用して予約ユニットを変更する方法](../../media-services/previous/media-services-portal-scale-media-processing.md)に関する記事を参照してください。

    予約ユニットは自分のアカウントに課金されます。[価格の詳細](https://azure.microsoft.com/pricing/details/media-services/#analytics)を参照してください。
1. Video Analyzer for Media Web アプリでビデオを再生するには、新しい Media Services アカウントの既定の **ストリーミング エンドポイント** を起動しておく必要があります。

    新しい Media Services アカウントで **[ストリーミング エンドポイント]** を選択します。 次に、ストリーミング エンドポイントを選択し、[開始] を押します。

    ![ストリーミング エンドポイント](./media/create-account/create-ams-account-se.png)
4. Video Analyzer for Media が Media Services API を使用して認証するには、AD アプリを作成する必要があります。 次に、「[Azure ポータルで Azure AD 認証を開始する](../../media-services/previous/media-services-portal-get-started-with-aad.md)」で説明されている Azure AD 認証プロセスについて、手順を追って説明します。

    1. 新しい Media Services アカウントで **[API アクセス]** をクリックします。
    2. [サービス プリンシパルの認証方法](../../media-services/previous/media-services-portal-get-started-with-aad.md)を選択します。
    3. クライアント ID とクライアント シークレットを取得する

        **[設定]** -> **[キー]** を選択してから **[説明]** を追加し、 **[保存]** を押すと、キー値が設定されます。

        キーの有効期限が切れた場合、アカウント所有者は Video Analyzer for Media のサポートに連絡してキーを更新する必要があります。

        > [!NOTE]
        > 必ずキー値とアプリケーション ID を書き留めてください。 次のセクションの手順で必要になります。

### <a name="connect-manually"></a>手動で接続する

[Video Analyzer for Media](https://www.videoindexer.ai/) ページの **[Azure サブスクリプションで新しいアカウントを作成する]** ダイアログで、 **[手動構成に切り替える]** リンクを選択します。

このダイアログに次の情報を入力します。

|設定|Description|
|---|---|
|Video Analyzer for Media アカウント リージョン|Video Analyzer for Media アカウント リージョンの名前 パフォーマンスの向上とコストの削減を実現するために、Azure Media Services リソースと Azure Storage アカウントがあるリージョンの名前を指定するよう強くお勧めします。 |
|Azure AD テナント|Azure AD テナントの名前 (例: "contoso.onmicrosoft.com")。 テナント情報は Azure Portal から取得できます。 右上隅のサインインしているユーザーの名前にカーソルを合わせます。 **[ドメイン]** の右側に名前が表示されます。|
|サブスクリプション ID|この接続を作成先にする Azure サブスクリプション。 サブスクリプション ID は Azure portal から取得できます。 左側のウィンドウで **[すべてのサービス]** を選択し、「サブスクリプション」と検索します。 **[サブスクリプション]** を選択して、サブスクリプションの一覧から目的の ID を選択します。|
|Azure Media Services のリソース グループ名|Media Services アカウントを作成したリソース グループの名前。|
|Media Services リソース名|前のセクションで作成した Azure Media Services アカウントの名前。|
|アプリケーション ID|前のセクションで作成した (指定した Media Services アカウントのアクセス許可を持つ) Azure AD アプリケーション ID。|
|アプリケーション キー|前のセクションで作成した Azure AD アプリケーション キー。 |

### <a name="import-your-content-from-the-trial-account"></a>"*試用版*" アカウントからコンテンツをインポートする

新しいアカウントを作成する場合は、 *"試用版"* アカウントから新しいアカウントにコンテンツをインポートするオプションがあります。 **[Azure サブスクリプションで新しいアカウントを作成する]** ダイアログで " *[インポート]* " オプションをオンにする場合、メディアおよびコンテンツ モデルのカスタマイズはすべて、"*試用版*" アカウントから新しいアカウントにコピーされます。

コンテンツをインポートする機能は、前述の自動と手動の両方の方法で有効です。

> [!NOTE]
> コンテンツは、各アカウントから一度だけインポートすることができます。
>
> "*試用版*" アカウントは、Azure Government クラウドでは利用できません。

## <a name="azure-media-services-considerations"></a>Azure Media Services に関する考慮事項

Azure Media Services に関する次の考慮事項が適用されます。

* 既存の Media Services アカウントに接続する予定の場合は、Media Services アカウントがクラシック API で作成されていることをご確認ください。 
 
    ![Media Services クラシック API](./media/create-account/enable-classic-api.png)
* 既存の Media Services アカウントに接続した場合、Video Analyzer for Media で既存メディアの **[予約ユニット]** の構成は変更されません。

   予想される負荷に従って、メディアの [予約ユニット] の種類と数を調整する必要があります。 負荷が高く、ユニットや速度が十分ではない場合は、ビデオ処理でタイムアウト エラーが発生する可能性がある点に注意してください。
* 新しい Media Services アカウントに接続した場合、Video Analyzer for Media は、既定の **ストリーミング エンドポイント** を自動的に起動します。

    ![Media Services ストリーミング エンドポイント](./media/create-account/ams-streaming-endpoint.png)

    ストリーミング エンドポイントの起動には時間がかかります。 そのため、アカウントを Azure に接続してから Video Analyzer for Media Web アプリでビデオをストリーミングして視聴できるようになるまでに数分かかることがあります。
* 既存の Media Services アカウントに接続した場合、Video Analyzer for Media で既定のストリーミング エンドポイントの構成は変更されません。 実行中の **ストリーミング エンドポイント** がない場合、この Media Services アカウントから、または Video Analyzer for Media でビデオを視聴できません。
* 自動的に接続されると、Video Analyzer for Media によってメディアの **[予約ユニット]** は 10 S3 ユニットに設定されます。

    ![Media Services の予約ユニット](./media/create-account/ams-reserved-units.png)
    
## <a name="automate-creation-of-the-video-analyzer-for-media-account"></a>Video Analyzer for Media アカウントの作成を自動化する

アカウント作成の自動化は、2 つの手順から成るプロセスです。
 
1. Azure Resource Manager を使用して、Azure Media Services アカウントと Azure AD アプリケーションを作成します。

    [Media Services アカウント作成テンプレート](https://github.com/Azure-Samples/media-services-v3-arm-templates)の例を参照してください。
1. [Media Services と Azure AD アプリケーションで Create-Account](https://videoindexer.ai.azure.us/account/login?source=apim) を呼び出します。

## <a name="video-analyzer-for-media-in-azure-government"></a>Azure Government での Video Analyzer for Media

### <a name="prerequisites-for-connecting-to-azure-government"></a>Azure Government に接続するための前提条件

-   [Azure Government](../../azure-government/index.yml) の Azure サブスクリプション。
- Azure Government の Azure AD アカウント。
- 「[Azure に接続するための前提条件](#prerequisites-for-connecting-to-azure)」で前述したアクセス許可とリソースの前提条件すべて。 「[自動フローのその他の前提条件](#additional-prerequisites-for-automatic-flow)」と「[手動フローのその他の前提条件](#additional-prerequisites-for-manual-flow)」を必ずご確認ください。

### <a name="create-new-account-via-the-azure-government-portal"></a>Azure Government ポータルを使用して新しいアカウントを作成する

> [!NOTE]
> Azure Government クラウドには、Video Analyzer for Media の "*試用版*" エクスペリエンスが含まれていません。

Video Analyzer for Media ポータルを使用して有料アカウントを作成するには、次のようにします。

1. [https://resources.azure.com](https://videoindexer.ai.azure.us) に移動します 
1. Azure Government の Azure AD アカウントでログインします。
1.  自分が所有者または共同作成者である Video Analyzer for Media アカウントが Azure Government にない場合は、アカウントの作成を開始できる空のエクスペリエンスが表示されます。 

    フローの残りの部分は、上記で説明したとおりです。選択肢となるリージョンのみが、Video Analyzer for Media が使用できる Government リージョンになります 

    自分が既に Azure Government 内の既存の 1 つ以上の Video Analyzer for Media アカウントの共同作成者または管理者である場合は、そのアカウントに誘導され、前述の説明に従って、必要に応じて追加のアカウントを作成するためのフォロー手順を開始できます。
    
### <a name="create-new-account-via-the-api-on-azure-government"></a>Azure Government で API を使用して新しいアカウントを作成する

Azure Government で有料アカウントを作成するには、[Create-Paid-Account]() の手順に従います。 この API エンド ポイントには、Government クラウドのリージョンのみが含まれます。

### <a name="limitations-of-video-analyzer-for-media-on-azure-government"></a>Azure Government の Video Analyzer for Media の制限

*   Government クラウドでは、手動によるコンテンツ モデレーションを利用できません。 

    パブリック クラウドでは、コンテンツがコンテンツ モデレーションに基づいて不快であると見なされる場合に、顧客はそのコンテンツの調査を人に依頼して、その判定を取り消せる可能性があります。  
*   試用版アカウントはありません。 
* Bing の説明 - Government クラウド では、識別される有名人および名前付きエンティティの説明は表示されません。 これは UI 機能のみです。 

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルを完了したら、使用する予定がないリソースを削除します。

### <a name="delete-a-video-analyzer-for-media-account"></a>Video Analyzer for Media アカウントを削除する

Video Analyzer for Media アカウントを削除する場合は、Video Analyzer for Media Web サイトからアカウントを削除できます。 アカウントを削除するには、所有者である必要があります。

アカウントを選択し、 **[設定]**  ->  **[このアカウントを削除する]** の順にクリックします。 

アカウントは 90 日後に完全に削除されます。

## <a name="next-steps"></a>次のステップ

[API の使用](video-indexer-use-apis.md)に関するページの手順に従って、試用アカウントや Azure に接続されている Video Analyzer for Media アカウントをプログラムで操作することができます。

Azure への接続時に使用したものと同じ Azure AD ユーザーを使用する必要があります。
