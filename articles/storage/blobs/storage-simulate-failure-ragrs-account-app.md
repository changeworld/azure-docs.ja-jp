---
title: Azure で読み取りアクセス冗長ストレージにアクセスする際のエラーをシミュレートする | Microsoft Docs
description: 読み取りアクセス geo 冗長ストレージにアクセスする際のエラーをシミュレートする
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 12/23/2017
ms.author: tamram
ms.openlocfilehash: e242395df1ffb16897b73c0330f24de4857ede13
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2018
ms.locfileid: "39398451"
---
# <a name="simulate-a-failure-in-accessing-read-access-redundant-storage"></a>読み取りアクセス冗長ストレージにアクセスする際のエラーをシミュレートする

このチュートリアルは、シリーズの第 2 部です。  このチュートリアルでは、[read-access geo-redundant](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) (RA-GRS) ストレージ アカウントのプライマリ エンドポイントに対する要求の失敗を [Fiddler](#simulate-a-failure-with-fiddler) または [Static Routing](#simulate-a-failure-with-an-invalid-static-route) を使用してシミュレートし、セカンダリ エンドポイントから読み取りを行うようにアプリケーションを制御します。

![シナリオ アプリ](media/storage-simulate-failure-ragrs-account-app/scenario.png)

このチュートリアルを完了するには、前のストレージのチュートリアル「[Make your application data highly available with Azure storage][previous-tutorial]」(Azure Storage を使用してアプリケーション データを高可用にする) を完了している必要があります。

シリーズの第 2 部で学習する内容は次のとおりです。

> [!div class="checklist"]
> * アプリケーションの実行と一時停止
> * [fiddler](#simulate-a-failure-with-fiddler) または[無効な静的ルート](#simulate-a-failure-with-an-invalid-static-route)で失敗をシミュレートする 
> * プライマリ エンドポイントの復元をシミュレートする


## <a name="prerequisites"></a>前提条件

Fiddler を使用して失敗をシミュレートするには 

* [Fiddler](https://www.telerik.com/download/fiddler) をダウンロードしてインストールする

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="simulate-a-failure-with-fiddler"></a>Fiddler で失敗をシミュレートする

Fiddler で失敗をシミュレートするには、RA-GRS ストレージ アカウントのプライマリ エンドポイントに対する要求についてエラー応答を挿入して、擬似的にエラーを発生させます。

失敗とプライマリ エンドポイントの回復を Fiddler でシミュレートするには、次の手順に従います。

### <a name="launch-fiddler"></a>Fiddler を起動する

Fiddler を開き、**[Rules]\(ルール\)**、**[Customize Rules]\(ルールのカスタマイズ\)** の順に選択します。

![Fiddler のルールをカスタマイズする](media/storage-simulate-failure-ragrs-account-app/figure1.png)

Fiddler ScriptEditor が起動し、**SampleRules.js** ファイルが表示されます。 このファイルを使用して Fiddler をカスタマイズします。 `OnBeforeResponse` 関数に次のコード サンプルを貼り付けます。 新しいコードをコメント アウトして、作成したロジックがすぐに実装されないようにします。 完了したら、**[File]\(ファイル\)**、**[Save]\(保存\)** の順に選択します。

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

### <a name="start-and-pause-the-application"></a>アプリケーションの開始と一時停止

IDE またはテキスト エディターでアプリケーションを実行します。 アプリケーションでプライマリ エンドポイントから読み取りが開始されたら、コンソール ウィンドウで**任意のキー**を押してアプリケーションを一時停止します。

### <a name="simulate-failure"></a>エラーをシミュレートする

アプリケーションを一時停止した状態で、前の手順で Fiddler で保存したカスタム ルールのコメントを解除します。 このコード サンプルは、RA-GRS ストレージ アカウントに対する要求を探し、パスにイメージ名 `HelloWorld` が含まれる場合は、`503 - Service Unavailable` の応答コードを返します。

Fiddler に移動し、**[Rules]\(ルール\)** -> **[Customize Rules]\(ルールのカスタマイズ\)** を選択します。次の行のコメントを解除し、`STORAGEACCOUNTNAME` をストレージ アカウントの名前で置き換えます。 **[File]\(ファイル\)** -> **[Save]\(保存\)** を選択して変更を保存します。 

> [!NOTE]
> サンプル アプリケーションを Linux で実行する場合は、**CustomRule.js** ファイルを編集するたびに Fiddler を再起動して、カスタム ロジックを Fiddler がインストールできるようにする必要があります。 
> 
> 


```javascript
         if ((oSession.hostname == "STORAGEACCOUNTNAME.blob.core.windows.net")
         && (oSession.PathAndQuery.Contains("HelloWorld"))) {
         oSession.responseCode = 503;
         }
```

アプリケーションを再開するために**任意のキー**を押します。

アプリケーションの実行が再開されると、プライマリ エンドポイントに対する要求は失敗するようになります。 アプリケーションはプライマリ エンドポイントへの再接続を 5 回試行します。 5 回の試行というエラーしきい値を超えると、セカンダリの読み取り専用エンドポイントのイメージを要求します。 セカンダリ エンドポイントからイメージの取得が 20 回正常に完了した後に、アプリケーションはプライマリ エンドポイントへの接続を試行します。 まだプライマリ エンドポイントに到達できない場合、アプリケーションはセカンダリ エンドポイントからの読み取りを再開します。 このパターンは、前のチュートリアルで説明した[ブレーカー](https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker)です。

![カスタマイズしたルールを貼り付ける](media/storage-simulate-failure-ragrs-account-app/figure3.png)

### <a name="simulate-primary-endpoint-restoration"></a>プライマリ エンドポイントの復元をシミュレートする

前の手順で選択した Fiddler のカスタム ルールがあるので、プライマリ エンドポイントに対する要求は失敗します。 プライマリ エンドポイントが機能することをもう一度シミュレートするには、`503` エラーを挿入するロジックを削除します。

アプリケーションを一時停止するために、**任意のキー**を押します。

Fiddler に移動し、**[Rules]\(ルール\)**、**[Customize Rules]\(ルールのカスタマイズ\)** の順に選択します。既定の関数はそのままにして、`OnBeforeResponse` 関数のカスタム ロジックをコメントアウトするか削除します。 **[File]\(ファイル\)**、**[Save]\(保存\)** の順に選択して変更を保存します。

![カスタマイズしたルールを削除する](media/storage-simulate-failure-ragrs-account-app/figure5.png)

完了したら、**任意のキー**を押してアプリケーションを再開します。 読み取り回数が 999 回になるまで、アプリケーションはプライマリ エンドポイントからの読み取りを継続します。

![アプリケーションを再開する](media/storage-simulate-failure-ragrs-account-app/figure4.png)


## <a name="simulate-a-failure-with-an-invalid-static-route"></a>無効な静的ルートで失敗をシミュレートする 
[read-access geo-redundant](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) (RA-GRS) ストレージ アカウントのプライマリ エンドポイントに対するすべての要求について無効な静的ルートを作成することができます。 このチュートリアルでは、ストレージ アカウントに要求をルーティングするためのゲートウェイとしてローカル ホストを使用します。 ローカル ホストをゲートウェイとして使用すると、ストレージ アカウントのプライマリ エンドポイントに対するすべての要求がホスト内でループバックされ、その後エラーとなります。 失敗とプライマリ エンドポイントの回復を無効な静的ルートでシミュレートするには、次の手順に従います。 

### <a name="start-and-pause-the-application"></a>アプリケーションの開始と一時停止

IDE またはテキスト エディターでアプリケーションを実行します。 アプリケーションでプライマリ エンドポイントから読み取りが開始されたら、コンソール ウィンドウで**任意のキー**を押してアプリケーションを一時停止します。 

### <a name="simulate-failure"></a>エラーをシミュレートする

アプリケーションを一時停止し、Windows で管理者としてコマンド プロンプトを起動するか、Linux で root としてターミナルを実行します。 ストレージ アカウントのプライマリ エンドポイント ドメインに関する情報を取得するには、コマンド プロンプトまたはターミナルから次のコマンドを入力します。

```
nslookup STORAGEACCOUNTNAME.blob.core.windows.net
``` 
 `STORAGEACCOUNTNAME` をストレージ アカウントの名前に置き換えます。 ストレージ アカウントの IP アドレスを後で使用できるようテキスト エディターにコピーします。 ローカル ホストの IP アドレスを取得するには、Windows のコマンド プロンプトから「`ipconfig`」と入力するか、または Linux のターミナルから「`ifconfig`」と入力します。 

宛先ホストの静的ルートを追加するには、Windows のコマンド プロンプトまたは Linux のターミナルから次のコマンドを入力します。 


# <a name="linuxtablinux"></a>[Linux](#tab/linux)

  route add <destination_ip> gw <gateway_ip>

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

  route add <destination_ip> <gateway_ip>

---
 
`<destination_ip>` の部分はストレージ アカウントの IP アドレスに、`<gateway_ip>` の部分はローカル ホストの IP アドレスに置き換えてください。 アプリケーションを再開するために**任意のキー**を押します。

アプリケーションの実行が再開されると、プライマリ エンドポイントに対する要求は失敗するようになります。 アプリケーションはプライマリ エンドポイントへの再接続を 5 回試行します。 5 回の試行というエラーしきい値を超えると、セカンダリの読み取り専用エンドポイントのイメージを要求します。 セカンダリ エンドポイントからイメージの取得が 20 回正常に完了した後に、アプリケーションはプライマリ エンドポイントへの接続を試行します。 まだプライマリ エンドポイントに到達できない場合、アプリケーションはセカンダリ エンドポイントからの読み取りを再開します。 このパターンは、前のチュートリアルで説明した[ブレーカー](/azure/architecture/patterns/circuit-breaker.md)です。

### <a name="simulate-primary-endpoint-restoration"></a>プライマリ エンドポイントの復元をシミュレートする

プライマリ エンドポイントが再び機能するようになった状態をシミュレートするには、ルーティング テーブルから、プライマリ エンドポイントの静的ルートを削除します。 これにより、プライマリ エンドポイントに対する要求はすべて、デフォルト ゲートウェイを介してルーティングできるようになります。 

宛先ホスト (ストレージ アカウント) の静的ルートを削除するには、Windows のコマンド プロンプトまたは Linux のターミナルから次のコマンドを入力します。 
 
# <a name="linuxtablinux"></a>[Linux](#tab/linux)

route del <destination_ip> gw <gateway_ip>

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

route delete <destination_ip>

---

**任意のキー**を押してアプリケーションを再開します。 読み取り回数が 999 回になるまで、アプリケーションはプライマリ エンドポイントからの読み取りを継続します。

![アプリケーションを再開する](media/storage-simulate-failure-ragrs-account-app/figure4.png)


## <a name="next-steps"></a>次の手順

シリーズの第 2 部では、次のような方法でエラーをシミュレートして、読み取りアクセス geo 冗長ストレージをテストしました。

> [!div class="checklist"]
> * アプリケーションの実行と一時停止
> * [fiddler](#simulate-a-failure-with-fiddler) または[無効な静的ルート](#simulate-a-failure-with-an-invalid-static-route)で失敗をシミュレートする 
> * プライマリ エンドポイントの復元をシミュレートする

RA-GRS ストレージのしくみ (とそれに関連するリスク) の詳細については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [RA-GRS を使用した HA アプリの設計](../common/storage-designing-ha-apps-with-ragrs.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md