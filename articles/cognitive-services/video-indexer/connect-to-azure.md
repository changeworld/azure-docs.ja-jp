---
title: Azure に接続された Video Indexer アカウントを作成する | Microsoft Docs
description: この記事では、Azure に接続された Video Indexer アカウントを作成する方法について説明します。
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 08/05/2018
ms.author: juliako
ms.openlocfilehash: 66ba79926a949371cb2280de408835862dd1a099
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2018
ms.locfileid: "41929861"
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

    Azure portal で、**[サブスクリプション]** > [<サブスクリプション>] > **[ResourceProviders]** > **[Microsoft.EventGrid]** に移動します。 "登録済み" 状態でない場合は、**[登録]** をクリックします。 登録には数分かかります。 

    ![EventGrid](./media/create-account/event-grid.png)

## <a name="connect-to-azure"></a>Azure への接続

1. そのユーザーでサインインし、**[Azure への接続]** ボタンをクリックします。

    ![Azure への接続](./media/create-account/connect-to-azure.png)

2. サブスクリプション一覧が表示されたら、使用するサブスクリプションを選択します。 

    ![Video Indexer を Azure に接続する](./media/create-account/connect-vi-to-azure-subscription.png)

3. サポートされている場所 (米国西部 2、北ヨーロッパ、東アジア) から Azure リージョンを選択します。
4. **[Azure Media Services アカウント]** で、次のいずれかのオプションを選択します。

    * 新しい Media Services アカウントを作成するには、**[新規リソース グループの作成]** を選択します。 リソース グループの名前を入力します。

        新しい Azure Storage アカウントを含め、新しいアカウントが Azure のサブスクリプションに作成されます。 新しい Media Services アカウントには、1 つのストリーミング エンドポイントと 10 個の S3 予約ユニットという既定の初期構成があります。
    * 既存の Media Services アカウントを使用するには、**[既存のリソースの使用]** を選択します。 アカウント一覧からアカウントを選択します。

        Media Services アカウントのリージョンは Video Indexer アカウントと同じである必要があります。 インデックス作成時間を最小限に抑え、スループットを低くするには、Media Services アカウントの予約ユニットの種類と数を **10 個の S3 予約ユニット**に調整します。
    * 手動で接続を設定するには、**[手動構成に切り替える]** リンクをクリックし、必要な情報を入力します。

    ![Video Indexer を Azure に接続する](./media/create-account/connect-vi-to-azure-subscription-2.png)

5. 完了したら、**[接続]** を選択します。 この操作が完了するまでに数分かかる場合があります。 

    Azure への接続が完了すると、新しい Video Indexer アカウントがアカウント一覧に表示されます。

    ![新しいアカウント](./media/create-account/new-account.png)

6. 新しいアカウントを表示します。 

    ![Video Indexer アカウント](./media/create-account/vi-account.png)

## <a name="considerations"></a>考慮事項

Azure Media Services に関する次の考慮事項が適用されます。

* 新しい Media Services アカウントに接続すると、Azure サブスクリプションに新しいリソース グループ、Media Services アカウント、ストレージ アカウントが表示されます。
* 新しい Media Services アカウントに接続した場合、Video Indexer によってメディアの **[予約ユニット]** は 10 S3 ユニットに設定されます。

    ![Media Services の予約ユニット](./media/create-account/ams-reserved-units.png)

* 既存の Media Services アカウントに接続した場合、Video Indexer で既存メディアの **[予約ユニット]** の構成は変更されません。

    予想される負荷に従って、メディアの **[予約ユニット]** の種類と数を調整する必要があります。 負荷が高く、ユニットや速度が十分ではない場合は、ビデオ処理でタイムアウト エラーが発生する可能性がある点に注意してください。

* 新しい Media Services アカウントに接続した場合、Video Indexer は**ストリーミング エンドポイント**を自動的に起動します。

    ![Media Services ストリーミング エンドポイント](./media/create-account/ams-streaming-endpoint.png)

* 既存の Media Services アカウントに接続した場合、Video Indexer でストリーミング エンドポイントの構成は変更されません。 実行中の**ストリーミング エンドポイント**がない場合、この Media Services アカウントから、または Video Indexer でビデオを視聴できません。

## <a name="use-video-indexer-apis-v2"></a>Video Indexer API v2 の使用

試用アカウント、Azure に接続されている Video Indexer アカウント、またその両方とプログラムでやり取りするには、[API の使用](video-indexer-use-apis.md)に関するページの手順を参照してください。

Azure への接続時に使用したものと同じ Azure AD ユーザーを使用する必要があります。

## <a name="next-steps"></a>次の手順

[出力 JSON の詳細を調べる](video-indexer-output-json-v2.md)

