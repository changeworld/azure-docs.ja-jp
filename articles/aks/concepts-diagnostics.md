---
title: Azure Kubernetes Service (AKS) 診断の概要
description: Azure Kubernetes Service の自己診断クラスターについて説明します。
services: container-service
author: yunjchoi
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: yunjchoi
ms.openlocfilehash: ee11221e5484a796b8dbbcb10a323288d3e74756
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011560"
---
# <a name="azure-kubernetes-service-diagnostics-preview-overview"></a>Azure Kubernetes Service 診断 (プレビュー) の概要

Azure Kubernetes Service (AKS) クラスターに関する問題のトラブルシューティングは、クラスターを維持する上で重要な役割を果たしており、クラスターでミッション クリティカルなワークロードが実行されている場合は特に当てはまります。 AKS 診断はインテリジェントな自己診断エクスペリエンスです。
* これは、クラスター内の問題を特定して解決するのに役立ちます。 
* クラウドネイティブです。
* 追加の構成や課金は必要ありません。

この機能は現在パブリック プレビュー段階にあります。 

## <a name="open-aks-diagnostics"></a>AKS 診断を開く

AKS 診断にアクセスするには:

1. [Azure portal](https://portal.azure.com) で、対象の Kubernetes クラスターに移動します。
1. 左側のナビゲーションで **[問題の診断と解決]** をクリックすると、AKS 診断が開きます。
1. 次の方法で、_クラスター ノードの問題_ など、クラスターの問題を最もよく表すカテゴリを選択します。
    * ホームページのタイルにあるキーワードを使用します。
    * 問題を最もよく表しているキーワードを検索バーに入力します。

![ホームページ](./media/concepts-diagnostics/aks-diagnostics-homepage.png)

## <a name="view-a-diagnostic-report"></a>診断レポートの表示

カテゴリをクリックした後、対象のクラスターに固有の診断レポートを表示できます。 診断レポートでは、状態アイコンを使用してクラスター内の問題をインテリジェントにお知らせします。 **[詳細情報]** をクリックして各トピックをドリルダウンすると、以下の詳細説明が表示されます。
* 発行
* 推奨アクション
* 役立つドキュメントへのリンク
* 関連メトリック
* データのログを記録する 

さまざまなチェックを実行した後、クラスターの現在の状態に基づいた診断レポートがインテリジェントに生成されます。 これはクラスターの問題を特定し、問題解決に向けた次の手順を調べる上で役立ちます。

![診断レポート](./media/concepts-diagnostics/diagnostic-report.png)

![展開された診断レポート](./media/concepts-diagnostics/node-issues.png)

## <a name="cluster-insights"></a>クラスター分析情報

**[Cluster Insights]\(クラスター分析情報\)** では、次の診断チェックを利用できます。

### <a name="cluster-node-issues"></a>クラスター ノードの問題

クラスター ノードの問題では、クラスターが予期しない動作をする原因となるノード関連の問題をチェックします。

- ノード対応性の問題
- ノードの障害
- リソース不足
- ノードの IP 構成の欠落
- ノード CNI の障害
- ノードが見つからない
- ノードの電源がオフ
- ノード認証エラー
- ノードの kube-proxy が古い

### <a name="create-read-update--delete-crud-operations"></a>作成、読み取り、更新、削除 (CRUD) の操作

CRUD の操作では、クラスターで問題の原因となる CRUD 操作をチェックします。

- 使用中サブネットの削除操作エラー
- ネットワーク セキュリティ グループの削除操作エラー
- 使用中ルート テーブルの削除操作エラー
- 被参照リソースのプロビジョニング エラー
- パブリック IP アドレスの削除操作エラー
- デプロイ クォータが原因のデプロイ失敗
- 組織のポリシーが原因の操作エラー
- サブスクリプション未登録
- VM 拡張機能のプロビジョニング エラー
- サブネット容量
- クォータ超過エラー

### <a name="identity-and-security-management"></a>ID とセキュリティ管理

ID とセキュリティ管理では、クラスターとの通信を妨げる可能性がある認証と承認のエラーを検出します。

- ノード承認エラー
- 401 エラー
- 403 エラー

## <a name="next-steps"></a>次のステップ

* [AKS Periscope](https://aka.ms/aksperiscope) を使用してログを収集すると、クラスターの問題のさらなるトラブルシューティングに役立ちます。

* AKS Day-2 Operations ガイドの[「トリアージのプラクティス」セクション](/azure/architecture/operator-guides/aks/aks-triage-practices)を参照してください。

* 質問またはフィードバックは、[UserVoice](https://feedback.azure.com/forums/914020-azure-kubernetes-service-aks) でタイトルに "[Diag]" を付けて投稿してください。