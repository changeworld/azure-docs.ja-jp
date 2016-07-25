<properties
   pageTitle="Power BI Embedded での認証と承認"
   description="Power BI Embedded での認証と承認"
   services="power-bi-embedded"
   documentationCenter=""
   authors="minewiskan"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="07/01/2016"
   ms.author="owend"/>

# Power BI Embedded での認証と承認

Power BI Embedded サービスでは、明示的なエンドユーザー認証ではなく、**キー**と**アプリケーション トークン**を使用して認証と承認を行います。このモデルでは、アプリケーションがエンドユーザーの認証と承認を管理します。アプリケーションは、必要に応じてアプリケーション トークンを作成して送信し、要求されたレポートをレンダリングするようサービスに伝えます。この設計では、アプリケーションでユーザーの認証と承認に Azure Active Directory を使用する必要はありません (ただし、Azure Active Directory を使用することもできます)。

## 2 つの認証方法

**キー** - キーは、すべての Power BI Embedded REST API 呼び出しに使用できます。キーを取得するには、**Azure ポータル**で **[すべての設定]**、**[アクセス キー]** の順にクリックします。キーは常にパスワードと同様に扱います。これらのキーは、特定のワークスペース コレクションで REST API 呼び出しを行うアクセス許可を持ちます。

REST 呼び出しでキーを使用するには、次の Authorization ヘッダーを追加します。

    Authorization: AppKey {your key}

**アプリケーション トークン** - アプリケーション トークンは、すべての組み込み要求に使用されます。アプリケーション トークンはクライアント側で実行されるように設計されているため、1 つのレポートに制限されます。アプリケーション トークンには有効期限を設定するのがベスト プラクティスです。

アプリケーション トークンは、いずれかのキーで署名された JWT (JSON Web トークン) です。

アプリケーション トークンには、次の要求を含めることができます。

| 要求 | 説明 |
|--------------|------------|
| **ver** | アプリケーション トークンのバージョン。現在のバージョンは 1.0.0 です。 |
| **aud** | トークンの対象となる受信者。Power BI Embedded では、"https://analysis.windows.net/powerbi/api" を使用します。 |
| **iss** | トークンを発行したアプリケーションを示す文字列。 |
| **type** | 作成されるアプリケーション トークンの種類。現在サポートされている種類は **embed** だけです。 |
| **wcn** | トークンの発行対象であるワークスペース コレクション名。 |
| **wid** | トークンの発行対象であるワークスペース ID。 |
| **rid** | トークンの発行対象であるレポート ID。 |
| **username** (省略可能) | RLS で使用されます。これは、RLS のルールを適用するときにユーザーを特定するのに役立つ文字列です。 |
| **roles** (省略可能) | 行レベルのセキュリティのルールを適用するときに選択したロールを含む文字列。複数のロールを渡す場合は、文字列配列として渡す必要があります。 |
| **exp** (省略可能) | トークンの有効期限を示します。これらは Unix タイムスタンプとして渡す必要があります。 |
| **nbf** (省略可能) | トークンの有効期間の開始時刻を示します。これらは Unix タイムスタンプとして渡す必要があります。 |

サンプル アプリケーション トークンは次のようになります。

![](media\power-bi-embedded-app-token-flow\power-bi-embedded-app-token-flow-sample-coded.png)


これをデコードすると次のようになります。

![](media\power-bi-embedded-app-token-flow\power-bi-embedded-app-token-flow-sample-decoded.png)


## フローのしくみ

1. アプリケーションに API キーをコピーします。このキーは **Azure ポータル**で取得できます。

    ![](media\powerbi-embedded-get-started-sample\azure-portal.png)

2. トークンが要求をアサートします。トークンには有効期限があります。

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-2.png)

3. トークンが API アクセス キーで署名されます。

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-3.png)

4. ユーザーがレポートの表示を要求します。

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-4.png)

5.	トークンが API アクセス キーで検証されます。

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-5.png)

6.	Power BI Embedded がユーザーにレポートを送信します。

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-6.png)

**Power BI Embedded** がユーザーにレポートを送信したら、ユーザーはカスタム アプリケーションでレポートを表示できます。たとえば、[Analyzing Sales Data PBIX サンプル](http://download.microsoft.com/download/1/4/E/14EDED28-6C58-4055-A65C-23B4DA81C4DE/Analyzing_Sales_Data.pbix)をインポートした場合、サンプル Web アプリが次のように表示されます。

![](media\powerbi-embedded-get-started-sample\sample-web-app.png)

## 関連項目
- [Get started with Microsoft Power BI Embedded sample (Microsoft Power BI Embedded のサンプルを使ってみる)](power-bi-embedded-get-started-sample.md)
- [Common Microsoft Power BI Embedded scenarios (Microsoft Power BI Embedded の一般的なシナリオ)](power-bi-embedded-scenarios.md)
- [Microsoft Power BI Embedded の概要](power-bi-embedded-get-started.md)

<!---HONumber=AcomDC_0713_2016-->