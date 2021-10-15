---
title: Azure Sentinel のベスト プラクティス
description: Azure Sentinel ワークスペースを管理する際のベスト プラクティスについて説明します。
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 07/21/2021
ms.openlocfilehash: 8454a75936c84f012d05c8917cfffeec4a08dcdc
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129278426"
---
# <a name="best-practices-for-azure-sentinel"></a>Azure Sentinel のベスト プラクティス

この一連のベストプラクティスでは、Azure Sentinel のデプロイ、管理、使用に関するガイダンスを提供しています。詳細については、他の記事へのリンクも含まれています。

> [!IMPORTANT]
> Azure Sentinel をデプロイする前に、「[展開前のアクティビティと前提条件](prerequisites.md)」を確認し、完了させてください。
>


## <a name="best-practice-references"></a>ベスト プラクティスのリファレンス

この Azure Sentinel のドキュメントには、記事全体にわたってベスト プラクティス ガイダンスが含まれています。 この記事で提供されている内容に加え、詳細については、次の記事を参照してください。

- **[管理者ユーザー]**:

    - [Azure Sentinel をデプロイするためのデプロイ前のアクティビティと前提条件](prerequisites.md)
    - [Azure Sentinel ワークスペース アーキテクチャのベスト プラクティス](best-practices-workspace-architecture.md)
    - [Azure Sentinel ワークスペース アーキテクチャを設計する](design-your-workspace-architecture.md)
    - [Azure Sentinel のサンプル ワークスペースの設計](sample-workspace-designs.md)
    - [データ収集のベスト プラクティス](best-practices-data.md)
    - [Azure Sentinel のコストと課金](azure-sentinel-billing.md)
    - [Azure Sentinel のアクセス許可](roles.md)
    - [Azure Sentinel で MSSP の知的財産権を保護する](mssp-protect-intellectual-property.md)
    - [Azure Sentinel への脅威インテリジェンスの統合](threat-intelligence-integration.md)
    - [Azure Sentinel クエリとアクティビティの監査](audit-sentinel-data.md)

