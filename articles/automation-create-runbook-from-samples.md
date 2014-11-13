<properties urlDisplayName="Get Started with Azure Automation" pageTitle="Azure オートメーションの使用" metaKeywords="" description="Azure でオートメーション ジョブをインポートして実行する方法について説明します。" metaCanonical="" services="automation" documentationCenter="" title="Azure オートメーションの使用" authors="bwren" solutions="" manager="stevenka" editor="" />

<tags ms.service="automation" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bwren" />

# Azure オートメーションの使用

開発者は Microsoft Azure オートメーションを使用すると、クラウド環境で一般的に実行される、手動で実行時間が長く、エラーが起こりやすく、頻繁に繰り返されるタスクを自動化する手段を入手できます。Runbook を使用して、Azure 環境内でリソースを作成、監視、管理、およびデプロイすることができます。Runbook は、内部で Windows PowerShell のワークフローを使用しています。オートメーションの詳細については、[オートメーション概要ガイドに関するページ][オートメーション概要ガイドに関するページ]を参照してください。

このチュートリアルでは、Azure オートメーションにサンプルの「Hello World」Runbook をインポートし、その Runbook を実行して、出力を表示する手順について説明します。

> [WACOM.NOTE] [Azure PowerShell コマンドレット][Azure PowerShell コマンドレット]を使用して Azure の操作を自動化する方法については、「[Azure オートメーション: Azure Active Directory を使用して Azure を認証する][Azure オートメーション: Azure Active Directory を使用して Azure を認証する]」を参照してください。

## サンプル Runbook とユーティリティ Runbook

Azure オートメーション チームはさまざまな Runbook サンプルを作成し、オートメーションに関連する作業を開始できるようにしました。これらのサンプルはオートメーションの基本的な概念を取り扱っており、独自の Runbook を作成する方法を学習するのに役立ちます。

また、オートメーション チームは、より大きいオートメーション タスクの構成要素として使用できるユーティリティ Runbook も作成しました。

> [WACOM.NOTE] 小規模で、モジュール構造を採用した、再利用可能な Runbook を作成することをお勧めします。また、オートメーションに慣れた後は、一般的に使用されるシナリオで役立つ独自のユーティリティ Runbook を作成することも強くお勧めします。

オートメーション チームが作成したサンプル Runbook とユーティリティ Runbook を[スクリプト センター][スクリプト センター]で閲覧、ダウンロードしたり、[Runbook ギャラリー][Runbook ギャラリー]からそれらをインポートしたりすることができます。

## オートメーション関連のコミュニティとフィードバック

コミュニティや Microsoft の他のチームから提供された Runbook も、[スクリプト センター][1]および [Runbook ギャラリー][Runbook ギャラリー]で公開されています。

**フィードバックの送信**オートメーション Runbook ソリューションや統合モジュールを探索している場合は、スクリプト センターにスクリプトの要求を投稿することができます。オートメーションに関連する新機能が思い浮かんだ場合は、[User Voice][User Voice] に投稿してください。

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

## このチュートリアルの手順概要

1.  [オートメーション プレビューへのサインアップ][オートメーション プレビューへのサインアップ]
2.  [Runbook ギャラリーからの Runbook のインポート][Runbook ギャラリーからの Runbook のインポート]
3.  [Runbook の発行][Runbook の発行]
4.  [Runbook の開始][Runbook の開始]

## <a name="preview"></a>Azure オートメーション プレビューへのサインアップ

オートメーションの使用を開始するには、Microsoft Azure オートメーション プレビュー機能が有効になっているアクティブな Azure サブスクリプションが必要です。

-   **[プレビュー機能]** ページで **[今すぐ試す]** をクリックします。

    ![プレビューの有効化][プレビューの有効化]

## <a name="automationaccount"></a>オートメーション アカウントの作成

1.  [Azure 管理ポータル][Azure 管理ポータル]にログインします。

2.  管理ポータルで、**[オートメーション アカウントの作成]** をクリックします。

    > [WACOM.NOTE] オートメーション アカウントを既に作成した場合は、手順 4 に進むことができます。

    ![アカウントの作成][アカウントの作成]

3.  **[新しいオートメーション アカウントの追加]** ページで、アカウントの名前を入力し、チェック マークをクリックします。

    ![新しいアカウントの追加][新しいアカウントの追加]

## <a name="importrunbook"></a>Runbook ギャラリーからの Runbook のインポート

1.  **[オートメーション]** ページで、作成した新しいアカウントをクリックします。

    ![新しいアカウント][新しいアカウント]

2.  **[Runbook]** をクリックします。

    ![[Runbook] タブ][]

3.  **[新規]** \> **[Runbook]** \> **[ギャラリーから]** の順にクリックします。

    ![Runbook Gallery][Runbook Gallery]

4.  **[チュートリアル]** カテゴリを選択し、**[Hello World for Azure Automation]** を選択します。右矢印ボタンをクリックします。

    ![Import Runbook][Import Runbook]

5.  Runbook の内容を確認し、右矢印ボタンをクリックします。

    ![Runbook Definition][Runbook Definition]

6.  Runbook の詳細を確認し、チェック マーク ボタンをクリックします。

    ![Runbook Details][Runbook Details]

## <a name="publishrunbook"></a>Runbook の発行

