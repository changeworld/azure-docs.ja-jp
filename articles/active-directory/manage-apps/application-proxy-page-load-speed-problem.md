---
title: アプリケーション プロキシのアプリケーション読み込みに時間がかかりすぎる | Microsoft Docs
description: Azure AD アプリケーション プロキシによるページ読み込みパフォーマンスの問題をトラブルシューティングする
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: ad2190f3bfa9e943258a55a8b8ecdff429d6576e
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55165780"
---
# <a name="an-application-proxy-application-takes-too-long-to-load"></a>アプリケーション プロキシのアプリケーション読み込みに時間がかかりすぎる

この記事では、Azure AD アプリケーション プロキシのアプリケーションが読み込みに時間がかかる理由について説明します。 また、この問題の解決方法について説明します。

## <a name="overview"></a>概要
アプリケーションが動作していても、待ち時間が長いことがあります。 ネットワーク トポロジを調整すれば、待ち時間を短縮できることがあります。 さまざまなトポロジの評価については、[ネットワークに関する考慮事項のマニュアル](application-proxy-network-topology.md)をご覧ください。

ネットワーク トポロジの他には、パフォーマンス調整のために推奨できることは現在ありません。 アプリケーション プロキシ サービスが拡大されるにつれ、データ センターに物理的に近くなることがあります。 近くなることで待ち時間が改善されることがあります。 Azure データ センターの一覧については、[待機時間テスト ページ](http://www.azurespeed.com/Azure/Latency)をご覧ください。 

アプリケーション プロキシ サービスを使用したデータ センターは、[コネクタ ポート テスト ツール](https://aadap-portcheck.connectorporttest.msappproxy.net/)で見つけることができます。 

## <a name="feedback-on-application-proxy-data-center-locations"></a>アプリケーション プロキシ データ センターの場所に関するフィードバック 
今のところはアプリケーション プロキシが含まれていないがお客様にとって待機時間の大幅な短縮につながる Azure データ センターがあるかもしれません。 データ センターの場所を aadapfeedback@microsoft.com に送信します。 Microsoft は皆様からのフィードバックを拡張計画に利用します。

Microsoft は、待機時間を改善するための追加機能に取り組んでいます。 改善策が利用可能になり次第、ドキュメントが更新されます。

## <a name="next-steps"></a>次の手順
[既存のオンプレミス プロキシ サーバーと連携する](application-proxy-configure-connectors-with-proxy-servers.md)
