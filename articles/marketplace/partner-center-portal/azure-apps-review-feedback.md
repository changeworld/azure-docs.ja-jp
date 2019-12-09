---
title: 商業マーケットプレースで Azure アプリ オファーのレビュー フィードバックを処理する
description: Microsoft パートナー センターの商業マーケットプレース ポータルを使用して、Azure Marketplace、AppSource、クラウド ソリューション プロバイダー (CSP) プログラムでリスト登録または販売を行うために Azure アプリ オファーのレビュー フィードバックを処理する方法。
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/11/2019
ms.openlocfilehash: 7406a6e35929e3df5d342affbcb4b740efa0cab8
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/21/2019
ms.locfileid: "74281029"
---
# <a name="handling-review-feedback"></a>レビュー フィードバックの処理

この記事では、Microsoft Azure Marketplace レビュー チームが使用する Azure DevOps 環境にアクセスする方法について説明します。 **Microsoft のレビュー** ステップで Azure アプリケーション オファーに重大な問題が見つかった場合は、このシステムにサインインして、それらの問題に関する詳細な情報 (レビュー フィードバック) を表示できます。 これらの問題をすべて修正した後、Azure Marketplace に引き続きオファーを発行するには、オファーを再送信する必要があります。 次の図は、このフィードバック プロセスと発行プロセスの関係を示しています。

![フィードバック プロセスのレビュー](./media/review-feedback-process.png)

通常、レビューの問題はプルリクエスト (PR) と呼ばれます。 各 PR は、問題に関する詳細を含むオンラインの [Azure DevOps](https://azure.microsoft.com/services/devops/) (旧称 Visual Studio Team Services (VSTS)) 項目にリンクされています。 次の図は、レビュー中に問題が見つかった場合のパートナー センターのエクスペリエンスの例を示しています。 

![発行状態](./media/publishing-status.png)

送信に関する特定の詳細情報が含まれている PR は、[認定レポートの表示] リンクに記載されています。 複雑な状況では、レビューおよびサポート チームからお客様にメールが送信されることもあります。

## <a name="azure-devops-access"></a>Azure DevOps アクセス

パートナー センターの "開発者" ロールにアクセスできるすべてのユーザーは、フィードバックのレビューで参照されている PR 項目を表示するためのアクセス権を持ちます。

<!---
To view the PR items referenced in review feedback, publishers must first be granted proper authorization. Otherwise, new publishers receive a 401 - Not Authorized response page when trying to view PRs. To request access to this Azure DevOps repository, perform the following steps:

1. Collect the following information:
    * Your publisher name and ID
    * Offer type (Azure app), offer name, and SKU ID
    * The pull request link, for example: `https://solutiontemplates.visualstudio.com/marketplacesolutions/_git/contoso/pullrequest/<number>`. This URL can be retrieved from the notification message or the address of the 401 response page.
    * The email address(es) of the individuals from your publishing organization that you want access granted to. This list should include the owner address(es) you provided when registering as a publisher on the Cloud Partner Portal (or Partner Center??).
1. Create a support incident. In the title bar of the Cloud Partner Portal (??), select the **Help** button, then choose **Support** from the menu. Your default web browser should launch and navigate to the Microsoft new support incident page. (You may have to sign in first.)
1. Specify the **Problem type** as **marketplace onboarding** and **Category** as **Access problem**, then select **Start request**.

    ![Create an incident](./media/create-an-incident.png)

1. In **Step 1 of 2** page, supply your contact information and select **Continue**.
1. In **Step 2 of 2** page, specify an incident title (for example Request Azure DevOps access) and supply the information you collected in the first step (above). Read and accept the agreement, then select **Submit**.

If the incident creation was successful, a confirmation page is displayed. Save the confirmation information on this page for your reference. The Microsoft Support Team should reply to your access request within a few business days. --->

## <a name="reviewing-the-pull-request"></a>プルリクエストの確認

プルリクエストに記述されている問題を確認するには、次の手順を使用します。

1. [発行手順] フォームの **[Microsoft によるレビュー]** セクションで、PR リンクをクリックしてブラウザーを起動し、この PR の **[概要]** (ホーム) ページに移動します。 次の図は、Contoso のサンプル アプリ オファーの重大な問題のホーム ページの例を示しています。 このページには、Azure アプリで検出されたレビューの問題に関する有用な概要情報が含まれています。

    [![pull request のホーム ページ](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> *拡大するには画像をクリックしてください。*

1. (省略可能) ウィンドウの右側にある **[ポリシー]** セクションで、問題のメッセージをクリックして (この例では、**Policy Validation failed\(ポリシーの検証に失敗しました\)** )、問題の低レベルの詳細 (関連付けられているログ ファイルを含む) を調査します。 通常、エラーはログ ファイルの下部に表示されます。
1. ホーム ページの左側にあるメニューで、**ファイル**を選択して、このオファーの技術資産を構成するリスト ファイルを表示します。 検出された重大な問題について説明するコメントが Microsoft のレビュー担当者によって追加されているはずです。 次の例では、2 つの問題が検出されました。

    [![pull request のホーム ページ](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> *拡大するには画像をクリックしてください。*

1. 左側のツリーで各コメント ノードをクリックして、周囲のコードのコンテキスト内のコメントに移動します。 チームのプロジェクト内のソース コードを修正し、コメントで説明されている問題を修正します。

>[!Note]
>レビュー チームの Azure DevOps 環境内でオファーの技術資産を編集することはできません。 パブリッシャーの場合、これは含まれているソース コードに対して読み取り専用の環境です。 ただし、Microsoft のレビュー チームのために、コメントに対する返信を残しておくことができます。

   次の例では、パブリッシャーが最初の問題をレビューし、修正して、返信しました。

   ![最初の修正およびコメントの返信](./media/first-comment-reply.png)

## <a name="next-steps"></a>次の手順

レビュー PR に記述されている重大な問題を修正したら、[Azure アプリ オファーを再発行する](./create-new-azure-apps-offer.md#publish)必要があります。
