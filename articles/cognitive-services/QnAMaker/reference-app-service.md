---
title: サービス構成 - QnA Maker
description: リソースを構成する方法と場所について理解します。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: f504e1de8c751cb7d97679c8d56c591b0508c808
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131071118"
---
# <a name="service-configuration"></a>サービス構成

QnA Maker の各バージョンには、異なる Azure リソース (サービス) のセットが使用されます。 この記事では、これらのサービスでサポートされているカスタマイズについて説明します。 

## <a name="app-service"></a>App Service

QnA Maker は、App Service を使用して、[generateAnswer API](/rest/api/cognitiveservices/qnamaker4.0/runtime/generateanswer) が使用するクエリ ランタイムを提供します。

これらの設定は、App Service 用に Azure portal に用意されています。 この設定は、 **[設定]** 、 **[構成]** を選択して使用できます。

個々に設定する場合は [アプリケーションの設定] の一覧から、いくつかの設定を変更する場合は **[高度な編集]** から選択して変更することができます。

|リソース|設定|
|--|--|
|AzureSearchAdminKey|Cognitive Search - QnA ペア ストレージおよび 1 位のランカーで使用されます|
|AzureSearchName|Cognitive Search - QnA ペア ストレージおよび 1 位のランカーで使用されます|
|DefaultAnswer|一致が見つからない場合の回答のテキスト|
|UserAppInsightsAppId|チャット ログとテレメトリ|
|UserAppInsightsKey|チャット ログとテレメトリ|
|UserAppInsightsName|チャット ログとテレメトリ|
|QNAMAKER_EXTENSION_VERSION|常に _latest_ に設定します。 この設定により、App Service の QnAMaker サイト拡張機能が初期化されます。|

変更を完了したら、Azure portal の **[概要]** ページからサービスを **再起動** する必要があります。

## <a name="qna-maker-service"></a>QnA Maker Service

QnA Maker サービスには、次のユーザーが 1 つの QnA Maker サービスとそのすべてのナレッジベースで共同作業するための構成があります。

お使いのサービスに[コラボレーターを追加する方法](./index.yml)を確認してください。

## <a name="change-azure-cognitive-search"></a>Azure Cognitive Search を変更する

QnA Maker サービスにリンクされている [Cognitive Search サービスを変更する方法](./how-to/configure-QnA-Maker-resources.md#configure-qna-maker-to-use-different-cognitive-search-resource)を参照してください。

## <a name="change-default-answer"></a>既定の回答の変更

[既定の回答のテキストを変更する方法](How-To/change-default-answer.md)を参照してください。 

## <a name="telemetry"></a>製品利用統計情報

Application Insights は、QnA Maker GA でテレメトリを監視するために使用されます。 QnA Maker に固有の構成設定はありません。

## <a name="app-service-plan"></a>App Service プラン

App Service プランには、QnA Maker に固有の構成設定はありません。

## <a name="next-steps"></a>次のステップ

ナレッジ ベースにインポートするドキュメントおよび URL の[形式](reference-document-format-guidelines.md)を確認してください。
