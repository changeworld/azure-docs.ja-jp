---
title: サービス構成 - QnA Maker
description: リソースを構成する方法と場所について理解します。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/9/2020
ms.openlocfilehash: 7a24045d38b594e09b931a885c60851109a108e3
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110372690"
---
# <a name="service-configuration"></a>サービス構成

QnA Maker の各バージョンには、異なる Azure リソース (サービス) のセットが使用されます。 この記事では、これらのサービスでサポートされているカスタマイズについて説明します。 

## <a name="app-service"></a>App Service

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/v1)

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

# <a name="custom-question-answering-preview-release"></a>[カスタム質問と回答 (プレビュー リリース)](#tab/v2)

App Service のカスタマイズは、Custom question answering (プレビュー) には適用されません。

---

## <a name="qna-maker-service"></a>QnA Maker Service

QnA Maker サービスには、次のユーザーが 1 つの QnA Maker サービスとそのすべてのナレッジベースで共同作業するための構成があります。

お使いのサービスに[コラボレーターを追加する方法](./reference-role-based-access-control.md)を確認してください。

## <a name="change-azure-cognitive-search"></a>Azure Cognitive Search を変更する

QnA Maker サービスにリンクされている [Cognitive Search サービスを変更する方法](./how-to/configure-QnA-Maker-resources.md#configure-qna-maker-to-use-different-cognitive-search-resource)を参照してください。

## <a name="change-default-answer"></a>既定の回答の変更

[既定の回答のテキストを変更する方法](How-To/change-default-answer.md)を参照してください。 

## <a name="telemetry"></a>製品利用統計情報

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/v1)

Application Insights は、QnA Maker GA でテレメトリを監視するために使用されます。 QnA Maker に固有の構成設定はありません。

# <a name="custom-question-answering-preview-release"></a>[カスタム質問と回答 (プレビュー リリース)](#tab/v2)

[テレメトリを Custom question answering (プレビュー) サービスに追加する方法](How-To/get-analytics-knowledge-base.md)を知る。 

---

## <a name="app-service-plan"></a>App Service プラン

# <a name="qnamaker-ga-stable-release"></a>[QnAMaker GA (安定版リリース)](#tab/v1)

App Service プランには、QnA Maker に固有の構成設定はありません。

# <a name="custom-question-answering-preview-release"></a>[カスタム質問と回答 (プレビュー リリース)](#tab/v2)

App Service Plan は Custom question answering (プレビュー) には使用しません。

---

## <a name="next-steps"></a>次のステップ

ナレッジ ベースにインポートするドキュメントおよび URL の[形式](reference-document-format-guidelines.md)を確認してください。