1.  Runbook のインポートが完了したら、**[Write-HelloWorld]** をクリックします。

    ![インポートした Runbook][インポートした Runbook]

2.  **[作成者]** をクリックし、**[ドラフト]** をクリックします。

    ドラフト モードでは、Runbook の内容を変更できます。この Runbook に対して、どのような変更も加える必要はありません。

    ![作成者のドラフト][作成者のドラフト]

3.  **[発行]** をクリックして Runbook を昇格し、運用環境で使用する準備をします。

    ![Publish][Publish]

4.  Runbook を保存して発行するように求めるメッセージが表示された場合は、**[はい]** をクリックします。

    ![保存して発行するためのメッセージ][保存して発行するためのメッセージ]

## <a name="startrunbook"></a>Runbook の開始

1.  **[Write-HelloWorld]** の Runbook が開いた状態で **[開始]** をクリックします。

    ![発行済][発行済]

2.  **[Runbook パラメーターの値を指定します]**ページで、Write-HelloWorld.ps1 スクリプトの入力パラメーターとして使用される **[名前]** を入力し、チェック マークをクリックします。

    ![Runbook のパラメーター][Runbook のパラメーター]

3.  **[ジョブ]** をクリックして、開始した Runbook ジョブの状態を確認し、**[ジョブ開始]** 列にあるタイムスタンプをクリックしてジョブの概要を表示します。

    ![Runbook のステータス][Runbook のステータス]

4.  **[概要]** ページで、ジョブの概要、入力パラメーター、出力を表示できます。

    ![Runbook の概要][Runbook の概要]

# Runbook からの Azure サービスの管理

この例では、Azure サービスを管理しない簡単な Runbook を示しています。[Azure コマンドレット][Azure PowerShell コマンドレット] は Azure の認証を要求します。Azure オートメーションによる管理のために Azure サブスクリプションを構成するには、「[Azure オートメーション: Azure Active Directory を使用して Azure を認証する][Azure オートメーション: Azure Active Directory を使用して Azure を認証する]」の指示に従います。

# 関連項目

-   [オートメーションの概要][オートメーションの概要]
-   [Runbook 作成ガイド (英語)][Runbook 作成ガイド (英語)]
-   [オートメーション フォーラム][オートメーション フォーラム]
-   [Azure オートメーション: Azure Active Directory を使用して Azure を認証する][Azure オートメーション: Azure Active Directory を使用して Azure を認証する]

  [オートメーション概要ガイドに関するページ]: http://go.microsoft.com/fwlink/p/?LinkId=392861
  [Azure PowerShell コマンドレット]: http://msdn.microsoft.com/ja-jp/library/jj156055.aspx
  [Azure オートメーション: Azure Active Directory を使用して Azure を認証する]: http://azure.microsoft.com/blog/2014/08/27/azure-automation-authenticating-to-azure-using-azure-active-directory/
  [スクリプト センター]: http://go.microsoft.com/fwlink/p/?LinkId=393029
  [Runbook ギャラリー]: http://aka.ms/runbookgallery
  [1]: http://go.microsoft.com/fwlink/?LinkID=391681
  [User Voice]: http://feedback.windowsazure.com/forums/34192--general-feedback
  [オートメーション プレビューへのサインアップ]: #automationaccount
  [Runbook ギャラリーからの Runbook のインポート]: #importrunbook
  [Runbook の発行]: #publishrunbook
  [Runbook の開始]: #startrunbook
  [プレビューの有効化]: ./media/automation/automation_00_EnablePreview.png
  [Azure 管理ポータル]: http://manage.windowsazure.com
  [アカウントの作成]: ./media/automation/automation_01_CreateAccount.png
  [新しいアカウントの追加]: ./media/automation/automation_02_addnewautoacct.png
  [新しいアカウント]: ./media/automation/automation_03_NewAutoAcct.png
  [[Runbook] タブ]: ./media/automation/automation_04_RunbooksTab.png
  [Runbook Gallery]: ./media/automation/automation_05_ImportGallery.png
  [Import Runbook]: ./media/automation/automation_06_ImportRunbook.png
  [Runbook Definition]: ./media/automation/automation_07_RunbookDefinition.png
  [Runbook Details]: ./media/automation/automation_08_RunbookDetails.png
  [インポートした Runbook]: ./media/automation/automation_07_ImportedRunbook.png
  [作成者のドラフト]: ./media/automation/automation_08_AuthorDraft.png
  [Publish]: ./media/automation/automation_085_Publish.png
  [保存して発行するためのメッセージ]: ./media/automation/automation_09_SavePubPrompt.png
  [発行済]: ./media/automation/automation_10_PublishStart.png
  [Runbook のパラメーター]: ./media/automation/automation_11_RunbookParams.png
  [Runbook のステータス]: ./media/automation/automation_12_RunbookStatus.png
  [Runbook の概要]: ./media/automation/automation_13_RunbookSummary_callouts.png
  [オートメーションの概要]: http://go.microsoft.com/fwlink/p/?LinkId=392860
  [Runbook 作成ガイド (英語)]: http://go.microsoft.com/fwlink/p/?LinkID=301740
  [オートメーション フォーラム]: http://go.microsoft.com/fwlink/p/?LinkId=390561
