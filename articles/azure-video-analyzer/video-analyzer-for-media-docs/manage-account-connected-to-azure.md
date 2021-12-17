---
title: Azure Video Analyzer for Media (旧称 Video Indexer) アカウントの管理
description: Azure に接続された Azure Video Analyzer for Media (旧 Video Indexer) アカウントを管理する方法について説明します。
ms.topic: how-to
ms.date: 01/14/2021
ms.author: juliako
ms.openlocfilehash: b5176f862f5c65c7721a59568863619a10527f8b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128573181"
---
# <a name="manage-a-video-analyzer-for-media-account-connected-to-azure"></a>Azure に接続されている Video Analyzer for Media アカウントを管理します。

この記事では、Azure サブスクリプションと Azure Media Services アカウントに接続する Azure Video Analyzer for Media (旧称 Video Indexer) アカウントを管理する方法について説明します。

> [!NOTE]
> このトピックで説明するアカウント構成の調整を行うには、Video Analyzer for Media アカウントの所有者であることが必要です。

## <a name="prerequisites"></a>前提条件

[Azure への接続](connect-to-azure.md)に関するページを参照して、自分の Video Analyzer for Media アカウントで Azure に接続します。

この記事の「[前提条件](connect-to-azure.md#prerequisites-for-connecting-to-azure)」に従い、「[考慮事項](connect-to-azure.md#azure-media-services-considerations)」を確認してください。

## <a name="examine-account-settings"></a>アカウント設定の調査

このセクションでは、Video Analyzer for Media アカウントの設定を調べます。

設定を表示するには:

1. 右上隅にあるユーザー アイコンをクリックし、 **[設定]** を選択します。

    ![Video Analyzer for Media の設定](./media/manage-account-connected-to-azure/select-settings.png)

2. **[設定]** ページで **[アカウント]** タブを選択します。

Videos Indexer アカウントが Azure に接続されている場合は、次の内容が表示されます。

* 基になる Azure Media Services アカウントの名前
* 実行中およびキューに入っているインデックス作成ジョブの数
* 割り当てられている占有ユニットの数と種類

アカウントの調整が必要な場合には、アカウント構成に関するエラーや警告が **[設定]** ページに表示されます。 メッセージには、Azure Portal 内で変更を行う必要がある正確な箇所を示すリンクが含まれています。 詳しくは、後の「[エラーと警告](#errors-and-warnings)」セクションをご覧ください。

## <a name="repair-the-connection-to-azure"></a>Azure への接続を修復する

[Video Analyzer for Media](https://www.videoindexer.ai/) ページの **[Update connection to Azure Media Services]\(Azure Media Services への接続の更新\)** ダイアログで、次の設定の値を指定するように求められます。

|設定|説明|
|---|---|
|Azure サブスクリプション ID|サブスクリプション ID は Azure portal から取得できます。 左側のウィンドウで **[すべてのサービス]** をクリックし、「サブスクリプション」を検索します。 **[サブスクリプション]** を選択して、サブスクリプションの一覧から目的の ID を選択します。|
|Azure Media Services のリソース グループ名|Media Services アカウントを作成したリソース グループの名前。|
|アプリケーション ID|この Video Analyzer for Media アカウントに対して作成した (指定された Media Services アカウントのアクセス許可を持つ) Azure AD アプリケーション ID。 <br/><br/>アプリ ID を取得するには、Azure portal に移動します。 Media Services アカウントで、アカウントを選択し、**[API アクセス]** に移動します。 **[サービス プリンシパルを使って Media Services API に接続する]**  ->  **[Azure AD アプリ]** を選択します。 関連するパラメーターをコピーします。|
|アプリケーション キー|上で指定した Media Services アカウントに関連付けられている Azure AD アプリケーション キー。 <br/><br/>アプリ キーを取得するには、Azure portal に移動します。 Media Services アカウントで、アカウントを選択し、**[API アクセス]** に移動します。 **[サービス プリンシパルを使って Media Services API に接続する]**  ->  **[アプリケーションの管理]**  ->  **[証明書とシークレット]** を選択します。 関連するパラメーターをコピーします。|

## <a name="errors-and-warnings"></a>エラーと警告

アカウントの調整が必要な場合には、アカウント構成に関するエラーや警告が **[設定]** ページに表示されます。 メッセージには、Azure Portal 内で変更を行う必要がある正確な箇所を示すリンクが含まれています。 このセクションでは、エラーおよび警告メッセージに関する詳細を提供します。

* EventGrid

    Azure Portal を使用して EventGrid リソース プロバイダーを登録する必要があります。 [Azure Portal](https://portal.azure.com/) で、 **[サブスクリプション]** > [<サブスクリプション>] > **[ResourceProviders]**  >  **[Microsoft.EventGrid]** に移動します。 **[登録済み]** 状態でない場合は、 **[登録]** を選択します。 登録には数分かかります。

* ストリーミング エンドポイント

    基になる Media Services アカウントに既定の **ストリーミング エンドポイント** があり、開始状態になっていることを確認します。 そうなっていない場合は、この Media Services アカウントから、または Video Analyzer for Media でビデオを視聴できません。

* メディア占有ユニット

    ビデオにインデックスを付けるには、Media Service リソースにメディア占有ユニットを割り当てる必要があります。 インデックス作成のパフォーマンスを最適にするには、少なくとも 10 以上の S3 占有ユニットを割り当てることをお勧めします。 料金について詳しくは、[Media Services の価格詳細](https://azure.microsoft.com/pricing/details/media-services/)ページの FAQ のセクションをご覧ください。

## <a name="next-steps"></a>次のステップ

[API の使用](video-indexer-use-apis.md)に関するページの手順に従って、試用アカウント、または Azure に接続されている Video Analyzer for Media アカウントをプログラムで操作することができます。

Azure への接続時に使用したものと同じ Azure AD ユーザーを使用します。
