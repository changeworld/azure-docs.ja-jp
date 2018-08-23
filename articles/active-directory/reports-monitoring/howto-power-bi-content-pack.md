---
title: Azure Active Directory Power BI コンテンツ パックの使用方法 | Microsoft Docs
description: Azure Active Directory Power BI コンテンツ パックの使用方法について説明します。
services: active-directory
author: priyamohanram
manager: mtillman
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.component: report-monitor
ms.date: 12/06/2017
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 090e7419f80bc734ee2b1cb4f976911792046a9c
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2018
ms.locfileid: "42146265"
---
# <a name="how-to-use-the-azure-active-directory-power-bi-content-pack"></a>Azure Active Directory Power BI コンテンツ パックの使用方法

自分が管理しているユーザーが Azure Active Directory の機能をどのように採り入れ、使用しているかを把握することは、IT 管理者にとってきわめて重要です。そうすることで自社の IT インフラストラクチャと通信を計画し、使用率を高め、AAD の機能を最大限に活かすことができます。 Azure Active Directory Power BI コンテンツ パックを使用すると、データを深く掘り下げて分析できます。利用頻度の高い各種の機能に関して、そのデータをどのように利用すれば Azure Active Directory で起こっていることについての洞察を深めることができるかが見えてきます。  Azure Active Directory の API が Power BI に統合されているため、既製のコンテンツ パックをすぐにダウンロードして、Power BI が備えている多彩な視覚化機能を使い、Azure Active Directory 内のあらゆるアクティビティについての洞察を得ることができます。 そうして得た情報は、独自のダッシュボードを作成することで、社内のだれとでも簡単に共有することができます。 

このトピックでは、ご利用の環境にコンテンツ パックをインストールして使用する具体的な手順を紹介します。

## <a name="installation"></a>インストール  

**Power BI コンテンツ パックをインストールするには**

