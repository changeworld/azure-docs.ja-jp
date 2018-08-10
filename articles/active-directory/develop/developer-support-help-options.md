---
title: Azure ID 開発者向けのサポート オプションとヘルプ オプション | Microsoft Docs
description: Microsoft Azure ID (Azure Active Directory や MSA) と連携するアプリケーションを作成するときに直面する、開発関連の疑問や問題に関するヘルプやサポートを入手する方法について説明します
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/27/2017
ms.author: celested
ms.reviewer: andret
ms.custom: aaddev
ms.openlocfilehash: 9b2ccce4ef6fad4f7710ba2092d67ac5f4e73b71
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2018
ms.locfileid: "39601266"
---
# <a name="support-and-help-options-for-developers"></a>開発者向けのサポート オプションとヘルプ オプション 

Azure Active Directory、Microsoft ID、Microsoft Graph API との連携を開始した直後でも、アプリケーションに新しい機能を実装するときでも、コミュニティからヘルプを入手し、開発者として使用できるサポートのオプションを把握しておく必要があります。 この記事では、これらのオプションについて説明します。以下はその概要です。

> [!div class="checklist"]
> * 問題に対する回答がコミュニティにないか、または実装する機能に関する既存のドキュメントがないか検索する
> * マイクロソフトのサポート ツールを使用して固有の問題をデバッグした方がよい場合もある
> * 必要な回答が見つからない場合は *Stack Overflow* で質問する
> * 認証ライブラリで問題が見つかった場合は *GitHub* の問題として提起する
> * 最後に、サポート要求をオープンしてサポート担当者と話をする


## <a name="search"></a>Search

開発に関連する疑問は、マイクロソフトのドキュメント、[GitHub のサンプル](https://github.com/azure-samples)、または [Stack Overflow](https://www.stackoverflow.com) への回答から解決できる場合があります。

### <a name="scoped-search"></a>範囲指定の検索
検索速度を高めるには、[お使いの検索エンジン](https://bing.com)で次を使用して、検索範囲を Stack Overflow、マイクロソフトのドキュメント、およびコード サンプルに設定できます。
```
{Your Search Terms} (site:stackoverflow.com OR site:docs.microsoft.com OR site:github.com/azure-samples OR site:cloudidentity.com OR site:developer.microsoft.com/en-us/graph)
```
ここで、*{Your Search Terms}* は検索するキーワードです。
<br/>

## <a name="use-our-development-support-tools"></a>開発サポート ツールを使用する

|ツール  |説明  |
|---------|---------|
|[jwt.ms](https://jwt.ms)| ID またはアクセス トークンを貼り付けて要求の名前と値をデコードする |
|[エラー コード分析ツール](https://apps.dev.microsoft.com/portal/tools/errors)| サインイン中または同意ページで受け取ったエラー コードを貼り付けて考えられる原因や修復方法を確認する |
|[Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)| Microsoft Graph API に対して要求を出して応答を確認できるツール|

<br/>

[![Stack Overflow](./media/developer-support-help-options/stackoverflow-logo.png)](https://www.stackoverflow.com)
## <a name="post-a-question-to-stack-overflow"></a>Stack Overflow に質問を投稿する

Stack Overflow は開発に関連する質問があるときに優先して使用するチャネルで、コミュニティのメンバーとマイクロソフト チームのメンバーが直接的に関与して問題の解決をサポートします。

検索によって質問の回答が見つからなかった場合は、新しい質問を Stack Overflow に投稿します。質問の際には、コミュニティでの分類とタイムリーな回答のために、次のタグを使用してください。

|コンポーネント/区分  |タグ  |
|---------|---------|
|ADAL ライブラリ |[[adal]](http://stackoverflow.com/questions/tagged/adal)|
|MSAL ライブラリ     |[[msal]](http://stackoverflow.com/questions/tagged/msal)|
|OWIN ミドルウェア  |[[azure-active-directory]](http://stackoverflow.com/questions/tagged/azure-active-directory)|
|[Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  |[[azure-ad-b2b]](http://stackoverflow.com/questions/tagged/azure-ad-b2b)|
|[Azure B2C](https://azure.microsoft.com/services/active-directory-b2c/)  |[[azure-ad-b2c]](http://stackoverflow.com/questions/tagged/azure-ad-b2b)|
|[Microsoft Graph API](https://developer.microsoft.com/graph/) |[[microsoft-graph]](http://stackoverflow.com/questions/tagged/microsoft-graph)
|その他認証や承認のトピックに関連する区分 |[[azure-active-directory]](http://stackoverflow.com/questions/tagged/azure-active-directory)
<br/>
> [!TIP]
> Stack Overflow の次の投稿には、質問をするためのコツやソース コードを追加するヒントがあります。次のガイドラインに従うと、コミュニティのメンバーが質問にアクセスしてすぐに回答が得られる確率が高まります。  
> - [よい質問をする方法](https://stackoverflow.com/help/how-to-ask)
> - [最小限の例、完全な例、実証可能な例を作成する方法](https://stackoverflow.com/help/mcve)

<br/>


[![Stack Overflow](./media/developer-support-help-options/github-logo.png)](https://www.github.com)
## <a name="create-a-github-issue"></a>GitHub の問題を作成する

 ライブラリにバグや問題が見つかった場合は、GitHub リポジトリに問題を報告してください。 ライブラリはオープンソースであるため、プル要求を送信することもできます。 次の記事には、ライブラリの一覧と GitHub リポジトリが含まれています。

- [ADAL、MSAL、および Owin ミドルウェア](active-directory-authentication-libraries.md)のライブラリと GitHub リポジトリ

<br/>

## <a name="open-a-support-request"></a>サポート要求をオープンする

サポート要求をオープンして、サポート担当者と話をすることができます。 Azure をご利用のお客様には複数のサポート オプションが用意されています。 プランの比較については、[こちらのページ](https://azure.microsoft.com/support/plans/)をご覧ください。 また、Azure をご利用のお客様には Developer サポートも用意されています。 Developer サポート プランを購入する方法については、[こちらのページ](https://azure.microsoft.com/support/plans/developer/)をご覧ください。

- Azure サポート プランをお持ちの場合は、[こちらからサポート要求をオープン](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)します。

- Azure をご利用でない場合、マイクロソフトの[商用サポート](https://support.microsoft.com/en-us/gp/contactus81?Audience=Commercial)からサポート要求をオープンすることもできます。

サポートの入手や質問に[仮想エージェント](https://support.microsoft.com/contactus/?ws=support)もお試しいただけます。

### <a name="free-chat-support-for-a-limited-time"></a>無料のチャット サポート (時間制限あり)

マイクロソフトのパートナーであれば、所定の時間分無料のチャット サポートを受けることができます。 マイクロソフトのパートナーでない場合は、[こちら](https://partners.microsoft.com/PartnerProgram/simplifiedenrollment.aspx)から無料で登録し、他のメリットも享受できます。

登録後、[こちら](https://aka.ms/devchat)からチャット要求を開始できます。