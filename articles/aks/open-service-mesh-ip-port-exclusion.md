---
title: IP とポート範囲を除外する
description: IP とポート範囲の除外を実装する
services: container-service
ms.topic: article
ms.date: 10/8/2021
ms.custom: mvc, devx-track-azurecli
ms.author: kochhars
ms.openlocfilehash: b12ec518aa46800778973524b4302ebed083d38f
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007020"
---
# <a name="implement-ip-and-span-classx-x-first-x-lastport-range-exclusionspan"></a>IP と<span class="x x-first x-last">ポート範囲の除外</span>を実装する

アプリケーションから TCP ベースでデータが送られてきた場合、OSM でプログラミングされた `iptables` ルールによって既定でインターセプトされ、Envoy プロキシ サイドカーにリダイレクトされます。 OSM には、必要に応じてトラフィック インターセプトから除外する IP 範囲とポートの一覧を指定する手段があります。 IP とポートの範囲を除外する方法のガイダンスについては、[こちらのドキュメント](https://release-v0-11.docs.openservicemesh.io/docs/guides/traffic_management/iptables_redirection/)を参照してください。 

**Open Service Mesh AKS アドオンの場合、ポート除外は `kubectl patch` を使用してインストールした後にのみ実装できます。OSM CLI `--set` フラグを使用してインストールするときは実装できません。**

> [!NOTE]
> メッシュに含まれるアプリケーション ポッドで IMDS、Azure DNS、または Kubernetes API サーバーにアクセスする必要がある場合、ユーザーは上記のコマンドを使用し、グローバル送信 IP 範囲の一覧にこれらの IP アドレスを明示的に追加する必要があります。 Kubernetes API Server ポート除外の例は[こちら](https://release-v0-11.docs.openservicemesh.io/docs/guides/app_onboarding/#onboard-services)でご覧ください。
