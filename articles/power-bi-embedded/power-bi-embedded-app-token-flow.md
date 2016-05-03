<properties
   pageTitle="Power BI Embedded のアプリケーション トークン フローについて"
   description="Power BI Embedded の認証と承認のためのアプリケーション トークンについて"
   services="power-bi-embedded"
   documentationCenter=""
   authors="dvana"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="03/29/2016"
   ms.author="derrickv"/>

# Power BI Embedded のアプリケーション トークン フローについて

**Power BI Embedded** サービスでは、明示的なエンドユーザー認証ではなく、**アプリケーション トークン**を使用して認証と承認を行います。**アプリケーション トークン** モデルでは、アプリケーションがエンドユーザーの認証と承認を管理します。アプリケーションは、必要に応じて**アプリケーション トークン**を作成して送信し、要求されたレポートをレンダリングするようサービスに伝えます。この設計では、アプリケーションでユーザーの認証と承認に **Azure Active Directory** を使用する必要はありません (ただし、Azure Active Directory を使用することもできます)。

**アプリケーション トークン キーのフロー**は次のとおりです。

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
- [What is Microsoft Power BI Embedded (Microsoft Power BI Embedded とは)](power-bi-embedded-what-is-power-bi-embedded.md)
- [Microsoft Power BI Embedded の一般的なシナリオ](power-bi-embedded-scenarios.md)
- [Get started with Microsoft Power BI Embedded Preview (Microsoft Power BI Embedded プレビューを使ってみる)](power-bi-embedded-get-started.md)

<!---HONumber=AcomDC_0420_2016-->