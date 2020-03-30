---
title: サービス構成 - QnA Maker
description: リソースを構成する方法と場所について理解します。
ms.topic: reference
ms.date: 02/21/2020
ms.openlocfilehash: 8ef6fecbfeb119d0c68ec5bc3bbc90ec449dbb7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651805"
---
# <a name="service-configuration"></a>サービス構成

QnA Maker では、Cognitive Search、App Service、App Service プラン、Application Insights などのいくつかの Azure リソース (サービス) を使用します。

QnA Maker でサポートされるこれらの設定のすべてのカスタマイズは、以下のとおりです。

## <a name="app-service"></a>App Service

QnA Maker は、App Service を使用して、[generateAnswer API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) が使用するクエリ ランタイムを提供します。


これらの設定は、App Service 用に Azure portal に用意されています。 この設定は、 **[設定]** 、 **[構成]** を選択して使用できます。

個々に設定する場合は [アプリケーションの設定] の一覧から、いくつかの設定を変更する場合は **[高度な編集]** から選択して変更することができます。

|リソース|設定|
|--|--|
|AzureSearchAdminKey|Cognitive Search - QnA セット ストレージおよび 1 位のランカーで使用されます|
|AzureSearchName|Cognitive Search - QnA セット ストレージおよび 1 位のランカーで使用されます|
|DefaultAnswer|一致が見つからない場合の回答のテキスト|
|UserAppInsightsAppId|チャット ログとテレメトリ|
|UserAppInsightsKey|チャット ログとテレメトリ|
|UserAppInsightsName|チャット ログとテレメトリ|

[お使いの Cognitive Search サービスに変更を追加する方法](./how-to/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource)を確認してください。

変更を完了したら、Azure portal の **[概要]** ページからサービスを**再起動**する必要があります。

## <a name="qna-maker-service"></a>QnA Maker Service

QnA Maker サービスには、次のユーザーが 1 つの QnA Maker サービスとそのすべてのナレッジベースで共同作業するための構成があります。

お使いのサービスに[コラボレーターを追加する方法](./how-to/collaborate-knowledge-base.md)を確認してください。

## <a name="application-insights"></a>Application Insights

Application Insights には、QnA Maker に固有の構成設定はありません。

## <a name="app-service-plan"></a>App Service プラン

App Service プランには、QnA Maker に固有の構成設定はありません。

## <a name="next-steps"></a>次のステップ

ナレッジ ベースにインポートするドキュメントおよび URL の[形式](reference-document-format-guidelines.md)を確認してください。