1. ご利用の Power BI アカウント (O365 と同じアカウントまたは Azure AD アカウント) で [Power BI](https://app.powerbi.com/groups/me/getdata/services) にログインします。

2. 左側のナビゲーション ウィンドウの一番下にある **[データを取得]** を選択します。

    ![Azure Active Directory Power BI コンテンツ パック](./media/howto-power-bi-content-pack/01.png)
 
3. **[サービス]** ボックスの **[取得]** をクリックします。
   
    ![Azure Active Directory Power BI コンテンツ パック](./media/howto-power-bi-content-pack/02.png)

4.  **Azure Active Directory** を検索します。

    ![Azure Active Directory Power BI コンテンツ パック](./media/howto-power-bi-content-pack/03.png)
 
5.  確認を求められたら、Azure AD のテナント ID を入力し、**[次へ]** をクリックします。

    > [!TIP] 
    > Office 365 / Azure AD テナントのテナント ID を取得するには、Azure AD ポータルにサインインして目的のディレクトリまで移動し、[**[プロパティ]** ページ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties)から **[ディレクトリ ID]** をコピーするのが簡単です。

    ![Azure Active Directory Power BI コンテンツ パック](./media/howto-power-bi-content-pack/04.png) 

6.  **[サインイン]** をクリックします。 
 
    ![Azure Active Directory Power BI コンテンツ パック](./media/howto-power-bi-content-pack/05.png) 



7.  ユーザー名とパスワードを入力し、**[サインイン]** をクリックします。
 
    ![Azure Active Directory Power BI コンテンツ パック](./media/howto-power-bi-content-pack/06.png) 

8.  同意を求めるアプリのダイアログで **[Accept]\(同意する\)** をクリックします。
 
9.  Azure Active Directory アクティビティ ログのダッシュボードが作成されたら、それをクリックします。
 
    ![Azure Active Directory Power BI コンテンツ パック](./media/howto-power-bi-content-pack/08.png) 

## <a name="what-can-i-do-with-this-content-pack"></a>このコンテンツ パックでできること

このコンテンツ パックで何ができるかを説明する前に、コンテンツ パックにあるさまざまなレポートをざっと見てみましょう。 レポート データは、**過去 30 日**までさかのぼることができます。

### <a name="reports-included-in-this-version-of-azure-active-directory-logs-content-pack"></a>このバージョンの Azure Active Directory ログ コンテンツ パックに付属するレポート

**アプリの使用と傾向に関するレポート**: 社内で使用されているアプリについて詳しい情報を得ることができます。どのアプリが最も多く使われているか、またいつ使われているかについても正確に把握することができます。 このレポートでは、最近社内に展開したアプリがどのように使われているか、またどのアプリが支持されているかについて洞察を得ることができます。 その結果、アプリが使われているのかどうかがわかれば、使用状況の改善につなげることができます。

**場所別およびユーザー別のサインイン**: Azure ID を使って行われたすべてのサインインとユーザーの身元について詳しい情報を得ることができます。 このレポートによって個々のサインインについてより深く把握し、次のような疑問を明らかにすることができます。

- ユーザーがどこからサインインしてきたか。
- サインインの回数が最も多いのはどのユーザーで、どこからサインインしているか。 
- サインインに成功したかどうか。  
 
特定の日付や場所をクリックすると、さらに詳しい情報が表示されます。

**アプリごとの一意のユーザー**: 特定のアプリを使用している一意のユーザーをすべて把握することができます。 この情報に含まれるのは、アプリケーションへのサインインに "*成功*" したユーザーだけです。

**デバイス サインイン**: 社内のユーザーが使っている OS とブラウザーの種類を把握することができます。そのユーザーに関する詳しい情報も報告されます。その例を次に示します。

- ユーザー名
- IP アドレス
- Location 
- サインインの状態 

このレポートでは、社内で使われている各種デバイスのプロファイルを把握し、実際の使用状況に基づいてデバイスのポリシーを決めることができます。

**SSPR じょうご**: パスワードのリセットが社内でどのように行われているかを把握できます。 SSPR ツールを使ったパスワード リセットが試行された回数とその中で成功した回数が表示されます。 パスワード リセットの失敗について、SSPR じょうごを使って詳しく調べ、具体的な失敗の理由を把握できます。 SSPR ツールが社内でどのように使われているかについての理解を深め、適切な意思決定を行うことができます。

## <a name="customizing-azure-ad-activity-content-pack"></a>Azure AD アクティビティ コンテンツ パックのカスタマイズ

**ビジュアルの変更**:  レポートのビジュアルは、**[レポートの編集]** をクリックして編集できるほか、必要なビジュアルを選択することができます。
 
![Azure Active Directory Power BI コンテンツ パック](./media/howto-power-bi-content-pack/09.png) 
 
![Azure Active Directory Power BI コンテンツ パック](./media/howto-power-bi-content-pack/10.png) 

**フィールドの追加**:  レポートにフィールドを追加したりレポートからフィールドを削除したりするには、追加/削除の対象となるビジュアルを選択します。 以下の例では、"サインイン状態" フィールドをテーブル ビューに追加しています。 
 
![Azure Active Directory Power BI コンテンツ パック](./media/howto-power-bi-content-pack/11.png) 

**ダッシュボードへのビジュアルのピン留め**: ダッシュボードをカスタマイズして、独自のビジュアルをレポートに追加し、それをダッシュボードにピン留めすることができます。 以下の例では、"サインイン状態" という新しいフィルターを追加してレポートに含めています。 さらに、横棒グラフのビジュアルを折れ線グラフに変更しました。この新しいビジュアルをダッシュボードにピン留めすることができます。

![Azure Active Directory Power BI コンテンツ パック](./media/howto-power-bi-content-pack/12.png) 

![Azure Active Directory Power BI コンテンツ パック](./media/howto-power-bi-content-pack/13.png) 
 

 


**ダッシュボードの共有**: 必要なコンテンツを作成したら、そのダッシュボードを社内のユーザーと共有することができます。 レポートを共有した場合、レポートの対象として選択したフィールドを他のユーザーも閲覧できるようになるので注意してください。
 
![Azure Active Directory Power BI コンテンツ パック](./media/howto-power-bi-content-pack/14.png) 



## <a name="scheduling-a-daily-refresh-of-your-power-bi-report"></a>Power BI レポートの日次更新スケジュール

Power BI レポートの日次更新をスケジュールするには、**[データセット]、[設定]、[更新のスケジュール設定]** の順に移動し、以下のように設定します。
 
![Azure Active Directory Power BI コンテンツ パック](./media/howto-power-bi-content-pack/15.png) 

## <a name="updating-to-newer-version-of-content-pack"></a>新しいバージョンのコンテンツ パックへの更新

コンテンツ パックを新しいバージョンに更新するには、次の手順に従います。

- 新しいコンテンツ パックをダウンロードし、この記事に記載した手順に従ってセットアップを行います。

- セットアップが完了したら、**[データ ソース]、[設定]、[データ ソースの資格情報]** の順に移動し、以下のように資格情報を入力し直します。

    ![Azure Active Directory Power BI コンテンツ パック](./media/howto-power-bi-content-pack/16.png) 

新しいバージョンのコンテンツ パックが正常に動作していることが確認できたら、必要に応じてコンテンツ パックに関連付けられている基になるレポートとデータセットを削除して、以前のバージョンは削除してかまいません。

## <a name="still-having-issues"></a>まだ問題がある場合 

[トラブルシューティング ガイド](troubleshoot-content-pack.md)を参照してください。 Power BI 全般のヘルプ情報については、こちらの[ヘルプ記事](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/)を参照してください。
 

## <a name="next-steps"></a>次の手順

レポートの概要については、「[Azure Active Directory レポート](overview-reports.md)」を参照してください。
