---
title: Azure Time Series Insights にアクセスして管理するためのセキュリティの構成 | Microsoft Docs
description: この記事では、管理アクセス ポリシーとデータ アクセス ポリシーとしてセキュリティとアクセス許可を構成し、Azure Time Series Insights を保護する方法を説明します。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/15/2017
ms.openlocfilehash: c9bddf8e20524433b31793e277efd954a5d1320e
ms.sourcegitcommit: 42405ab963df3101ee2a9b26e54240ffa689f140
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/28/2018
ms.locfileid: "47423376"
---
# <a name="grant-data-access-to-a-time-series-insights-environment-using-azure-portal"></a>Azure Portal を使用して Time Series Insights 環境にデータ アクセスを許可する

ここでは、Time Series Insights の 2 つのタイプのアクセス ポリシーについて説明します。

## <a name="video"></a>ビデオ: 

### <a name="in-this-video-we-cover-creating-and-managing-access-policies-within-time-series-insights-br"></a>このビデオでは、Time Series Insights 内でアクセス ポリシーを作成および管理する方法について説明します。 </br>

> [!VIDEO https://www.youtube.com/embed/5zTIdyHMJW8]

Time Series Insights 環境には、2 種類の独立したアクセス ポリシーがあります。

* 管理アクセス ポリシー
* データ アクセス ポリシー

どちらのポリシーでも、特定の環境に対するさまざまなアクセス許可が Azure Active Directory プリンシパル (ユーザーおよびアプリ) に付与されます。 このプリンシパル (ユーザーおよびアプリ) は、その環境が含まれているサブスクリプションに関連付けられたアクティブなディレクトリ ("Azure テナント" として知られる) に属している必要があります。

管理アクセス ポリシーは、次のような、環境の構成に関連したアクセス許可を付与します。
*   環境、イベント ソース、参照データ セットの作成と削除。
*   データ アクセス ポリシーの管理。

データ アクセス ポリシーは、データ クエリの発行、環境内での参照データの操作、環境に関連付けられた保存クエリとパースペクティブの共有を実行するためのアクセス許可を付与します。

この 2 種類のポリシーにより、環境の管理へのアクセスと環境内のデータへのアクセスを明確に分離できます。 たとえば、環境の所有者または作成者がデータ アクセスから除外されるように環境を設定することが可能です。 また、環境からのデータの読み取りを許可されているユーザーとサービスに環境の構成へのアクセスを許可しないことも可能です。

## <a name="grant-data-access"></a>データ アクセスの許可
次の手順に従って、ユーザー プリンシパルにデータ アクセスを許可します。

1. [Azure Portal](https://portal.azure.com) にサインインします。

2. Time Series Insights 環境を検索します。 **[検索]** ボックスに「**Time Series**」と入力します。 検索結果の **[Time Series Environment]\(Time Series 環境\)** を選択します。 

3. リストから Time Series Insights 環境を選択します。
   
4. **[データ アクセス ポリシー]** を選択し、**[+ 追加]** をクリックします。
  ![Time Series Insights ソースの管理 - 環境](media/data-access/getstarted-grant-data-access1.png)

5. **[ユーザーの選択]** を選択します。  ユーザー名または電子メール アドレスを検索して追加するユーザーを探します。 **[選択]** をクリックして選択内容を確定します。 

   ![Time Series Insights ソースの管理 - 追加](media/data-access/getstarted-grant-data-access2.png)

6. **[ロールの選択]** を選択します。 ユーザーに割り当てる適切なアクセス ロールを選択します。
   - ユーザーに対して、参照データの変更に加え、保存クエリとパースペクティブを環境の他のユーザーと共有することを許可する場合は **[共同作成者]** を選択します。 
   - それ以外の場合は **[閲覧者]** を選択し、環境内のデータへのクエリ実行と環境内の個人用 (共有されない) クエリの保存をユーザーに許可します。

   **[OK]** を選び、ロールの選択を確定します。

   ![Time Series Insights ソースの管理 - ユーザーの選択](media/data-access/getstarted-grant-data-access3.png)

8. **[ユーザー ロールの選択]** ページで **[OK]** を選択します。

   ![Time Series Insights ソースの管理 - ロールの選択](media/data-access/getstarted-grant-data-access4.png)

9. **[データ アクセス ポリシー]** ページには、各ユーザーのユーザーとロールが一覧表示されます。

   ![Time Series Insights ソースの管理 - 結果](media/data-access/getstarted-grant-data-access5.png)

## <a name="provide-guest-access-to-a-user-from-another-aad-tenant"></a>別の AAD テナントからゲスト アクセスをユーザーに提供する

「ゲスト」は管理ロールではありません。これは、あるテナントから別のテナントに招待されたアカウントを示す用語として使用されています。 ゲスト アカウントがテナントのディレクトリに招待された後、Access Control (IAM) ブレードを使用して TSI 環境への管理アクセス権を付与するか、Data Access Policies ブレードを使用して環境内のデータへのアクセス権を付与することで、他のアカウントと同様に、同じアクセス制御を適用できます。 AAD テナント ゲストのアクセス権の詳細については、この[ドキュメント](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator)をご覧ください。

別のテナントから AAD ユーザーに Time Series Insights 環境へのゲスト アクセスを付与するには、以下の手順を実行します。

1. [Azure Portal](https://portal.azure.com) にサインインします。

2. Time Series Insights 環境を検索します。 **[検索]** ボックスに「**Time Series**」と入力します。 検索結果の **[Time Series Environment]\(Time Series 環境\)** を選択します。

3. リストから Time Series Insights 環境を選択します。

4. **[データ アクセス ポリシー]** を選択し、**[招待]** を選択します。

    ![Time Series Insights ソースの管理 - ユーザーの詳細](media/data-access/getstarted-grant-data-access6.png)

5. 招待するユーザーの電子メールを指定します。 これは、AAD に関連付けられている電子メールである必要があります。 招待状に個人的なメッセージを含めることもできます。

    ![Time Series Insights ソースの管理 - ユーザーの選択](media/data-access/getstarted-grant-data-access7.png)

6. 画面に確認のバブルが表示されます。

    ![Time Series Insights ソースの管理 - ユーザーの確認](media/data-access/getstarted-grant-data-access8.png)

7. **[ユーザーの選択]** を選択します。 招待したゲスト ユーザーの電子メール アドレスを検索し、追加するユーザーを見つけます。 **[選択]** をクリックして選択内容を確定します。
  
    ![Time Series Insights ソースの管理 - ユーザーの確認](media/data-access/getstarted-grant-data-access9.png)

8. **[ロールの選択]** を選択します。 ゲスト ユーザーに割り当てる適切なアクセス ロールを選択します。

    * ユーザーに対して、参照データの変更に加え、保存クエリとパースペクティブを環境の他のユーザーと共有することを許可する場合は **[共同作成者]** を選択します。

    * それ以外の場合は **[閲覧者]** を選択し、環境内のデータへのクエリ実行と環境内の個人用 (共有されない) クエリの保存をユーザーに許可します。

    **[OK]** を選び、ロールの選択を確定します。

    ![Time Series Insights ソースの管理 - ロールの選択](media/data-access/getstarted-grant-data-access10.png)

9. **[ユーザー ロールの選択]** ページで **[OK]** を選択します。

10. **[データ アクセス ポリシー]** ページに、ゲスト ユーザーと、各ゲスト ユーザーのロールが一覧表示されます。

    ![Time Series Insights ソースの管理 - ロールの確認](media/data-access/getstarted-grant-data-access11.png)

11. ゲスト ユーザーは、招待された Azure テナントに用意された環境にアクセスするために、いくつかの手順を踏まなくてはなりません。 まず、先ほど送信した招待を承認する必要があります。 この招待は、ステップ 5 で招待した電子メール アドレスにメールで送信されます。 [作業開始] をクリックして、承認する必要があります。

    ![Time Series Insights ソースの管理 - ユーザーの詳細](media/data-access/getstarted-grant-data-access12.png)

12. 次に、ゲスト ユーザーは、管理者の組織に関連付けられているアクセス許可を承認する必要があります。

    ![Time Series Insights ソースの管理 - アクセス許可の承認](media/data-access/getstarted-grant-data-access13.png)

13. 招待した電子メール アドレスにゲスト ユーザーがログインすると、招待は承認され、そのユーザーは insights.azure.com に移動します。 ここで、ユーザーは画面の右上隅に表示された自分の電子メールの横にあるアバターをクリックする必要があります。 

    ![Time Series Insights ソースの管理 - アクセス許可の承認](media/data-access/getstarted-grant-data-access14.png)

14. 次に、ゲスト ユーザーはディレクトリのドロップダウン メニューから Azure テナントを選択します。 これは、ゲスト ユーザーが招待されたテナントです。 

    ![Time Series Insights ソースの管理 - アクセス許可の承認](media/data-access/getstarted-grant-data-access15.png)

15. 最後に、ゲスト ユーザーがテナントを選択すると、アクセス権を付与した Time Series Insights 環境がユーザーに表示されます。 ユーザーは、ステップ 8 で彼らに提供したロールに関連付けられたすべての能力を持つようになります。

## <a name="next-steps"></a>次の手順
* [Azure Time Series Insights 環境にイベント ハブ イベント ソースを追加する方法](time-series-insights-how-to-add-an-event-source-eventhub.md)について説明します。
* イベント ソースに[イベントを送信](time-series-insights-send-events.md)します。
* [Time Series Insights エクスプローラー](https://insights.timeseries.azure.com)で環境を表示します。
