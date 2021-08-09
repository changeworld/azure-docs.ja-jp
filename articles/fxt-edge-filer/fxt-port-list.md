---
title: Azure FXT Edge Filer のポートの一覧
description: FXT クラスター環境で使用される TCP/UDP ポートの一覧
author: ekpgh
ms.author: v-erkel
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 05/20/2021
ms.openlocfilehash: 5067e60a45025ae6c37af63df4e1392e9ae52709
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/03/2021
ms.locfileid: "111358486"
---
# <a name="required-network-ports"></a>必要なネットワーク ポート

この一覧は、FXT クラスター環境で必要な TCP/UDP ポートを示しています。 これらのポートにアクセスできるように、使用するファイアウォールを構成してください。

お使いのシステムの具体的な要件は、使用するバックエンド ストレージの種類によって異なります。

詳細については、Microsoft サービスおよびサポートにお問い合わせください。

## <a name="api-ports"></a>API ポート

| Direction | 種類 | ポート番号 | Protocol |
|-----------|------|-------------|----------|
| 受信   | TCP  | 22          | SSH      |
| 送信  | TCP  | 80          | HTTP     |
| 受信および送信  | TCP  | 443         | HTTPS    |

## <a name="nfs-ports"></a>NFS ポート

受信 NFS ポート:

| 種類    | ポート番号 | サービス  |
|---------|-------------|----------|
| TCP/UDP | 111         | RPCBIND  |
| TCP/UDP | 2049        | NFS      |
| TCP/UDP | 4045        | NLOCKMGR |
| TCP/UDP | 4046        | MOUNTD   |
| TCP/UDP | 4047        | 状態   |

送信 NFS ポート:

| 種類    | ポート番号 | サービス  |
|---------|-------------|----------|
| TCP/UDP | 111         | RPCBIND  |
| TCP/UDP | 2049        | NFS      |

送信 NFS ポートのトラフィックは、コア ファイラーとして使用されるストレージの種類によって異なります。 (ポート 2049 を使用しないシステムもありますが、一般的に使用されるため、ここに記載しています。 すべてのクラスターは、ポート 111 でのアクセスが必要です)。お使いのストレージ システムのドキュメントを確認するか、「[追加のポートの要件](#additional-port-requirements)」で後述するクエリ手法を使用してください。

FXT ノードからの一部の送信 NFS トラフィックでは、エフェメラル ポートを使用します。 上記の既知のポートからの送信 FXT トラフィックは、トランスポート レベルで制限しないでください。

## <a name="smb-ports"></a>SMB ポート

| Direction | 種類 | ポート番号 | Protocol |
|-----------|------|-------------|----------|
| 受信および送信  | UDP  | 137         | NetBIOS  |
| 受信   | UDP  | 138         | NetBIOS  |
| 受信および送信  | TCP  | 139         | SMB      |
| 受信および送信  | TCP  | 445         | SMB      |

<!--| Outbound  | UDP  | 137         | NetBIOS  | 
| Outbound  | TCP  | 139         | SMB      |
| Outbound  | TCP  | 445         | SMB      |
-->

## <a name="general-traffic-ports"></a>一般的なトラフィック ポート

| Direction | 種類    | ポート番号 | Protocol |
|-----------|---------|-------------|----------|
| 送信  | TCP/UDP | 53          | DNS      |
| 送信  | TCP/UDP | 88          | Kerberos |
| 送信  | UDP     | 123         | NTP      |
| 送信  | TCP/UDP | 389         | LDAP     |
| 送信  | TCP     | 686         | LDAPS    |

## <a name="additional-port-requirements"></a>追加のポートの要件

コア ファイラーは、追加のポートでのアクセスを必要とする場合があります。 この要件は、使用されるストレージの種類によって異なります。

`rpcinfo` コマンドを使用して、特定のサーバーで使用されているポートを確認できます。 ファイアウォールに接続されていないクライアント システムから次のコマンドを発行します。

`rpcinfo -p <server_IP_address>`
