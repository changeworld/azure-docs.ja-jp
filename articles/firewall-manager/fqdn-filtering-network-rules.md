---
title: ネットワーク ルールでの Azure Firewall Manager のフィルタリング (プレビュー)
description: ネットワーク ルールで FQDN フィルタリングを使用する方法
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: article
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 599620c5fcc3ad1802527bd66e2dbead1b97d11d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87079027"
---
# <a name="fqdn-filtering-in-network-rules-preview"></a>ネットワーク ルールでの FQDN フィルタリング (プレビュー)

> [!IMPORTANT]
> ネットワーク ルールでの FQDN フィルタリングは現在、パブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

完全修飾ドメイン名 (FQDN) は、ホストのドメイン名を表します。 ドメイン名は、1 つまたは複数の IP アドレスに関連付けられます。 アプリケーション ルールで FQDN と FQDN タグを許可またはブロックすることができます。 カスタム DNS および DNS プロキシ設定を使用して、ネットワーク ルールで FQDN フィルタリングを使用することもできます。

## <a name="how-it-works"></a>しくみ

Azure Firewall は、その DNS 設定を使用して FQDN を IP アドレスに変換し、Azure DNS またはカスタム DNS 構成に基づいてルール処理を実行します。

ネットワーク ルールで FQDN を使用するには、DNS プロキシを有効にする必要があります。 DNS プロキシを有効にしない場合は、信頼性の高いルール処理が危険にさらされます。 有効にすると、DNS トラフィックは Azure Firewall に送信されます。Azure Firewall では、カスタム DNS サーバーを構成することができます。 その後、ファイアウォールとクライアントでは、構成された同じ DNS サーバーが使用されます。 DNS プロキシが有効になっていない場合、クライアントとファイアウォールで異なるサーバーが名前解決に使用される可能性があるため、Azure Firewall では異なる応答が生成される可能性があります。 クライアントとファイアウォールで異なる DNS 応答を受信する場合、ネットワーク ルールでの FQDN のフィルター処理に問題があるか、整合性がない可能性があります。

## <a name="next-steps"></a>次のステップ

[Azure Firewall の DNS 設定](dns-settings.md)
