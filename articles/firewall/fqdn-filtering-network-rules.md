---
title: ネットワーク ルールでの Azure Firewall の FQDN フィルタリング
description: ネットワーク ルールで Azure Firewall の FQDN フィルタリングを使用する方法
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 09/01/2021
ms.author: victorh
ms.openlocfilehash: 1aaea272e83bf39f2f4aa7e373b26ae28aadbda0
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123430326"
---
# <a name="use-fqdn-filtering-in-network-rules"></a>ネットワーク ルールでの FQDN フィルタリング

完全修飾ドメイン名 (FQDN) は、ホストまたは IP アドレスのドメイン名を表します。 Azure Firewall とファイアウォール ポリシーの DNS 解決に基づいて、ネットワーク ルールで FQDN を使用できます。 この機能を使用すると、任意の TCP/UDP プロトコル (NTP、SSH、RDP など) を使用して送信トラフィックをフィルター処理できます。 ネットワーク ルールで FQDN を使用するには、DNS プロキシを有効にする必要があります。 詳細については、「[Azure Firewall の DNS 設定](dns-settings.md)」を参照してください。

> [!NOTE]
> 仕様上、ネットワーク規則の FQDN フィルターではワイルドカードはサポートされていません

## <a name="how-it-works"></a>しくみ

組織で必要な DNS サーバー (Azure DNS または独自のカスタム DNS) を定義すると、選択した DNS サーバーに基づいて Azure Firewall によって FQDN が IP アドレスに変換されます。 この変換は、アプリケーションとネットワーク ルールの両方の処理で行われます。

新しい DNS 解決が行われると、新しい IP アドレスがファイアウォール規則に追加されます。 DNS サーバーによって返されなくなった古い IP アドレスは、15 分後に有効期限が切れます。 Azure Firewall 規則は、ネットワーク ルールの FQDN の DNS 解決から 15 秒ごとに更新されます。

### <a name="differences-in-application-rules-vs-network-rules"></a>アプリケーション ルールとネットワーク ルールの違い

- HTTP/S および MSSQL のアプリケーション ルールでの FQDN フィルタリングは、アプリケーション レベルの透過プロキシと SNI ヘッダーに基づいています。 そのため、同じ IP アドレスに解決される 2 つの FQDN を区別できます。 これは、ネットワーク ルールでの FQDN フィルタリングには当てはまりません。 

   可能な場合は、常にアプリケーション ルールを使用してください。
  - プロトコルが HTTP/S または MSSQL の場合は、FQDN フィルタリングにアプリケーション ルールを使用します。
  - AzureBackup、HDInsight などのサービスの場合は、FQDN タグが含まれるアプリケーション ルールを使用します。
  - 他のプロトコルの場合は、FQDN フィルタリング用のネットワーク規則を使用できます。

## <a name="next-steps"></a>次のステップ

[Azure Firewall の DNS 設定](dns-settings.md)
