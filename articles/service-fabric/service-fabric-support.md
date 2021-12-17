---
title: Azure Service Fabric のサポート オプションについて説明します。
description: サポートされている Azure Service Fabric クラスターのバージョンと、サポート チケットを提出するためのリンク
author: erikadoyle
ms.topic: troubleshooting
ms.date: 5/17/2021
ms.author: edoyle
ms.custom: support-help-page
ms.openlocfilehash: 5228663dfa3a272d4d5aaa569d8a14689ddf7490
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131467437"
---
# <a name="azure-service-fabric-support-options"></a>Azure Service Fabric のサポート オプション

必要なサポートの緊急度と問題の重大度に応じて、Service Fabric クラスターとアプリケーション ワークロードを管理するニーズに対応するため、多数のサポート リクエスト オプションが作成されました。

## <a name="self-help-troubleshooting"></a>セルフ ヘルプのトラブルシューティング
<div class='icon is-large'>
    <img alt='Self help content' src='./media/logos/doc-logo.png'>
</div>

これは、Service Fabric のサイト信頼性エンジニアがインシデントに対応するときや、ユーザーがアクティブなシステムの問題の解決策を見つけるときに参照する資料です。また、チケットが作成されたときに、カスタマー サポート サービスもこれを参照します。

セルフ ヘルプのトラブルシューティングに関するコンテンツの完全な一覧については、「[Service Fabric トラブルシューティング ガイド](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)」を参照してください。

## <a name="create-an-azure-support-request"></a>Azure サポート要求を作成する
<div class='icon is-large'>
    <img alt='Azure support' src='./media/logos/azure-logo.png'>
</div>

Azure で実行されている Service Fabric クラスターに関連する問題を報告するには、[Azure Portal で](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)、または [Microsoft サポート ポータルで](https://support.microsoft.com/oas/default.aspx?prid=16146)サポート チケットを開きます。

各項目の詳細情報

- [Azure のサポート オプション](https://azure.microsoft.com/support/plans/?b=16.44)。
- [Microsoft プレミア サポート](https://support.microsoft.com/premier)。

> [!Note]
> ブロンズの信頼性レベルで実行されているクラスターまたは単一ノード クラスターでは、テスト ワークロードのみを実行できます。 ブロンズの信頼性で実行されているクラスターまたは単一ノード クラスターで問題が発生した場合は、Microsoft サポート チームによって問題を軽減するための支援が提供されますが、根本原因分析は実行されません。 詳細については、[クラスターの信頼性の特徴](./service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster)に関するページを参照してください。
>
> 運用の準備が整ったクラスターで必要なものの詳細については、「[運用環境の準備状況チェックリスト](./service-fabric-production-readiness-checklist.md)」を参照してください。

<a id="getlivesitesupportonprem"></a>

## <a name="create-a-support-request-for-standalone-service-fabric-clusters"></a>スタンドアロン Service Fabric クラスターのサポート リクエストを作成する
<div class='icon is-large'>
    <img alt='Azure support' src='./media/logos/azure-logo.png'>
</div>

オンプレミスまたは他のクラウドで実行されている Service Fabric クラスターに関連する問題を報告するには、[Microsoft サポート ポータル](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)でプロフェッショナル サポートのチケットを開くことができます。

各項目の詳細情報

- [オンプレミスに関する Microsoft によるプロフェッショナル サポート](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0)。
- [Microsoft プレミア サポート](https://support.microsoft.com/en-us/premier)。

## <a name="post-a-question-to-microsoft-qa"></a>Microsoft Q&A に質問を投稿する
<div class='icon is-large'>
    <img alt='Microsoft Q&A' src='./media/logos/microsoft-logo.png'>
</div>   

Service Fabric に関する質問については、Microsoft のエンジニア、Azure Most Valuable Professionals (MVP)、およびエキスパート コミュニティのメンバーから直接回答が得られます。

[Microsoft Q&A](/answers/topics/azure-service-fabric.html) は、Azure のコミュニティ サポートの推奨される情報源です。

Microsoft Q&A を検索しても問題に対する回答が見つからない場合は、新しい質問を送信します。 質問を投稿するときは、必ず [**azure-service-fabric**](/answers/topics/azure-service-fabric.html) タグを使用してください。 ここでは、[上手な質問](/answers/articles/24951/how-to-write-a-quality-question.html)を作成するための Microsoft Q&A のヒントをいくつかを紹介します。

## <a name="open-a-github-issue"></a>GitHub の問題を開く
<div class='icon is-large'>
    <img alt='GitHub-image' src='./media/logos/github-logo.png'>
</div>

Azure Service Fabric の問題は、[Service Fabric GitHub](https://github.com/microsoft/service-fabric/issues) で報告してください。 このリポジトリは、Azure Service Fabric に関連する問題の報告と追跡に加え、機能に関する小さな要求を行うためのものです。 **稼働中のサイトの問題を報告するのに、この手段を使用しないでください**。

## <a name="check-the-stackoverflow-forum"></a>StackOverflow フォーラムを確認する
<div class='icon is-large'>
    <img alt='Stack Overflow' src='./media/logos/stack-overflow-logo.png'>
</div>

[StackOverflow][stackoverflow] の `azure-service-fabric` タグは、プラットフォームのしくみや、それを使用して特定のタスクを実現する方法について一般的な質問をする場合に使用します。

## <a name="stay-informed-of-updates-and-new-releases"></a>更新プログラムと新しいリリースに関する最新情報を入手する

<div class='icon is-large'>
    <img alt='Stay informed' src='./media/logos/updates-logo.png'>
</div>

重要な製品の更新プログラム、ロードマップ、および発表については、「[Azure の更新情報](https://azure.microsoft.com/updates/?product=service-fabric)」で確認できます。

Service Fabric ランタイムおよび SDK の最新リリースと更新については、「[Service Fabric のリリース](release-notes.md)」を参照してください。



## <a name="next-steps"></a>次のステップ

[サポートされている Service Fabric のバージョン](service-fabric-versions.md)

<!--references-->
[Microsoft Q&A question page]: /answers/topics/azure-service-fabric.html
[stackoverflow]: https://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/d365community/forum/e622b37a-2225-ec11-b6e6-000d3a4f0f84
[acom-docs]: ./index.yml
[sample-repos]: /samples/browse/?products=azure
