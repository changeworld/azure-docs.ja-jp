---
title: Azure Dev Spaces での kubectl の使用方法 | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: article
description: Azure のコンテナーとマイクロサービスを使用した迅速な Kubernetes 開発
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー
manager: douge
ms.openlocfilehash: 978274a6059a327c8596df6776eaa47a27ea4a34
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/15/2018
ms.locfileid: "42141351"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Azure Dev Space で kubectl を使用する

Azure Dev Space の内部で Kubernetes クラスターにアクセスし、`kubectl` などの既存の Kubernetes ツールを使用することができます。

`az aks use-dev-spaces` コマンドを実行すると、`kubectl` 構成コンテキストが自動的に追加されるので、kubectl は Azure Dev Spaces の Kubernetes クラスターに既に接続されています。 次に例を示します。
- 現在のコンテキストを確認します: `kubectl config current-context`
- すべての使用可能なコンテキストを一覧表示します: `kubectl config get-contexts`。 
- コンテキストを変更します: `kubectl config use-context <context-name>`
- Kubernetes ダッシュボードの表示: `kubectl proxy` を実行した後、このコマンドが出力するアドレスをブラウザーで開きます (URL に `/ui` を追加して Kubernetes ダッシュボードに移動します)。
- *default* という名前の既定の Azure Dev Spaces スペースで実行中のサービスを一覧表示します: `kubectl get services --namespace=default`

