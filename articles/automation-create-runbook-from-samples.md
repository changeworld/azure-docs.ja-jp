<properties linkid="automation-create-runbook-from-samples" urlDisplayName="Get Started with Azure Automation" pageTitle="Get Started with Azure Automation" metaKeywords="" description="Learn how to import and run an automation job in Azure." metaCanonical="" services="automation" documentationCenter="" title="Get Started with Azure Automation" authors="bwren" solutions="" manager="stevenka" editor="" />

<tags ms.service="automation" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bwren"></tags>

# Azure オートメーションの使用

開発者は Microsoft Azure オートメーションを使用すると、クラウド環境で一般的に実行される、手動で実行時間が長く、エラーが起こりやすく、頻繁に繰り返されるタスクを自動化する手段を入手できます。Runbook を使用して、Azure 環境内でリソースを作成、監視、管理、およびデプロイすることができます。Runbook は、内部で Windows PowerShell のワークフローを使用しています。オートメーションの詳細については、[オートメーション概要ガイドに関するページ][オートメーション概要ガイドに関するページ]を参照してください。

このチュートリアルでは、Azure オートメーションにサンプルの「Hello World」Runbook をインポートし、その Runbook を実行して、出力を表示する手順について説明します。

<blockquote>
<p>[WACOM.NOTE] <a href="http://msdn.microsoft.com/ja-jp/library/jj156055.aspx">Azure PowerShell コマンドレット</a>を使用して Azure の操作を自動化する方法については、「<a href="http://azure.microsoft.com/blog/2014/08/27/azure-automation-authenticating-to-azure-using-azure-active-directory/">Azure オートメーション: Azure Active Directory を使用して Azure を認証する</a>」を参照してください。</p>
</blockquote>


## サンプル Runbook とユーティリティ Runbook

Azure オートメーション チームはさまざまな Runbook サンプルを作成し、オートメーションに関連する作業を開始できるようにしました。これらのサンプルはオートメーションの基本的な概念を取り扱っており、独自の Runbook を作成する方法を学習するのに役立ちます。

また、オートメーション チームは、より大きいオートメーション タスクの構成要素として使用できるユーティリティ Runbook も作成しました。

> [WACOM.NOTE] 小規模で、モジュール構造を採用した、再利用可能な Runbook を作成することをお勧めします。また、オートメーションに慣れた後は、一般的に使用されるシナリオで役立つ独自のユーティリティ Runbook を作成することも強くお勧めします。

[スクリプト センター][スクリプト センター]で、オートメーション チームが作成したサンプル Runbook とユーティリティ Runbook を表示し、ダウンロードすることができます。

## オートメーション関連のコミュニティとフィードバック

コミュニティや Microsoft の他のチームから提供された Runbook も、[スクリプト センター][1]で公開されています。

**フィードバックの送信**オートメーション Runbook ソリューションや統合モジュールを探索している場合は、スクリプト センターにスクリプトの要求を投稿することができます。オートメーションに関連する新機能が思い浮かんだ場合は、[User Voice][User Voice] に投稿してください。

[WACOM.INCLUDE [create-account-note][create-account-note]]

## このチュートリアルの手順概要

1.  [オートメーション プレビューへのサインアップ][オートメーション プレビューへのサインアップ]
2.  [サンプル Runbook のダウンロード][サンプル Runbook のダウンロード]
3.  [サンプル Runbook の出力のインポート、実行、表示][サンプル Runbook の出力のインポート、実行、表示]

## <a name="preview"></a>Azure オートメーション プレビューへのサインアップ

オートメーションの使用を開始するには、Microsoft Azure オートメーション プレビュー機能が有効になっているアクティブな Azure サブスクリプションが必要です。

-   **[プレビュー機能]** ページで **[今すぐ試す]** をクリックします。

    ![プレビューの有効化][プレビューの有効化]

## <a name="download-sample"></a> スクリプト センターからのサンプル Runbook のダウンロード

1.  [スクリプト センター][スクリプト センター]にアクセスし、**[Hello World Azure Automation]** をクリックします。

2.  **[Download]** の横にある **[Write-HelloWorld.ps1]** というファイル名をクリックし、そのファイルをコンピューターに保存します。

## <a name="import-sample"></a>Azure オートメーションでのサンプル Runbook のインポート、実行、表示

