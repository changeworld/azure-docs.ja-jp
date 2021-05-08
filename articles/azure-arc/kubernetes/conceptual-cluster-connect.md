---
title: クラスター接続 - Azure Arc 対応 Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: この記事では、Azure Arc 対応 Kubernetes のクラスター接続機能の概念的な概要を示します。
ms.openlocfilehash: 716ffe0c1f123c2a6abe8f407f6435e157ba5b6a
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450791"
---
# <a name="cluster-connect-on-azure-arc-enabled-kubernetes"></a>Azure Arc 対応 Kubernetes でのクラスター接続

Azure Arc 対応 Kubernetes "*クラスター接続*" 機能を使用すると、ファイアウォールで受信ポートを有効にしなくても、クラスターの `apiserver` に接続できます。 クラスターで実行されているリバース プロキシ エージェントでは、Azure Arc サービスとのセッションを送信方法で安全に開始できます。 

クラスター接続を使用すると、開発者は任意の場所からクラスターにアクセスして、対話型の開発とデバッグを行うことができます。 また、クラスターのユーザーと管理者は、どこからでもクラスターにアクセスしたり管理したりすることができます。 Azure Pipelines または GitHub Actions のホステッド エージェントやランナー、またはその他のホストされる CI/CD サービスを使用して、アプリケーションをオンプレミス クラスターにデプロイすることもできます。自己ホスト型のエージェントは必要ありません。

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="architecture"></a>アーキテクチャ

[ ![クラスター接続のアーキテクチャ](./media/conceptual-cluster-connect.png) ](./media/conceptual-cluster-connect.png#lightbox)

クラスター側では、エージェントの Helm グラフの一部としてデプロイされたリバース プロキシ エージェント (`clusterconnect-agent` と呼ばれる) により、Azure Arc サービスに対して発信呼び出しが行われて、セッションが確立されます。

ユーザーが `az connectedk8s proxy` を呼び出すと、次の処理が行われます。
1. Azure Arc プロキシ バイナリがダウンロードされ、クライアント コンピューター上のプロセスとしてスピンアップされます。 
1. Azure Arc プロキシにより、`az connectedk8s proxy` が呼び出された Azure Arc 対応 Kubernetes クラスターに関連付けられている `kubeconfig` ファイルがフェッチされます。
    * Azure Arc プロキシにより、呼び出し元の Azure アクセス トークンと Azure Resource Manager ID 名が使用されます。 
1. Azure Arc プロキシによってコンピューターに保存された `kubeconfig` ファイルにより、サーバーの URL が Azure Arc プロキシ プロセスのエンドポイントがポイントされます。

ユーザーがこの `kubeconfig` ファイルを使用して要求を送信すると、次の処理が行われます。
1. Azure Arc プロキシにより、要求を受信するエンドポイントが Azure Arc サービスにマップされます。 
1. その後、Azure Arc サービスにより、クラスターで実行されている `clusterconnect-agent` に要求が転送されます。 
1. `clusterconnect-agent` により、要求が `kube-aad-proxy` コンポーネントに渡されます。これにより、呼び出し元のエンティティで Azure AD 認証が実行されます。 
1. Azure AD 認証後、`kube-aad-proxy` によって Kubernetes の "[ユーザーの偽装](https://kubernetes.io/docs/reference/access-authn-authz/authentication/#user-impersonation)" 機能を使用して、要求がクラスターの `apiserver` に転送されます。

## <a name="next-steps"></a>次の手順

* クイックスタートを利用して、[Kubernetes クラスターを Azure Arc に接続](./quickstart-connect-cluster.md)する。
* クラスター接続を使用して、任意の場所から安全に[クラスターにアクセスする](./cluster-connect.md)。