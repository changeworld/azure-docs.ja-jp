---
title: Azure CLI のサンプル - Azure SignalR Service
description: Azure CLI のサンプル - Azure SignalR Service
author: sffamily
ms.service: signalr
ms.topic: sample
ms.date: 04/20/2018
ms.author: zhshang
ms.custom: mvc
ms.openlocfilehash: a39f0abf4f3a8cc30d6f8f83dcad7e9069f348e4
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/11/2018
ms.locfileid: "53258782"
---
# <a name="azure-cli-samples"></a>Azure CLI のサンプル

次の表は、Azure CLI を使った Azure SignalR Service 用 Bash スクリプトへのリンクをまとめたものです。

| | |
|-|-|
|**作成**||
| [Create a new SignalR Service and resource group (新しい SignalR Service とリソース グループの作成)](scripts/signalr-cli-create-service.md) | ランダムな名前を使って、新しいリソース グループ内に新しい Azure SignalR Service リソースを作成します。  |
|**統合**||
| [Create a new SignalR Service and Web App configured to use SignalR (新しい SignalR Service と、SignalR を使うよう構成された Web アプリの作成)](scripts/signalr-cli-create-with-app-service.md) | ランダムな名前を使って、新しいリソース グループ内に新しい Azure SignalR Service リソースを作成します。 また、SignalR サービスを使用する ASP.NET Core Web アプリをホストするための、新しい Web アプリと App Service プランを追加します。 この Web アプリは、新しい SignalR サービス リソースに接続するためのアプリ設定を使って構成されます。 |
| [Create a new SignalR Service and Web App configured to use SignalR, and GitHub OAuth (新しい SignalR Service と、SignalR を使うよう構成された Web アプリの作成)](scripts/signalr-cli-create-with-app-service-github-oauth.md) | ランダムな名前を使って、新しいリソース グループ内に新しい Azure SignalR Service リソースを作成します。 また、SignalR サービスを使用する ASP.NET Core Web アプリをホストするための、新しい Azure Web アプリとホスティング プランを追加します。 この Web アプリは、新しい SignalR サービス リソースへの接続文字列と、[GitHub 認証](https://developer.github.com/v3/guides/basics-of-authentication/) ([認証チュートリアル](signalr-authenticate-oauth.md)で説明されています) をサポートするためのクライアント シークレットを使ったアプリ設定によって構成されます。 また、この Web アプリはローカル Git リポジトリのデプロイ ソースを使うように構成されます。 |
| | |