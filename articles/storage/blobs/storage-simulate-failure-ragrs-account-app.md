---
title: "Azure で読み取りアクセス冗長ストレージにアクセスする際のエラーをシミュレートする | Microsoft Docs"
description: "読み取りアクセス geo 冗長ストレージにアクセスする際のエラーをシミュレートする"
services: storage
documentationcenter: 
author: georgewallace
manager: jeconnoc
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 12/05/2017
ms.author: gwallace
ms.custom: mvc
ms.openlocfilehash: f709e216f9308c4405776b25ca44b0aaddd3d3f8
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/13/2017
---
# <a name="simulate-a-failure-in-accessing-read-access-redundant-storage"></a>読み取りアクセス冗長ストレージにアクセスする際のエラーをシミュレートする

このチュートリアルは、シリーズの第 2 部です。 このチュートリアルでは、Fiddler を使用して、[読み取りアクセス geo 冗長](../common/storage-redundancy.md#read-access-geo-redundant-storage) (RA-GRS) ストレージ アカウントに対する要求の失敗した応答を挿入し、エラーをシミュレートし、アプリケーションでセカンダリ エンドポイントから読み取ります。

![シナリオ アプリ](media/storage-simulate-failure-ragrs-account-app/scenario.png)

シリーズの第 2 部で学習する内容は次のとおりです。

> [!div class="checklist"]
> * アプリケーションの実行と一時停止
> * エラーをシミュレートする
> * プライマリ エンドポイントの復元をシミュレートする

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

* [Fiddler](https://www.telerik.com/download/fiddler) をダウンロードしてインストールする

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

このチュートリアルを完了するには、前のストレージのチュートリアル「[Make your application data highly available with Azure storage][previous-tutorial]」(Azure Storage を使用してアプリケーション データを高可用にする) を完了している必要があります。

## <a name="launch-fiddler"></a>Fiddler を起動する

Fiddler を開き、**[Rules]\(ルール\)**、**[Customize Rules]\(ルールのカスタマイズ\)** の順に選択します。

![Fiddler のルールをカスタマイズする](media/storage-simulate-failure-ragrs-account-app/figure1.png)

Fiddler ScriptEditor が起動し、**SampleRules.js** ファイルが表示されます。 このファイルを使用して Fiddler をカスタマイズします。 `OnBeforeResponse` 関数に次のコード サンプルを貼り付けます。 新しいコードをコメント アウトして、作成したロジックがすぐに実装されないようにします。 完了したら、**[File]\(ファイル\)**、**[Save]\(保存\)**の順に選択します。

```javascript
    /*
        // Simulate data center failure
        // After it is successfully downloading the blob, pause the code in the sample,
        // uncomment these lines of script, and save the script.
        // It will intercept the (probably successful) responses and send back a 503 error. 
        // When you're ready to stop sending back errors, comment these lines of script out again 
        //     and save the changes.

        if ((oSession.hostname == "contosoragrs.blob.core.windows.net") 
            && (oSession.PathAndQuery.Contains("HelloWorld"))) {
            oSession.responseCode = 503;  
        }
    */
```

![カスタマイズしたルールを貼り付ける](media/storage-simulate-failure-ragrs-account-app/figure2.png)

## <a name="start-and-pause-the-application"></a>アプリケーションの開始と一時停止

Visual Studio で **F5** キーを押すか **[スタート]** を選択してアプリケーションのデバッグを開始します。 アプリケーションでプライマリ エンドポイントから読み取りが開始されたら、コンソール ウィンドウで**任意のキー**を押してアプリケーションを一時停止します。

## <a name="simulate-failure"></a>エラーをシミュレートする

アプリケーションを一時停止した状態で、前の手順で Fiddler で保存したカスタム ルールのコメントを解除します。 このコード サンプルは、RA-GRS ストレージ アカウントに対する要求を探し、パスにイメージ名 `HelloWorld` が含まれる場合は、`503 - Service Unavailable` の応答コードを返します。

Fiddler に移動し、**[Rules]\(ルール\)** -> **[Customize Rules]\(ルールのカスタマイズ\)** を選択します。次の行のコメントを解除し、`STORAGEACCOUNTNAME` をストレージ アカウントの名前で置き換えます。 **[File]\(ファイル\)** -> **[Save]\(保存\)** を選択して変更を保存します。

```javascript
         if ((oSession.hostname == "STORAGEACCOUNTNAME.blob.core.windows.net")
         && (oSession.PathAndQuery.Contains("HelloWorld"))) {
         oSession.responseCode = 503;
         }
```

アプリケーションを再開するために**任意のキー**を押します。

アプリケーションの実行が再開されると、プライマリ エンドポイントに対する要求は失敗するようになります。 アプリケーションはプライマリ エンドポイントへの再接続を 5 回試行します。 5 回の試行というエラーしきい値を超えると、セカンダリの読み取り専用エンドポイントのイメージを要求します。 セカンダリ エンドポイントからイメージの取得が 20 回正常に完了した後に、アプリケーションはプライマリ エンドポイントへの接続を試行します。 まだプライマリ エンドポイントに到達できない場合、アプリケーションはセカンダリ エンドポイントからの読み取りを再開します。 このパターンは、前のチュートリアルで説明した[ブレーカー](/azure/architecture/patterns/circuit-breaker.md)です。

![カスタマイズしたルールを貼り付ける](media/storage-simulate-failure-ragrs-account-app/figure3.png)

## <a name="simulate-primary-endpoint-restoration"></a>プライマリ エンドポイントの復元をシミュレートする

前の手順で選択した Fiddler のカスタム ルールがあるので、プライマリ エンドポイントに対する要求は失敗します。 プライマリ エンドポイントが機能することをもう一度シミュレートするには、`503` エラーを挿入するロジックを削除します。

アプリケーションを一時停止するために、**任意のキー**を押します。

### <a name="remove-the-custom-rule"></a>カスタム ルールを削除する

Fiddler に移動し、**[Rules]\(ルール\)**、**[Customize Rules]\(ルールのカスタマイズ\)** の順に選択します。既定の関数はそのままにして、`OnBeforeResponse` 関数のカスタム ロジックをコメントアウトするか削除します。 **[File]\(ファイル\)**、**[Save]\(保存\)**の順に選択して変更を保存します。

![カスタマイズしたルールを削除する](media/storage-simulate-failure-ragrs-account-app/figure5.png)

完了したら、**任意のキー**を押してアプリケーションを再開します。 読み取り回数が 999 回になるまで、アプリケーションはプライマリ エンドポイントからの読み取りを継続します。

![アプリケーションを再開する](media/storage-simulate-failure-ragrs-account-app/figure4.png)

## <a name="next-steps"></a>次のステップ

シリーズの第 2 部では、次のような方法でエラーをシミュレートして、読み取りアクセス geo 冗長ストレージをテストしました。

> [!div class="checklist"]
> * アプリケーションの実行と一時停止
> * エラーをシミュレートする
> * プライマリ エンドポイントの復元をシミュレートする

事前に作成されたストレージ サンプルを確認するには、次のリンクに従ってください。

> [!div class="nextstepaction"]
> [Azure Storage のサンプル スクリプト](storage-samples-blobs-cli.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md