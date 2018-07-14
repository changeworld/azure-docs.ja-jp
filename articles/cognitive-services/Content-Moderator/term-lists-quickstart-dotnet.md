---
title: Azure Content Moderator でカスタム用語リストを使用してモデレートする | Microsoft Docs
description: Azure Content Moderator SDK for .NET を使用してカスタム用語リストをモデレートする方法
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/11/2018
ms.author: sajagtap
ms.openlocfilehash: 6da72ad070d9c3a6be38e24626dff77b52fed852
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373357"
---
# <a name="moderate-with-custom-term-lists-in-net"></a>.NET でカスタム用語リストを使用してモデレートする

Azure Content Moderator の既定のグローバルな用語リストは、ほとんどのコンテンツ モデレーションのニーズに十分対応できます。 しかし、組織に固有の用語のスクリーニングが必要になる場合があります。 たとえば、さらに詳しくレビューするために、競合他社名にタグを付けたい場合などです。 

Content Moderator SDK for .NET を使用して、Text Moderation API で使用するカスタム用語リストを作成できます。

> [!NOTE]
> **用語リストの数は 5 個まで**、各リスト内の**用語の数は 10,000 個まで**に制限されています。
>

この記事では、Content Moderator SDK for .NET を使用して次の操作をすぐに開始するために役立つ情報とコード サンプルを提供します。
- リストを作成します。
- リストに用語を追加する。
- 用語をリスト内の用語に照らしてスクリーニングする。
- リストから用語を削除する。
- リストを削除する。
- リストの情報を編集する。
- リストに加えた変更が今後のスキャンに含まれるように、インデックスを最新の情報に更新する。

この記事では、Visual Studio と C# に精通していることを前提としています。

## <a name="sign-up-for-content-moderator-services"></a>Content Moderator サービスにサインアップする

REST API や SDK を通じて Content Moderator サービスを使用するには、サブスクリプション キーが必要です。

サブスクリプション キーは、Content Moderator のダッシュボードで、**[設定]** > **[資格情報]** > **[API]** > **[Trial Ocp-Apim-Subscription-Key]** の順に選択すると表示されます。 詳細については、[概要](overview.md)に関するページを参照してください。

## <a name="create-your-visual-studio-project"></a>Visual Studio プロジェクトを作成する

1. ソリューションに新しい**コンソール アプリ (.NET Framework)** プロジェクトを追加します。

1. プロジェクトに **TermLists** と名前を付けます。 このプロジェクトをソリューションのシングル スタートアップ プロジェクトとして選択します。

1. [Content Moderator クライアント ヘルパーのクイックスタート](content-moderator-helper-quickstart-dotnet.md)で作成した **ModeratorHelper** プロジェクト アセンブリへの参照を追加します。

### <a name="install-required-packages"></a>必要なパッケージをインストールする

TermLists プロジェクト用に次の NuGet パッケージをインストールします。

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Microsoft.Rest.ClientRuntime.Azure
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>プログラムの using ステートメントを更新する

プログラムの using ステートメントを変更します。

    using System;
    using System.Threading;
    using Microsoft.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator.Models;
    using ModeratorHelper;

### <a name="add-private-properties"></a>プライベート プロパティを追加する

名前空間 TermLists、クラス Program に次のプライベート プロパティを追加します。

    /// <summary>
    /// The language of the terms in the term lists.
    /// </summary>
    private const string lang = "eng";

    /// <summary>
    /// The minimum amount of time, in milliseconds, to wait between calls
    /// to the Content Moderator APIs.
    /// </summary>
    private const int throttleRate = 3000;

    /// <summary>
    /// The number of minutes to delay after updating the search index before
    /// performing image match operations against a the list.
    /// </summary>
    private const double latencyDelay = 0.5;

## <a name="create-a-term-list"></a>用語リストを作成する

