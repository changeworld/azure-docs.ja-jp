---
title: IP とポート範囲を除外する
description: IP とポート範囲の除外を実装する
services: container-service
ms.topic: article
ms.date: 10/8/2021
ms.custom: mvc, devx-track-azurecli
ms.author: kochhars
ms.openlocfilehash: b2f43fc1ce78a10079bc37cdf77097caaa208bd6
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131066712"
---
# <a name="implement-ip-and-span-classx-x-first-x-lastport-range-exclusionspan"></a>IP と<span class="x x-first x-last">ポート範囲の除外</span>を実装する

アプリケーションから TCP ベースでデータが送られてきた場合、OSM でプログラミングされた `iptables` ルールによって既定でインターセプトされ、Envoy プロキシ サイドカーにリダイレクトされます。 OSM には、必要に応じてトラフィック インターセプトから除外する IP 範囲とポートの一覧を指定する手段があります。 IP とポートの範囲を除外する方法のガイダンスについては、[こちらのドキュメント](https://release-v0-11.docs.openservicemesh.io/docs/guides/traffic_management/iptables_redirection/)を参照してください。

> [!NOTE]
>
> - **オープンサービスメッシュ AKS アドオンの場合、ポート除外は `kubectl patch` を使用してインストールした後にのみ実装できます。OSM CLI `--set` フラグを使用してインストールする場合は実装できません。**
> - メッシュに含まれるアプリケーション ポッドで IMDS、Azure DNS、または Kubernetes API サーバーにアクセスする必要がある場合、ユーザーは上記のコマンドを使用し、グローバル送信 IP 範囲の一覧にこれらの IP アドレスを明示的に追加する必要があります。 Kubernetes API Server ポート除外の例は[こちら](https://release-v0-11.docs.openservicemesh.io/docs/guides/app_onboarding/#onboard-services)でご覧ください。
