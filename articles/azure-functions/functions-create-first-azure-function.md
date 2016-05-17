<properties
   pageTitle="初めての Azure 関数の作成 | Microsoft Azure"
   description="初めての Azure 関数、つまりサーバー不要のアプリケーションを 2 分以内に作成します。"
   services="functions"
   documentationCenter="na"
   authors="ggailey777"
   manager="erikre"
   editor=""
   tags=""
   />

<tags
   ms.service="functions"
   ms.devlang="multiple"
   ms.topic="hero-article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="05/05/2016"
   ms.author="glenga"/>

# 初めての Azure 関数の作成

##概要
Azure Functions は、イベント駆動型のオンデマンドのコンピューティング エクスペリエンスを提供します。これは、他の Azure サービス、SaaS 製品、オンプレミス システムで発生するイベントによってトリガーされるコードを実装する機能により、既存の Azure アプリケーション プラットフォームを拡張するものです。Azure Functions を利用すれば、アプリケーションを必要に応じてスケールでき、消費したリソースに対して料金を支払うだけで済みます。Azure Functions では、スケジュールやトリガーが設定されたコード ユニットを、さまざまなプログラミング言語で作成して実装できます。Azure Functions の詳細については、「[Azure Functions の概要](functions-overview.md)」を参照してください。

このトピックでは、Azure Functions ポータルで Azure Functions のクイック スタートを使用し、HTTP トリガーによって呼び出される簡単な "hello world" Node.js 関数を作成する方法について説明します。また、ポータルでこれらの手順を実行する方法についての短いビデオも視聴できます。

## ビデオを見る

次のビデオでは、このチュートリアルの基本的な手順を実行する方法について説明します。

[AZURE.VIDEO create-your-first-azure-function-simple]

##クイック スタートから関数を作成する

関数アプリは Azure での関数の実行をホストします。次の手順に従って、新しい関数アプリと新しい関数を作成してください。初めて関数を作成する際は、その前にアクティブな Azure アカウントを用意する必要があります。Azure アカウントがない場合は、[無料アカウントを利用できます](https://azure.microsoft.com/free/)。

1. [Azure Functions ポータル](https://functions.azure.com/signin)に移動し、Azure アカウントでサインインします。

2. 新しい関数アプリ用に一意の **[Name]** (名前) を入力するか、生成された名前をそのまま使用し、希望する **[Region]** (リージョン) を選択して **[Create + get started]** (作成 + 開始) をクリックします。

3. **[Quickstart]** (クイック スタート) タブで、**[WebHook + API]**、**[Create a function]** (関数を作成する) の順にクリックします。新しい定義済みの Node.js 関数が作成されます。

4. (オプション) クイック スタートのこの時点で、ポータルの Azure Functions 機能のクイック ツアーを開始するかどうか選択することができます。ツアーを完了するかスキップすると、HTTP トリガーを使用して新しい関数をテストすることができます。

##関数をテストする

Azure Functions クイック スタートには機能コードが含まれているため、すぐに新しい関数をテストできます。

1. **[Develop]** (開発) タブで **[Code]** (コード) ウィンドウを確認すると、この Node.js コードでは、*name* 値を含む HTTP 要求をメッセージ本文またはクエリ文字列で渡す必要があることがわかります。関数を実行すると、応答メッセージにこの値が返されます。

2. **[Request body]** (要求本文) ボックスまで下へスクロールして、*name* プロパティを自分の名前に変更し、**[Run]** (実行) をクリックします。テスト HTTP 要求によって実行がトリガーされ、ストリーミング ログに情報が書き込まれて、"hello" という応答が **[Output]** (出力) に表示されます。

3. 同じ関数の実行を別のブラウザー ウィンドウやタブからトリガーするには、**[Develop]** (開発) タブから **[Function URL]** (関数の URL) の値をコピーしてブラウザーのアドレス バーに貼り付け、クエリ文字列の値 `&name=yourname` を追加して Enter キーを押します。同じ情報がログに書き込まれて、ブラウザーに "hello" という応答が前と同じように表示されます。

##次のステップ

このクイック スタートでは、基本的な HTTP トリガーによる関数を非常に簡単な方法で実行します。Azure Functions の機能をアプリで活用する方法の詳細については、以下のトピックを参照してください。

+ [Azure Functions 開発者向けリファレンス](functions-reference.md) 関数のコーディングとトリガーおよびバインドの定義に関するプログラマ向けリファレンスです。
+ [Azure Functions のテスト](functions-test-a-function.md) 関数をテストするための各種ツールと手法について説明します。
+ [Azure Functions のスケーリング方法](functions-scale.md) Azure Functions で利用できるサービス プラン (動的サービス プランを含む) と、適切なプランを選択する方法について説明します。 
+ [Azure App Service とは](../app-service/app-service-value-prop-what-is.md) Azure Functions では、デプロイ、環境変数、診断などの主要な機能に Azure App Service プラットフォームを活用しています。 

[AZURE.INCLUDE [概要のメモ](../../includes/functions-get-help.md)]

<!---HONumber=AcomDC_0511_2016-->