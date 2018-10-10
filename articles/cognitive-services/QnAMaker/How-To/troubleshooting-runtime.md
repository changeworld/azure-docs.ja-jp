---
title: トラブルシューティング - QnAMaker
titlesuffix: Azure Cognitive Services
description: QnA Maker は、ユーザーの Azure アカウントでホストされるコンポーネントで構成されます。 デバッグを行うには、ユーザーは QnA Maker Azure リソースを操作するか、QnA Maker サポート チームに設定に関する追加情報を提供する必要がある場合があります。
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: ad3c01ebf37c8b544830b281144090694eeadfcd
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "47033731"
---
# <a name="qnamaker-troubleshooting"></a>QnA Maker のトラブルシューティング
QnA Maker は、ユーザーの Azure アカウントでホストされるコンポーネントで構成されます。 デバッグを行うには、ユーザーは QnA Maker Azure リソースを操作するか、QnA Maker サポート チームに設定に関する追加情報を提供する必要がある場合があります。

## <a name="how-to-get-latest-qnamaker-runtime-updates"></a>QnA Maker ランタイムの最新の更新プログラムを取得する方法
QnA Maker ランタイムは、Azure portal で [QnA Maker サービスを作成する](./set-up-qnamaker-service-azure.md)ときにデプロイされる Azure App Service の一部です。 ランタイムの更新は定期的に行われます。 最新の更新プログラムを QnA Maker の設定に適用するには、App Service を再起動する必要があります。
1. [Azure portal](https://portal.azure.com) で QnA Maker サービス (リソース グループ) に移動します。

    ![QnA Maker Azure リソース グループ](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

2. App Service をクリックして、[概要] セクションを開きます。

     ![QnA Maker App Service](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

3. App Service を再起動します。 数秒以内に完了します。 この再起動中に、エンド ユーザーはこの QnA Maker サービスにビルドされたダウンストリーム アプリケーション/ボットを使用できないことに注意してください。

    ![QnA Maker App Service の再起動](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="how-to-get-the-qnamaker-service-hostname"></a>QnA Maker サービスのホスト名を取得する方法
QnA Maker サービスのホスト名は、デバッグ目的で QnA Maker サポートや UserVoice に問い合わせる場合に役立ちます。 ホスト名は、 https://*{ホスト名}*.azurewebsites.net という形式の URL です。
    
1. [Azure portal](https://portal.azure.com) で QnA Maker サービス (リソース グループ) に移動します。

    ![QnA Maker Azure リソース グループ](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

2. App Service をクリックします。

     ![QnA Maker App Service](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

3. ホスト名 URL は [概要] セクションで確認できます。

    ![QnA Maker のホスト名](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)
    

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [QnA Maker API を使用する](./upgrade-qnamaker-service.md)
