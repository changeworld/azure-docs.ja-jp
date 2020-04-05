---
title: Azure Dev Spaces と共に kubectl を使用する方法
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Azure Dev Spaces が有効になっている Azure Kubernetes Service クラスター上の開発スペース内で kubectl コマンドを使用する方法について説明します
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー, Helm, サービス メッシュ, サービス メッシュのルーティング, kubectl, k8s '
ms.openlocfilehash: 7530cde68b2ce8d06fb7b16a9a147f0f0bc8645c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "75438363"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Azure Dev Space で kubectl を使用する

Azure Dev Space の内部で Kubernetes クラスターにアクセスし、`kubectl` などの既存の Kubernetes ツールを使用することができます。

`az aks use-dev-spaces` コマンドを実行すると、`kubectl` 構成コンテキストが自動的に追加されるので、kubectl は Azure Dev Spaces の Kubernetes クラスターに既に接続されています。 例 :
- 現在のコンテキストを確認します: `kubectl config current-context`
- すべての使用可能なコンテキストを一覧表示します: `kubectl config get-contexts`。 
- コンテキストを変更します: `kubectl config use-context <context-name>`
- Kubernetes ダッシュボードの表示: `kubectl proxy` を実行した後、このコマンドが出力するアドレスをブラウザーで開きます (URL に `/ui` を追加して Kubernetes ダッシュボードに移動します)。
- *default* という名前の既定の Azure Dev Spaces スペースで実行中のサービスを一覧表示します: `kubectl get services --namespace=default`

