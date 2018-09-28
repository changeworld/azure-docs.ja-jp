---
title: Azure Portal で Video Indexer アカウントを作成する
titlesuffix: Azure Cognitive Services
description: この記事では、Azure Portal で Video Indexer アカウントを作成する方法について説明します。
services: cognitive services
author: juliako
manager: cgronlun
ms.service: cognitive-services
ms.component: video-indexer
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: juliako
ms.openlocfilehash: 8b8aa3e2f7a461c13cc73270863498283f02f740
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2018
ms.locfileid: "45983151"
---
# <a name="create-a-video-indexer-account-connected-to-azure"></a>Azure に接続された Video Indexer アカウントを作成する

Video Indexer アカウントを作成する場合、無料試用アカウント (一定分数の無料インデックス作成を利用可能) または有料オプション (クォータによる制限がありません) を選択できます。 無料試用アカウントで Video Indexer 使用すると、Web サイト ユーザーは最大 600 分間の無料インデックス作成、API ユーザーは最大 2,400 分間の無料インデックス作成を利用できます。 有料オプションでは、Azure サブスクリプションと Azure Media Services アカウントに接続する Video Indexer アカウントを作成します。 Media アカウント関連の料金と同様に、インデックス作成時間 (分単位) の料金がかかります。 

この記事では、Azure サブスクリプションと Azure Media Services アカウントにリンクされた Video Indexer アカウントを作成する方法について説明します。 

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 

    Azure サブスクリプションがまだない場合は、[Azure 無料試用版](https://azure.microsoft.com/free/)にサインアップしてください。

* Azure Active Directory (AD) ドメイン。 

    Azure AD ドメインがない場合は、Azure サブスクリプションを使用して作成してください。 詳細については、「[Azure Active Directory のカスタム ドメイン名の管理](../../active-directory/users-groups-roles/domains-manage.md)」を参照してください。

* Azure AD ドメインのユーザーとメンバー。 このメンバーは、Video Indexer アカウントを Azure に接続するときに使用します。

    このユーザーは次の基準を満たしている必要があります。

    * outlook.com、live.com、hotmail.com などの個人用アカウントではなく、職場または学校アカウントを持つ Azure AD ユーザーであること。
        
        ![すべての AAD ユーザー](./media/create-account/all-aad-users.png)

    *  所有者ロール、または共同作成者ロールとユーザー アクセス管理者ロールの両方が割り当てられた Azure サブスクリプションのメンバーであること。 1 人のユーザーが 2 つのロールを持つ場合は 2 回追加できます。 共同作成者ロールで 1 回、ユーザー アクセス管理者ロールで 1 回です。

        ![アクセスの制御](./media/create-account/access-control-iam.png)

* Azure portal を使用して EventGrid リソース プロバイダーを登録します。

    [Azure Portal](https://portal.azure.com/) で、**[サブスクリプション]** > [<サブスクリプション>] > **[ResourceProviders]** > **[Microsoft.EventGrid]** に移動します。 "登録済み" 状態でない場合は、**[登録]** をクリックします。 登録には数分かかります。 

    ![EventGrid](./media/create-account/event-grid.png)

## <a name="connect-to-azure"></a>Azure への接続

1. [Video Indexer](https://www.videoindexer.ai/) Web サイトに移動してサインインします。

2. **[Azure への接続]** ボタンをクリックします。

    ![Azure への接続](./media/create-account/connect-to-azure.png)

3. サブスクリプション一覧が表示されたら、使用するサブスクリプションを選択します。 

    ![Video Indexer を Azure に接続する](./media/create-account/connect-vi-to-azure-subscription.png)

4. サポートされている場所 (米国西部 2、北ヨーロッパ、東アジア) から Azure リージョンを選択します。
5. **[Azure Media Services アカウント]** で、次のいずれかのオプションを選択します。

    * 新しい Media Services アカウントを作成するには、**[新規リソース グループの作成]** を選択します。 リソース グループの名前を入力します。

        新しい Azure Storage アカウントを含め、新しいアカウントが Azure のサブスクリプションに作成されます。 新しい Media Services アカウントには、1 つのストリーミング エンドポイントと 10 個の S3 予約ユニットという既定の初期構成があります。
    * 既存の Media Services アカウントを使用するには、**[既存のリソースの使用]** を選択します。 アカウント一覧からアカウントを選択します。

        Media Services アカウントのリージョンは Video Indexer アカウントと同じである必要があります。 インデックス作成時間を最小限に抑え、スループットを低くするには、Media Services アカウントの予約ユニットの種類と数を **10 個の S3 予約ユニット**に調整します。
    * 接続を手動で設定するには、**[手動構成に切り替える]** をクリックします。 
    
        なんらかの理由により自動オプションが完了しないとき、またはお客様の設定と構成が一般的なケースと異なるとき、接続を手動で構成したい場合があります。また、設定を完全に可視化して制御したい場合があります。 
        
        **[Video Indexer を Azure サブスクリプションと接続する]** で、次の情報を入力します。

        |Setting|説明|
        |---|---|
        |Video Indexer アカウントのリージョン|Video Indexer アカウントのリージョンの名前。 パフォーマンスの向上とコストの削減を実現するために、Azure Media Services リソースと Azure ストレージ アカウントがあるリージョンの名前を指定するよう強くお勧めします。 |
        |Azure Active Directory (AAD) テナント|Azure AD テナントの名前 (例: "contoso.onmicrosoft.com")。 テナント情報は Azure Portal から取得できます。 右上隅のサインインしているユーザーの名前にカーソルを合わせます。|
        |サブスクリプション ID|この接続を作成先にする Azure サブスクリプション。 サブスクリプション ID は Azure portal から取得できます。 左側のウィンドウで **[すべてのサービス]** をクリックし、「サブスクリプション」と検索します。 **[サブスクリプション]** を選択して、サブスクリプションの一覧から目的の ID を選択します。|
        |Azure Media Services のリソース グループ名|Media Services アカウントが存在するリソース グループの名前。|
        |Media Services リソース名|Azure Media Services リソースの名前。|
        |アプリケーション ID|指定済みの Media Services アカウントのアクセス許可を備えた Azure AD アプリケーション ID。 詳細については、[サービス プリンシパルの認証の使用](../../media-services/previous/media-services-portal-get-started-with-aad.md#service-principal-authentication)に関するページを参照してください。|
        |アプリケーション キー|詳細については、[サービス プリンシパルの認証の使用](../../media-services/previous/media-services-portal-get-started-with-aad.md#service-principal-authentication)に関するページを参照してください。|

6. 完了したら、**[接続]** を選択します。 この操作が完了するまでに数分かかる場合があります。 

    Azure への接続が完了すると、新しい Video Indexer アカウントがアカウント一覧に表示されます。

    ![新しいアカウント](./media/create-account/new-account.png)

7. 新しいアカウントを表示します。 

    ![Video Indexer アカウント](./media/create-account/vi-account.png)

## <a name="considerations"></a>考慮事項

Azure Media Services に関する次の考慮事項が適用されます。

* 新しい Media Services アカウントに接続すると、Azure サブスクリプションに新しいリソース グループ、Media Services アカウント、ストレージ アカウントが表示されます。
* 新しい Media Services アカウントに接続した場合、Video Indexer によってメディアの **[予約ユニット]** は 10 S3 ユニットに設定されます。

    ![Media Services の予約ユニット](./media/create-account/ams-reserved-units.png)

* 既存の Media Services アカウントに接続した場合、Video Indexer で既存メディアの **[予約ユニット]** の構成は変更されません。

    予想される負荷に従って、メディアの **[予約ユニット]** の種類と数を調整する必要があります。 負荷が高く、ユニットや速度が十分ではない場合は、ビデオ処理でタイムアウト エラーが発生する可能性がある点に注意してください。

* 新しい Media Services アカウントに接続した場合、Video Indexer は、既定の**ストリーミング エンドポイント**を自動的に起動します。

    ![Media Services ストリーミング エンドポイント](./media/create-account/ams-streaming-endpoint.png)

* 既存の Media Services アカウントに接続した場合、Video Indexer で既定のストリーミング エンドポイントの構成は変更されません。 実行中の**ストリーミング エンドポイント**がない場合、この Media Services アカウントから、または Video Indexer でビデオを視聴できません。

## <a name="next-steps"></a>次の手順

試用アカウント、Azure に接続されている Video Indexer アカウント、またその両方とプログラムでやり取りするには、[API の使用](video-indexer-use-apis.md)に関するページの手順を参照してください。

Azure への接続時に使用したものと同じ Azure AD ユーザーを使用する必要があります。