1.  [Azure 管理ポータル][Azure 管理ポータル]にログインします。

2.  管理ポータルで、**[オートメーション アカウントの作成]** をクリックします。

    > [WACOM.NOTE] オートメーション アカウントを既に作成した場合は、手順 4 に進むことができます。

    ![アカウントの作成][アカウントの作成]

3.  **[新しいオートメーション アカウントの追加]** ページで、アカウントの名前を入力し、チェック マークをクリックします。

    ![新しいアカウントの追加][新しいアカウントの追加]

4.  **[オートメーション]** ページで、作成した新しいアカウントをクリックします。

    ![新しいアカウント][新しいアカウント]

5.  **[Runbook]** をクリックします。

    <p><img src="./media/automation/automation_04_RunbooksTab.png" alt="[Runbook] タブ" /></p></li>


6.  **[インポート]** をクリックします。

    ![Import][Import]

7.  ダウンロードした **Write-HelloWorld.ps1** スクリプトを参照し、チェック マークをクリックします。

    ![参照][参照]

8.  **[Write-HelloWorld]** をクリックします。

    ![インポートした Runbook][インポートした Runbook]

9.  **[作成者]** をクリックし、**[ドラフト]** をクリックします。この Runbook に対して、どのような変更も加える必要はありません。

    ここで、**Write-HelloWorld.ps1** の内容を参照できます。ドラフト モードでは、Runbook の内容を変更できます。

    ![作成者のドラフト][作成者のドラフト]

10. **[発行]** をクリックして Runbook を昇格し、運用環境で使用する準備をします。

    ![Publish][Publish]

11. Runbook を保存して発行するように求めるメッセージが表示された場合は、**[はい]** をクリックします。

    ![保存して発行するためのメッセージ][保存して発行するためのメッセージ]

12. **[発行済]** をクリックし、**[開始]** をクリックします。

    ![発行済][発行済]

13. **[Runbook パラメーターの値を指定します]**ページで、Write-HelloWorld.ps1 スクリプトの入力パラメーターとして使用される **[名前]** を入力し、チェック マークをクリックします。

    ![Runbook のパラメーター][Runbook のパラメーター]

14. **[ジョブ]** をクリックして、開始した Runbook ジョブの状態を確認し、**[ジョブ開始]** 列にあるタイムスタンプをクリックしてジョブの概要を表示します。

    ![Runbook のステータス][Runbook のステータス]

15. **[概要]** ページで、ジョブの概要、入力パラメーター、出力を表示できます。

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
  [1]: http://go.microsoft.com/fwlink/?LinkID=391681
  [User Voice]: http://feedback.windowsazure.com/forums/34192--general-feedback
  [create-account-note]: ../includes/create-account-note.md
  [オートメーション プレビューへのサインアップ]: #preview
  [サンプル Runbook のダウンロード]: #download-sample
  [サンプル Runbook の出力のインポート、実行、表示]: #import-sample
  [プレビューの有効化]: ./media/automation/automation_00_EnablePreview.png
  [Azure 管理ポータル]: http://manage.windowsazure.com
  [アカウントの作成]: ./media/automation/automation_01_CreateAccount.png
  [新しいアカウントの追加]: ./media/automation/automation_02_addnewautoacct.png
  [新しいアカウント]: ./media/automation/automation_03_NewAutoAcct.png

  [Import]: ./media/automation/automation_05_Import.png
  [参照]: ./media/automation/automation_06_Browse.png
  [インポートした Runbook]: ./media/automation/automation_07_ImportedRunbook.png
  [作成者のドラフト]: ./media/automation/automation_08_AuthorDraft.png
  [Publish]: ./media/automation/automation_085_Publish.png
  [保存して発行するためのメッセージ]: ./media/automation/automation_09_SavePubPrompt.png
  [Runbook のパラメーター]: ./media/automation/automation_11_RunbookParams.png
  [Runbook のステータス]: ./media/automation/automation_12_RunbookStatus.png
  [Runbook の概要]: ./media/automation/automation_13_RunbookSummary_callouts.png
  [オートメーションの概要]: http://go.microsoft.com/fwlink/p/?LinkId=392860
  [Runbook 作成ガイド (英語)]: http://go.microsoft.com/fwlink/p/?LinkID=301740
  [オートメーション フォーラム]: http://go.microsoft.com/fwlink/p/?LinkId=390561
