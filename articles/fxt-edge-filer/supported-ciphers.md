---
title: Azure FXT Edge Filer でサポートされている暗号化による暗号
description: FXT Edge Filer クラスターで使用される暗号化標準の一覧。
author: femila
ms.author: femila
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 05/20/2021
ms.openlocfilehash: 0c05e821ab19c4a617a8a6e13bd7f1c37331a661
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131033002"
---
# <a name="supported-encryption-standards-for-azure-fxt-edge-filer"></a>Azure FXT Edge Filer でサポートされている暗号化標準

このドキュメントでは、Azure FXT Edge Filer に必要な暗号化標準について説明します。 これらの標準は、オペレーティング システム バージョン 5.1.1.2 以降で実装されています。

これらの標準は、Azure FXT Edge Filer だけでなく、[Avere vFXT for Azure](../avere-vfxt/index.yml) にも適用されます。

Azure FXT Edge Filer のキャッシュまたは個々のノードに接続する管理またはインフラストラクチャ システムはすべて、これらの標準を満たす必要があります。

(クライアント マシンは NFS を使用してキャッシュをマウントします。そのため、これらの暗号化要件は適用されません。 その他の妥当な手段を使用してセキュリティを確保してください。)

## <a name="tls-standard"></a>TLS 標準

* TLS1.2 を有効にする必要があります
* SSL V2 と V3 を無効にする必要があります

TLS1.0 と TLS1.1 は、プライベート オブジェクト ストアとの下位互換性のためにどうしても必要な場合には使用できますが、プライベート ストレージを最新のセキュリティ標準にアップグレードすることをお勧めします。 詳細については、Microsoft カスタマー サービスおよびサポートにお問い合わせください。

## <a name="permitted-cipher-suites"></a>許可される暗号スイート

Azure FXT Edge Filer では、以下の TLS 暗号スイートをネゴシエートできます。

* ECDHE-ECDSA-AES128-GCM-SHA256
* ECDHE-ECDSA-AES256-GCM-SHA384
* ECDHE-RSA-AES128-GCM-SHA256
* ECDHE-RSA-AES256-GCM-SHA384
* ECDHE-ECDSA-AES128-SHA256
* ECDHE-ECDSA-AES256-SHA384
* ECDHE-RSA-AES128-SHA256
* ECDHE-RSA-AES256-SHA384

クラスター管理 HTTPS インターフェイス (コントロール パネル Web GUI および管理 RPC 接続に使用) は、上記の暗号スイートと TLS1.2 のみをサポートします。 管理インターフェイスに接続するときに、他のプロトコルや暗号スイートはサポートされません。

## <a name="ssh-server-access"></a>SSH サーバー アクセス

これらの標準は、これらの製品に埋め込まれている SSH サーバーに適用されます。

SSH サーバーでは、スーパーユーザー "root" としてリモート ログインすることは許可されません。 Microsoft カスタマー サービスおよびサポートのガイダンスの下で、リモート SSH アクセスが必要な場合は、シェルが制限されている SSH "admin" ユーザーとしてログインします。

以下の SSH 暗号スイートは、クラスター SSH サーバーで使用できます。 SSH を使用してクラスターに接続するクライアントに、これらの標準を満たす最新のソフトウェアがインストールされていることを確認してください。

### <a name="ssh-encryption-standards"></a>SSH 暗号化標準

| 種類 | サポート状況の値 |
|--|--|
| Ciphers | aes256-gcm@openssh.com</br> aes128-gcm@openssh.com</br> aes256-ctr</br> aes128-ctr |
| MAC | hmac-sha2-512-etm@openssh.com</br> hmac-sha2-256-etm@openssh.com</br> hmac-sha2-512</br> hmac-sha2-256 |
| KEX アルゴリズム | ecdh-sha2-nistp521</br> ecdh-sha2-nistp384</br> ecdh-sha2-nistp256</br> diffie-hellman-group-exchange-sha256 |

## <a name="next-steps"></a>次のステップ

* Azure FXT Edge Filer クラスターに[ストレージを追加](add-storage.md)する方法を確認する
* [コントロール パネルに接続](cluster-create.md#open-the-settings-pages)してクラスターを管理する
* [クライアントをマウント](mount-clients.md)してクラスターからデータにアクセスする
* 暗号化標準の詳細については、[サポートにお問い合わせください](support-ticket.md)
