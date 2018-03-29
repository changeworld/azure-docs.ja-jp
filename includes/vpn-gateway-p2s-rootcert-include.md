---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b2a96aad793d956b3ea3de06fba74bcf68e50786
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
---
エンタープライズ ソリューション (推奨) を使って生成されたルート証明書を使用するか、または自己署名証明書を生成してください。 ルート証明書の作成後、(秘密キーではなく) 公開証明書データを、Base-64 でエンコードされた X.509 .cer ファイルとしてエクスポートし、公開証明書データを Azure にアップロードします。

* **エンタープライズ証明書:** エンタープライズ ソリューションを使用している場合、既存の証明書チェーンを使うことができます。 使用するルート証明書の .cer ファイルを取得します。
* **自己署名ルート証明書:** エンタープライズ証明書ソリューションを使用していない場合は、自己署名ルート証明書を作成する必要があります。 P2S 証明書に関する以下のいずれかの記事の手順に従うことが重要です。 そうしないと、作成する証明書と P2S 接続との互換性がなくなり、クライアントが接続しようとすると接続エラー メッセージを受信します。 Azure PowerShell、MakeCert、または OpenSSL を使用できます。 以下の各記事の手順で、互換性のある証明書が生成されます。

  * [Windows 10 PowerShell の手順](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md): これらの手順で証明書を生成するには、Windows 10 および PowerShell が必要です。 ルート証明書から生成されるクライアント証明書は、サポートされている任意の P2S クライアントにインストールすることができます。
  * [MakeCert の手順](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): 証明書を生成するために使用する Windows 10 コンピューターにアクセスできない場合は、MakeCert を使用します。 MakeCert は非推奨になりましたが、まだ MakeCert を使用して証明書を生成することができます。 ルート証明書から生成されるクライアント証明書は、サポートされている任意の P2S クライアントにインストールすることができます。
