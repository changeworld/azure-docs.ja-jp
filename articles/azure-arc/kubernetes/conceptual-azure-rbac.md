---
title: Azure RBAC - Azure Arc 対応 Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: この記事では、Azure Arc 対応 Kubernetes での Azure RBAC 機能の概念について説明します
ms.openlocfilehash: 7eb55ed819b6487697b5c2d64cdbabe2bbdae8a3
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450792"
---
# <a name="azure-rbac-on-azure-arc-enabled-kubernetes"></a>Azure Arc 対応 Kubernetes での Azure RBAC

Kubernetes の [ClusterRoleBinding および RoleBinding](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#rolebinding-and-clusterrolebinding) オブジェクト型は、Kubernetes でネイティブに承認を定義するのに役立ちます。 Azure RBAC では、Azure Active Directory (Azure AD) と Azure のロールの割り当てを使用して、クラスターでの承認チェックを制御できます。

この機能を使用すると、Azure でのロールの割り当てのすべての利点 (Azure リソースに対する Azure RBAC の変更をアクティビティ ログにすべて表示するなど) が、Azure Arc 対応 Kubernetes クラスターに適用できるようになります。

## <a name="architecture---azure-rbac-on-azure-arc-enabled-kubernetes"></a>アーキテクチャ - Azure Arc 対応 Kubernetes での Azure RBAC

[ ![Azure RBAC のアーキテクチャ](./media/conceptual-azure-rbac.png) ](./media/conceptual-azure-rbac.png#lightbox)

すべてのアクセス承認チェックを Azure の承認サービスにルーティングするために、Webhook サーバー ([ガード](https://github.com/appscode/guard)) がクラスターにデプロイされます。

クラスターの `apiserver` は [Webhook トークン認証](https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication)と [Webhook 承認](https://kubernetes.io/docs/reference/access-authn-authz/webhook/)を使用するように構成されています。これにより、`TokenAccessReview` および `SubjectAccessReview` 要求がガード Webhook サーバーにルーティングされます。 `TokenAccessReview` および `SubjectAccessReview` 要求は、`apiserver` に送信される Kubernetes リソースに対する要求によってトリガーされます。

ガードは、Azure の承認サービスに対して `checkAccess` 呼び出しを行い、要求元の Azure AD エンティティが、目的のリソースにアクセスできるかどうかを確認します。 

このアクセスを許可するロールが割り当てられている場合、承認サービスからガードに `allowed` 応答が送信されます。 次に、ガードは `apiserver` に `allowed` 応答を送信し、呼び出し元のエンティティが、要求された Kubernetes リソースにアクセスできるようにします。


このアクセスを許可するロールが割り当てられていない場合は、承認サービスからガードに `denied` 応答が送信されます。 ガードは `apiserver` に `denied` 応答を送信し、呼び出し元のエンティティに対し、要求されたリソースへの 403 (許可されていません) エラーを発出します。

## <a name="next-steps"></a>次の手順

* クイックスタートを使用して、[Kubernetes クラスターを Azure Arc に接続します](./quickstart-connect-cluster.md)。
* Azure Arc 対応 Kubernetes クラスターで、[Azure RBAC を設定します](./azure-rbac.md)。