---
title: Azure Active Directory アプリケーション プロキシ アプリケーションの読み込みに時間がかかりすぎる
description: Azure Active Directory アプリケーション プロキシによるページ読み込みパフォーマンスの問題のトラブルシューティングを行う
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.openlocfilehash: 29dd0b1fd3cb03a165ab278c3d8867525a84566d
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "129988964"
---
# <a name="an-application-proxy-application-takes-too-long-to-load"></a>アプリケーション プロキシのアプリケーション読み込みに時間がかかりすぎる

この記事では、Azure AD アプリケーション プロキシのアプリケーションが読み込みに時間がかかる理由について説明します。 また、この問題の解決方法について説明します。

## <a name="overview"></a>概要
アプリケーションが動作していても、待ち時間が長いことがあります。 ネットワーク トポロジを調整すれば、待ち時間を短縮できることがあります。 さまざまなトポロジの評価については、[ネットワークに関する考慮事項のマニュアル](application-proxy-network-topology.md)をご覧ください。

ネットワーク トポロジの他には、パフォーマンス調整のために推奨できることは現在ありません。 アプリケーション プロキシ サービスが拡大されるにつれ、データ センターに物理的に近くなることがあります。 近くなることで待ち時間が改善されることがあります。 Azure データ センターの一覧については、[待機時間テスト ページ](http://www.azurespeed.com/Azure/Latency)をご覧ください。 

## <a name="next-steps"></a>次のステップ
[既存のオンプレミス プロキシ サーバーと連携する](application-proxy-configure-connectors-with-proxy-servers.md)
