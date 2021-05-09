---
title: Azure Service Fabric のサポート オプションについて説明します。
description: サポートされている Azure Service Fabric クラスターのバージョンと、サポート チケットを提出するためのリンク
author: pkcsf
ms.topic: troubleshooting
ms.date: 8/24/2018
ms.author: pkc
ms.openlocfilehash: 9ba2f25665620524cd55a6f81c796aa024362d9d
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2021
ms.locfileid: "108123035"
---
# <a name="azure-service-fabric-support-options"></a>Azure Service Fabric のサポート オプション

必要なサポートの緊急度と問題の重大度に応じて、Service Fabric クラスターとアプリケーション ワークロードを管理するニーズに対応するため、多数のサポート リクエスト オプションが作成されました。

## <a name="create-an-azure-support-request"></a>Azure サポート要求を作成する

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

オンプレミスまたは他のクラウドで実行されている Service Fabric クラスターに関連する問題を報告するには、[Microsoft サポート ポータル](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)でプロフェッショナル サポートのチケットを開くことができます。

各項目の詳細情報

- [オンプレミスに関する Microsoft によるプロフェッショナル サポート](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0)。
- [Microsoft プレミア サポート](https://support.microsoft.com/en-us/premier)。

## <a name="post-a-question-to-microsoft-qa"></a>Microsoft Q&A に質問を投稿する

Service Fabric に関する質問については、Microsoft のエンジニア、Azure Most Valuable Professionals (MVP)、およびエキスパート コミュニティのメンバーから直接回答が得られます。

[Microsoft Q&A](https://docs.microsoft.com/answers/products/) は、Azure のコミュニティ サポートの推奨される情報源です。

Microsoft Q&A を検索しても問題に対する回答が見つからない場合は、新しい質問を送信します。 質問の投稿では、必ず **azure-service-fabric** タグを使用してください。 ここでは、[上手な質問](https://docs.microsoft.com/answers/articles/24951/how-to-write-a-quality-question.html)を作成するための Microsoft Q&A のヒントをいくつかを紹介します。

## <a name="open-a-github-issue"></a>GitHub の問題を開く

Azure Service Fabric の問題は、[Service Fabric GitHub](https://github.com/microsoft/service-fabric/issues) で報告してください。 このリポジトリは、Azure Service Fabric に関連する問題の報告と追跡に加え、機能に関する小さな要求を行うためのものです。 **稼働中のサイトの問題を報告するのに、この手段を使用しないでください**。

## <a name="check-the-stackoverflow-forum"></a>StackOverflow フォーラムを確認する

[StackOverflow][stackoverflow] の `azure-service-fabric` タグは、プラットフォームのしくみや、それを使用して特定のタスクを実現する方法について一般的な質問をする場合に使用します。

## <a name="submit-feedback-on-azure-feedback"></a>Azure フィードバックでフィードバックを送信する

[Service Fabric の Azure フィードバック フォーラム][uservoice-forum]は、製品の機能に関する重要なアイデアを出すのに最適な場所です。 最も人気のある要求を確認し、それらを考慮して長期的な計画を立てる Microsoft の手段となっています。 Microsoft では、コミュニティ内で自身の提案への支持を集めることをお勧めしています。


> [!Note]
> **Service Fabric プレビュー バージョンは、運用環境での使用はサポートされていません。** Microsoft では、場合によって、重要な機能の変更を含む特別なプレビュー リリースを作成します。それについて、早期のフィードバックを調査するためです。 プレビュー バージョンは、運用環境のワークロードを処理しない分離されたテスト環境でのみ使用してください。 運用環境のクラスターは常に、サポートされていて、安定した Service Fabric のバージョンを実行する必要があります。 これらのプレビュー リリースについては、Microsoft は有償のサポート オプションを提供していません。
>
> プレビュー バージョンのメジャーおよびマイナーのバージョン番号は常に 255 で始まります。 たとえば、Service Fabric のバージョンが 255.255.5703.949 であれば、このリリースはプレビュー段階にあり、テスト クラスターでの使用だけが想定されています。 これらのプレビュー リリースは、[Service Fabric チームのブログ](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)でも発表され、含まれる機能の詳細情報があります。 上記のいずれかのオプションを使用してフィードバックをお送りください。

## <a name="next-steps"></a>次のステップ

[サポートされている Service Fabric のバージョン](service-fabric-versions.md)

<!--references-->
[Microsoft Q&A question page]: /answers/topics/azure-service-fabric.html
[stackoverflow]: https://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: ./index.yml
[sample-repos]: /samples/browse/?products=azure