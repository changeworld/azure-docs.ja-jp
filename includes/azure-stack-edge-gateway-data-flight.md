---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: f79081d506db6225b9ebef25674aad136e342ecf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96465731"
---
移動中のデータの場合:

- デバイスと Azure の間を移動するデータには標準トランスポート層セキュリティ (TLS) 1.2 が使用されます。 TLS 1.1 以前へのフォールバックはありません。 TLS 1.2 がサポートされていない場合は、エージェント通信がブロックされます。 TLS 1.2 はまた、ポータルや SDK の管理にも必要です。
- クライアントがブラウザーのローカル Web UI 経由でデバイスにアクセスする場合は、標準 TLS 1.2 が既定のセキュリティで保護されたプロトコルとして使用されます。

  - ベスト プラクティスとして、TLS 1.2 を使用するようにブラウザーを構成することをお勧めします。
  - お使いのデバイスでは TLS 1.2 のみがサポートされ、それより古いバージョン (TLS 1.1 も TLS 1.0 も) はサポートされません。
- データ サーバーからデータをコピーする場合は、そのデータを保護するために暗号化付き SMB 3.0 を使用することをお勧めします。