用語リストを作成するには、**ContentModeratorClient.ListManagementTermLists.Create** を使用します。 **Create** の 1 番目のパラメーターは、MIME の種類を含む文字列です。これは "application/json" にする必要があります。 詳細については、[API リファレンス](https://westus2.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)に関するページを参照してください。 2 番目のパラメーターは、新しい用語リストの名前と説明を含む **Body** オブジェクトです。

名前空間 TermLists、クラス Program に次のメソッドの定義を追加します。

> [!NOTE]
> お使いの Content Moderator のサービス キーは 1 秒ごとの要求数 (RPS) が制限されており、その上限を超えると、SDK が 429 エラー コードとともに例外をスローします。 
>
> 無料レベルのキーの RPS は 1 に制限されています。

    /// <summary>
    /// Creates a new term list.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <returns>The term list ID.</returns>
    static string CreateTermList (ContentModeratorClient client)
    {
        Console.WriteLine("Creating term list.");

        Body body = new Body("Term list name", "Term list description");
        TermList list = client.ListManagementTermLists.Create("application/json", body);
        if (false == list.Id.HasValue)
        {
            throw new Exception("TermList.Id value missing.");
        }
        else
        {
            string list_id = list.Id.Value.ToString();
            Console.WriteLine("Term list created. ID: {0}.", list_id);
            Thread.Sleep(throttleRate);
            return list_id;
        }
    }

## <a name="update-term-list-name-and-description"></a>用語リストの名前と説明を更新する

用語リストの情報を更新するには、**ContentModeratorClient.ListManagementTermLists.Update** を使用します。 **Update** の 1 番目のパラメーターは、用語リスト ID です。 2 番目のパラメーターは MIME の種類で、"application/json" にする必要があります。 詳細については、[API リファレンス](https://westus2.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f685)に関するページを参照してください。 3 番目のパラメーターは、新しい名前と説明を含む **Body** オブジェクトです。

名前空間 TermLists、クラス Program に次のメソッドの定義を追加します。

    /// <summary>
    /// Update the information for the indicated term list.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="list_id">The ID of the term list to update.</param>
    /// <param name="name">The new name for the term list.</param>
    /// <param name="description">The new description for the term list.</param>
    static void UpdateTermList (ContentModeratorClient client, string list_id, string name = null, string description = null)
    {
        Console.WriteLine("Updating information for term list with ID {0}.", list_id);
        Body body = new Body(name, description);
        client.ListManagementTermLists.Update(list_id, "application/json", body);
        Thread.Sleep(throttleRate);
    }

## <a name="add-a-term-to-a-term-list"></a>用語リストに用語を追加する

名前空間 TermLists、クラス Program に次のメソッドの定義を追加します。

    /// <summary>
    /// Add a term to the indicated term list.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="list_id">The ID of the term list to update.</param>
    /// <param name="term">The term to add to the term list.</param>
    static void AddTerm (ContentModeratorClient client, string list_id, string term)
    {
        Console.WriteLine("Adding term \"{0}\" to term list with ID {1}.", term, list_id);
        client.ListManagementTerm.AddTerm(list_id, term, lang);
        Thread.Sleep(throttleRate);
    }

## <a name="get-all-terms-in-a-term-list"></a>用語リスト内のすべての用語を取得する

名前空間 TermLists、クラス Program に次のメソッドの定義を追加します。

    /// <summary>
    /// Get all terms in the indicated term list.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="list_id">The ID of the term list from which to get all terms.</param>
    static void GetAllTerms(ContentModeratorClient client, string list_id)
    {
        Console.WriteLine("Getting terms in term list with ID {0}.", list_id);
        Terms terms = client.ListManagementTerm.GetAllTerms(list_id, lang);
        TermsData data = terms.Data;
        foreach (TermsInList term in data.Terms)
        {
            Console.WriteLine(term.Term);
        }
        Thread.Sleep(throttleRate);
    }

## <a name="add-code-to-refresh-the-search-index"></a>検索インデックスを最新の情報に更新するコードを追加する

用語リストに変更を加えた後は、次に用語リストを使用してテキストをスクリーニングしたときにその変更が含まれるように、用語リストの検索インデックスを最新の情報に更新します。 これは、デスクトップ上の検索エンジン (有効になっている場合) や Web 検索エンジンが、新しいファイルやページが含まれるように絶えずインデックスを最新の情報に更新する方法と似ています。

用語リストの検索インデックスを最新の情報に更新するには、**ContentModeratorClient.ListManagementTermLists.RefreshIndexMethod** を使用します。

名前空間 TermLists、クラス Program に次のメソッドの定義を追加します。

    /// <summary>
    /// Refresh the search index for the indicated term list.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="list_id">The ID of the term list to refresh.</param>
    static void RefreshSearchIndex (ContentModeratorClient client, string list_id)
    {
        Console.WriteLine("Refreshing search index for term list with ID {0}.", list_id);
        client.ListManagementTermLists.RefreshIndexMethod(list_id, lang);
        Thread.Sleep((int)(latencyDelay * 60 * 1000));
    }

## <a name="screen-text-using-a-term-list"></a>用語リストを使用してテキストをスクリーニングする

用語リストを使用してテキストをスクリーニングするには、**ContentModeratorClient.TextModeration.ScreenText** を使用します。これは次のパラメーターを受け取ります。

- 用語リスト内の用語の言語。
- MIME の種類 ("text/html"、"text/xml"、"text/markdown"、"text/plain" のいずれか)。
- スクリーニングするテキスト。
- ブール値。 スクリーニングの前にテキストのオートコレクトを実行するには、このフィールドを **true** に設定します。
- ブール値。 テキスト内の個人を特定できる情報 (PII) を検出するには、このフィールドを **true** に設定します。
- 用語リスト ID。

詳細については、[API リファレンス](https://westus2.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f)に関するページを参照してください。

**ScreenText** は、**Screen** オブジェクトを返します。このオブジェクトには、Content Moderator がスクリーニングで検出した用語を一覧表示する **Terms** プロパティがあります。 Content Moderator がスクリーニング時に用語を検出しなかった場合は、**Terms** プロパティの値が **null** になることに注意してください。

名前空間 TermLists、クラス Program に次のメソッドの定義を追加します。

    /// <summary>
    /// Screen the indicated text for terms in the indicated term list.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="list_id">The ID of the term list to use to screen the text.</param>
    /// <param name="text">The text to screen.</param>
    static void ScreenText (ContentModeratorClient client, string list_id, string text)
    {
        Console.WriteLine("Screening text: \"{0}\" using term list with ID {1}.", text, list_id);
        Screen screen = client.TextModeration.ScreenText(lang, "text/plain", text, false, false, list_id);
        if (null == screen.Terms)
        {
            Console.WriteLine("No terms from the term list were detected in the text.");
        }
        else
        {
            foreach (DetectedTerms term in screen.Terms)
            {
                Console.WriteLine(String.Format("Found term: \"{0}\" from list ID {1} at index {2}.", term.Term, term.ListId, term.Index));
            }
        }
        read.Sleep(throttleRate);
    }

## <a name="delete-terms-and-lists"></a>用語とリストを削除する

用語やリストを削除するのは簡単です。 SDK を使用して、次のタスクを実行できます。

- 用語を削除する。 (**ContentModeratorClient.ListManagementTerm.DeleteTerm**)
- リストを削除せずに、リスト内のすべての用語を削除する。 (**ContentModeratorClient.ListManagementTerm.DeleteAllTerms**)
- リストと、そのコンテンツをすべて削除する。 (**ContentModeratorClient.ListManagementTermLists.Delete**)

### <a name="delete-a-term"></a>用語を削除する

名前空間 TermLists、クラス Program に次のメソッドの定義を追加します。

    /// <summary>
    /// Delete a term from the indicated term list.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="list_id">The ID of the term list from which to delete the term.</param>
    /// <param name="term">The term to delete.</param>
    static void DeleteTerm (ContentModeratorClient client, string list_id, string term)
    {
        Console.WriteLine("Removed term \"{0}\" from term list with ID {1}.", term, list_id);
        client.ListManagementTerm.DeleteTerm(list_id, term, lang);
        Thread.Sleep(throttleRate);
    }

### <a name="delete-all-terms-in-a-term-list"></a>用語リスト内のすべての用語を削除する

名前空間 TermLists、クラス Program に次のメソッドの定義を追加します。

    /// <summary>
    /// Delete all terms from the indicated term list.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="list_id">The ID of the term list from which to delete all terms.</param>
    static void DeleteAllTerms (ContentModeratorClient client, string list_id)
    {
        Console.WriteLine("Removing all terms from term list with ID {0}.", list_id);
        client.ListManagementTerm.DeleteAllTerms(list_id, lang);
        Thread.Sleep(throttleRate);
    }

### <a name="delete-a-term-list"></a>用語リストを削除する

名前空間 TermLists、クラス Program に次のメソッドの定義を追加します。

    /// <summary>
    /// Delete the indicated term list.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="list_id">The ID of the term list to delete.</param>
    static void DeleteTermList (ContentModeratorClient client, string list_id)
    {
        Console.WriteLine("Deleting term list with ID {0}.", list_id);
        client.ListManagementTermLists.Delete(list_id);
        Thread.Sleep(throttleRate);
    }

## <a name="putting-it-all-together"></a>まとめ

名前空間 TermLists、クラス Program に **Main** メソッドの定義を追加します。 最後に、Program クラスと TermLists 名前空間を閉じます。

    static void Main(string[] args)
    {
        using (var client = Clients.NewClient())
        {
            string list_id = CreateTermList(client);

            UpdateTermList(client, list_id, "name", "description");
            AddTerm(client, list_id, "term1");
            AddTerm(client, list_id, "term2");

            GetAllTerms(client, list_id);

            // Always remember to refresh the search index of your list
            RefreshSearchIndex(client, list_id);

            string text = "This text contains the terms \"term1\" and \"term2\".";
            ScreenText(client, list_id, text);

            DeleteTerm(client, list_id, "term1");

            // Always remember to refresh the search index of your list
            RefreshSearchIndex(client, list_id);

            text = "This text contains the terms \"term1\" and \"term2\".";
            ScreenText(client, list_id, text);

            DeleteAllTerms(client, list_id);
            DeleteTermList(client, list_id);

            Console.WriteLine("Press ENTER to close the application.");
            Console.ReadLine();
        }
    }

## <a name="run-the-application-to-see-the-output"></a>アプリケーションを実行して出力を確認する

出力は次の行のようになりますが、データは異なる場合があります。

    Creating term list.
    Term list created. ID: 252.
    Updating information for term list with ID 252.
    
    Adding term "term1" to term list with ID 252.
    Adding term "term2" to term list with ID 252.
    
    Getting terms in term list with ID 252.
    term1
    term2
    
    Refreshing search index for term list with ID 252.
    
    Screening text: "This text contains the terms "term1" and "term2"." using term list with ID 252.
    Found term: "term1" from list ID 252 at index 32.
    Found term: "term2" from list ID 252 at index 46.
    
    Removed term "term1" from term list with ID 252.
    
    Refreshing search index for term list with ID 252.
    
    Screening text: "This text contains the terms "term1" and "term2"." using term list with ID 252.
    Found term: "term2" from list ID 252 at index 46.
    
    Removing all terms from term list with ID 252.
    Deleting term list with ID 252.
    Press ENTER to close the application.
    
## <a name="next-steps"></a>次の手順

.NET 用のこのクイック スタートや他の Content Moderator のクイック スタートの [Visual Studio ソリューションをダウンロード](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator)し、統合を開始します。