- **アナリスト**:

    - [推奨されるプレイブック](automate-responses-with-playbooks.md#recommended-playbooks)
    - [Azure Sentinel での偽陽性の処理](false-positives.md)
    - [Azure Sentinel で脅威を検出する](hunting.md)
    - [一般的に使用される Azure Sentinel ブック](top-workbooks.md)
    - [難しい設定なしで脅威を検出する](detect-threats-built-in.md)
    - [脅威を検出するためのカスタム分析規則を作成する](detect-threats-custom.md)
    - [Jupyter Notebook を使用してセキュリティの脅威を検出する](notebooks.md)

詳細については、「[Architecting SecOps for Success: Best Practices for Deploying Azure Sentinel (成功するための SecOps の設計: Azure Sentinel のデプロイに関するベスト プラクティス)](https://youtu.be/DyL9MEMhqmI)」という動画をご覧ください

## <a name="regular-soc-activities-to-perform"></a>実行する標準 SOC アクティビティ

継続的なセキュリティのベストプラクティスを確保するために、次の Azure Sentinel アクティビティを定期的にスケジュールします。

### <a name="daily-tasks"></a>毎日のタスク

- **インシデントをトリアージして調査します**。  Azure Sentinel **[インシデント]** ページで、現在設定されている分析ルールによって生成された新しいインシデントを確認し、新しいインシデントの調査を開始します。 詳細については、「[チュートリアル:Azure Sentinel でインシデントを調査する](investigate-cases.md)」を参照してください。

- **ハンティング クエリとブックマークを確認します**。 すべての組み込みクエリの結果を確認し、既存のハンティング クエリとブックマークを更新します。 新しいインシデントを手動で生成するか、該当する場合は古いインシデントを更新します。  詳細については、次を参照してください。

    - [Microsoft セキュリティ アラートからインシデントを自動的に作成する](create-incidents-from-alerts.md)
    - [Azure Sentinel で脅威を検出する](hunting.md)
    - [Azure Sentinel でハンティング中にデータを追跡する](bookmarks.md)

- **分析ルール**。  新しくリリースされたルールと最近接続されたデータ コネクタから新たに利用可能になったルールなど、必要に応じて新しい分析ルールを確認し、有効にします。

- **データ コネクタ**。 各データコネクタから受信した最後のログの状態、日付、時刻を確認し、データが流れていることを確認します。 新しいコネクタとインジェストを検査して、セットの制限を超えていないことを確認します。 詳細については、「[データ コレクションのベスト プラクティス](best-practices-data.md)」と「[データ ソースの接続](connect-data-sources.md)」を参照してください。

- **Log Analytics エージェント**。 サーバーとワークステーションのワークスペースへの接続が有効なことを確認し、失敗した接続のトラブルシューティングと修復を行います。   詳細については、「[Log Analytics エージェントの概要](../azure-monitor/agents/log-analytics-agent.md)」を参照してください。

- **プレイブックのエラー**。 プレイブックの状態を確認し、エラーが発生した場合はトラブルシューティングを行います。   詳細については、「[チュートリアル: Azure Sentinel でオートメーション ルールとプレイブックを使用する](tutorial-respond-threats-playbook.md)」を参照してください。


### <a name="weekly-tasks"></a>毎週行うタスク

- **ブックの更新**。 ブックにインストールしなければならない更新プログラムがあるかどうかを確認します。 詳細については、「[一般的に使用される Azure Sentinel ブック](top-workbooks.md)」を参照してください。

- **Azure Sentinel の GitHub リポジトリの確認**。 [Azure Sentinel GitHub](https://github.com/Azure/Azure-Sentinel) リポジトリを確認して、分析ルール、ブック、ハンティング クエリ、プレイブックなど、環境に有効な新しいリソースまたは更新されたリソースがないかどうかを調べます。

- **Azure Sentinel 監査**。 Azure Sentinel アクティビティを使用して、誰が分析ルールやブックマークなどのリソースを更新または削除したかを確認します。 詳細については、「[Azure Sentinel クエリとアクティビティの監査](audit-sentinel-data.md)」をご覧ください。

### <a name="monthly-tasks"></a>毎月行うタスク

- **ユーザーのアクセスを確認**。 ユーザーのアクセス許可を確認し、無効なユーザーを検査します。 詳細については、「[Azure Sentinel のアクセス許可](roles.md)」を参照してください。

- **Log Analytics ワークスペースの確認**。 Log Analytics ワークスペースのデータ保持ポリシーが組織のポリシーと引き続き整合していることを確認します。  詳細については、「[データ保持ポリシー](/workplace-analytics/privacy/license-expiration)」と、「[Azure Data Explorer の統合によるログの長期保存](store-logs-in-azure-data-explorer.md)」を参照してください。


## <a name="integrate-with-microsoft-security-services"></a>他の Microsoft セキュリティ サービスとの統合

Azure Sentinel は、ワークスペースにデータを送信するコンポーネントによって強化されており、他の Microsoft サービスとの統合によってより強力になっています。 Microsoft Cloud App Security、Microsoft Defender for Endpoint、Microsoft Defender for Identity などの製品に取り込まれたログは、これらのサービスが検知情報を作成し、その検知情報を Azure Sentinel に提供します。 また、ログを Azure Sentinel に直接取り込んで、イベントやインシデントの全体像を把握することもできます。

たとえば次の図は、Azure Sentinel が他の Microsoft サービスやマルチクラウド、パートナーのプラットフォームからデータを取り込み、環境に適したものにする方法を示しています。

:::image type="content" source="media/best-practices/azure-sentinel-and-other-services.png" alt-text="Azure Sentinel と他の Microsoft およびパートナーサービスとの統合":::

Azure Sentinel は、他のソースからアラートやログを取り込むだけでなく、以下のような機能も備えています。

- **取り込んだ情報を [機械学習](bring-your-own-ml.md)で利用する** ことで、より優れたイベントの相関、アラートの集約、異常検知などが可能になります。
- **[ワークブック](get-visibility.md)を使って、トレンドや関連情報、管理業務や調査に使用する主要データを示すインタラクティブなビジュアルを構築し、提示** します。
- **アラートに対応するための [プレイブック](tutorial-respond-threats-playbook.md)を実行し**、情報の収集、アイテムに対するアクションの実行、各種プラットフォームへの通知の送信を行います。
- ServiceNow や Jira などの **パートナー プラットフォーム** と統合し、SOC チームに必要なサービスを提供します。
- [脅威インテリジェンス プラットフォーム](threat-intelligence-integration.md)から **エンリッチメント フィードを取り込み、フェッチし**、調査のための貴重なデータを提供します。

##  <a name="manage-and-respond-to-incidents"></a>インシデントを管理して対応する

次の図は、インシデント管理と応答プロセスで推奨される手順を示しています。

:::image type="content" source="media/best-practices/incident-handling.png" alt-text="インシデント管理プロセス: トリアージ。準備。修復。根絶。インシデント アクティビティの投稿。":::

次のセクションでは、Azure Sentinel 機能を使用してプロセス全体のインシデント管理と応答を行う方法について、プロセス全体を通して高度な説明を行います。 詳細については、「[チュートリアル:Azure Sentinel でインシデントを調査する](investigate-cases.md)」を参照してください。

### <a name="use-the-incidents-page-and-the-investigation-graph"></a>[インシデント] ページと調査グラフを使用する

Azure Sentinel の **[インシデント]** ページと **調査グラフ** で、新しいインシデントに対するあらゆるトリアージ プロセスを開始します。 

アカウント、Url、IP アドレス、ホスト名、アクティビティ、タイムラインなどの主要なエンティティを検出します。 このデータを使用して、[偽陽性](false-positives.md)があるかどうかを把握します。この場合、インシデントを直接閉じることができます。

生成されたインシデントはすべて **[インシデント]** ページに表示されます。このページは、トリアージおよび早期調査の中心地として機能します。 **[インシデント]** ページには、タイトル、重大度、関連するアラート、ログ、および関心のあるエンティティが一覧表示されます。 インシデントには、収集されたログや、インシデントに関連するすべてのツールへのクイック ジャンプも用意されています。

**[インシデント]** ページは **調査グラフ** と連動しています。これは、ユーザーがアラートを調査して深く掘り下げ、攻撃の全容を示すことができるインタラクティブなツールです。 その後、ユーザーはイベントのタイムラインを構築し、脅威チェーンの範囲を検出できます。

インシデントが真陽性であることが判明した場合、 **[インシデント]** ページから直接アクションを起こし、ログやエンティティを調査し、脅威の連鎖を検査します。 脅威を特定し、アクション プランを作成したら、Azure Sentinel と[その他の Microsoft セキュリティサービス](best-practices.md#integrate-with-microsoft-security-services)の他のツールを使用して調査を続行します。


### <a name="handle-incidents-with-workbooks"></a>ブックでインシデントを処理する

Azure Sentinel ブックは、[情報や傾向を視覚化して表示する](get-visibility.md)だけでなく、貴重な調査ツールでもあります。

たとえば、[調査分析情報](top-workbooks.md#investigation-insights)ブックを使用して、関連付けられているエンティティやアラートと共に特定のインシデントを調査します。 このブックを使用すると、関連するログ、アクション、およびアラートを表示することで、エンティティをより深く掘り下げていくことができます。

### <a name="handle-incidents-with-threat-hunting"></a>脅威を追及してインシデントを処理する

根本原因の調査と検索では、組み込みの脅威検索クエリを実行し、セキュリティ侵害の兆候について結果を確認します。

調査中、または脅威を修復、根絶するための措置を講じた後、[livestream](livestream.md) を使用して、悪意のあるイベントが残っていないか、または悪意のあるイベントがまだ継続していないかをリアルタイムで監視します。

### <a name="handle-incidents-with-entity-behavior"></a>エンティティの動作によるインシデントの処理

Azure Sentinel のエンティティの動作を使用すると、ユーザーは、アカウントやホスト名の調査など、特定のエンティティのアクションとアラートを確認し、調査できます。 詳細については、次を参照してください。

- [Azure Sentinel でのユーザーとエンティティの動作分析の有効化 (UEBA)](enable-entity-behavior-analytics.md)
- [UEBA データを使用したインシデントの調査](investigate-with-ueba.md)
- [Azure Sentinel UEBA エンリッチメント リファレンス](ueba-enrichments.md)

### <a name="handle-incidents-with-watchlists-and-threat-intelligence"></a>ウォッチリストと脅威インテリジェンスによるインシデントの処理

脅威インテリジェンスに基づく検出を最大限に活用するには、[脅威インテリジェンス データ コネクタ](connect-threat-intelligence-tip.md)を使用して、セキュリティ侵害の兆候を取り込む必要があります。

- [Fusion](fusion.md) と [TI Map アラート](./understand-threat-intelligence.md)で必要なデータ ソースを接続します
- [TAXII と TIP のプラットフォーム](./connect-threat-intelligence-tip.md)から兆候を取り込みます

セキュリティ侵害の兆候は、分析ルール、脅威の探索、ログの調査、インシデントの発生などに使用します。

取り込まれたデータと、エンリッチ データなど外部ソースからのデータを結合するウォッチリストを使用します。 たとえば、組織または最近終了した従業員が使用する IP アドレス範囲のリストを作成します。 プレイブックでウォッチリストを使用することで、悪意のある IP アドレスをウォッチリストに追加するなど、エンリッチメント データを収集し、検知、脅威のハンティングや調査の際に使用できます。

インシデントの発生時には、ウォッチリストを使用して調査データを格納し、調査を行ったときにそれらを削除して、機密データが表示されないようにします。

## <a name="next-steps"></a>次のステップ

Azure Sentinel の概要は、以下を参照してください。

- [Azure Sentinel をオンボードする](quickstart-onboard.md)
- [アラートの視覚化](get-visibility.md)
