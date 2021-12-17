---
title: Azure Arc を使用した Kubernetes 上の Event Grid - サポートを受ける
description: この記事では、Azure Arc を使用した Kubernetes 上の Event Grid の問題に対してサポートを受ける方法について説明します。
author: jfggdl
ms.author: jafernan
ms.subservice: kubernetes
ms.date: 05/25/2021
ms.topic: how-to
ms.openlocfilehash: 5914a0b76929919446e4a4deccc8aeb523bb5f15
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110388207"
---
# <a name="event-grid-on-kubernetes-with-azure-arc---get-support"></a>Azure Arc を使用した Kubernetes 上の Event Grid - サポートを受ける
この記事では、Azure Arc を使用した Kubernetes 上の Event Grid で問題が発生した場合にサポートを受ける方法について説明します。 

## <a name="how-to-create-a-support-request"></a>サポート リクエストを作成する方法
問題が発生した場合、こちらの手順に従い [Azure サポート リクエストを作成](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)します。

1. [問題の種類] で、 **[技術]** を選択します。
1. メッセージが表示されたら、用意されている値から [Azure サブスクリプション] を選択します。
1. **[サービス]** で **[すべてのサービス]** を選択します。
1. **[サービスの種類]** に、問題が発生している手順に応じて適切なサービスを入力します。
    1. クラスターを Azure に接続するときに問題が発生した場合は、「**Azure Arc 対応 Kubernetes**」と入力し、そのオプションを選択します。    
    1. インストール手順の他の手順で問題が発生した場合は、「**Azure Arc を使用した Kubernetes 上の Event Grid**」と入力し、そのオプションを選択します。 
1. **[リソース]** で適切な Azure リソースを選択します。該当するリソースがない場合は **[一般的な質問]** を選択します。
1. **[概要]** フィールドに、問題の簡潔な説明を入力します。
1. [Azure Arc へのクラスターの接続](../../azure-arc/kubernetes/quickstart-connect-cluster.md)で問題が発生した場合、[問題の種類] で **[クラスター接続]** を選択します。その他の問題が発生した場合、適切なオプションを選択します。 たとえば、Event Grid のデプロイ時、またはトピックやイベント サブスクリプションの作成時に問題が発生した場合は、 **[管理の問題]** を選択します。
1. [問題のサブタイプ] で、適切なオプションを選択します。
1. **「解決方法」** を参照してください。 [Solutions]\(解決策\) タブが表示されます。
1. 推奨される解決策を確認します。 適切な解決策が見つからない場合、または解決策で問題が解決しなかった場合、ページの下部にある **[次へ: 詳細]** を選択します。
1.  [詳細] ページで、要求された情報をフォームに入力します。 
1. **[Review + create]\(レビュー + 作成\)** を選択します。
1. 表示された情報を確認し、ページの下部にある **[作成]** を選択します。

## <a name="next-steps"></a>次の手順
Kubernetes 上の Event Grid について詳しく学習するために、[Azure Arc を使用した Kubernetes 上の Event Grid (プレビュー) - 概要](overview.md)に関する記事を参照してください。