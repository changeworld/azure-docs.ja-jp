---
title: 自己署名から P2S ゲートウェイ対応の公開 CA 証明書に移行する | Azure VPN Gateway | Microsoft Docs
description: この記事を利用して、P2S ゲートウェイ対応の新しい公開 CA 証明書に正常に移行できます。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: cherylmc
ms.openlocfilehash: ac1ae4125418a9c0b3e9587cd03a44e752ac8f82
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56236959"
---
# <a name="transition-from-self-signed-to-public-ca-certificates-for-p2s-gateways"></a>自己署名から P2S ゲートウェイ対応の公開 CA 証明書に移行する

Azure VPN Gateway では、P2S 接続用のゲートウェイに対して自己署名証明書が発行されなくなりました。 発行された証明書は現在、公的な証明機関 (CA) によって署名されています。 ただし、古いゲートウェイではまだ、自己署名証明書を使用している場合があります。 これらの自己署名証明書は有効期間の終了が近づいており、公開 CA 証明書へ移行する必要があります。

以前は、ゲートウェイ対応の自己署名証明書は 18 か月ごとに更新する必要がありました。 その後、VPN クライアント構成ファイルが生成され、すべての P2S クライアントに再展開される必要がありました。 公開 CA 証明書に移行すると、この制限はなくなります。 証明書の移行に加えて、この変更によってプラットフォームの強化、メトリックの向上、および安定性の改善も実現します。

古いゲートウェイのみが、この変更の影響を受けます。 お使いのゲートウェイ証明書を移行する必要がある場合は、Azure portal 内で通信またはトーストを受信します。 この記事の手順を使用して、お使いのゲートウェイが影響を受けるかどうかを判断するためのチェックをすることが可能です。

>[!IMPORTANT]
>移行は、2019 年 3 月 12 日 18 時 00 分 (UTC) から開始するようにスケジュールされています。 別の時間枠が好ましい場合は、サポート ケースを作成できます。 少なくとも 24 時間前までに、要求を行って最終処理を完了してください。  次のいずれかの時間枠を要求できます。
>
>* 2 月 25 日 06時 00 分 (UTC)
>* 2 月 25 日 18時 00 分 (UTC)
>* 3 月 1 日 06時 00 分 (UTC)
>* 3 月 1 日 18時 00 分 (UTC)
>
>**残りのすべてのゲートウェイでは、2019年 3 月 12 日 18時 00 分 (UTC) に移行を開始します**。
>

## <a name="1-verify-your-certificate"></a>1.証明書を確認する

1. この更新によって影響を受けるかどうかを判断するためのチェックを行います。 [この記事](point-to-site-vpn-client-configuration-azure-cert.md)の手順を使用して、現在の VPN クライアント構成をダウンロードします。

2. zip ファイルを開くか、または抽出し、"Generic" フォルダーを閲覧します。 Generic フォルダー内には、2 つのファイルが見つかります。そのうち 1 つは、*VPNSettings.xml* です。
3. 任意の xml ビューア―/エディターで *VPNSettings.xml* を開きます。 xml ファイル内で、次のフィールドを検索します。

  * `<ServerCertRootCn>DigiCert Global Root CA</ServerCertRootCn>`
  * `<ServerCertIssuerCn>DigiCert Global Root CA</ServerCertIssuerCn>`
4. *ServerCertRotCn* および *ServerCertIssuerCn* が "DigiCert Global Root CA" になっている場合は、この更新による影響を受けず、この記事の手順を進める必要はありません。 しかし、これ以外が表示された場合は、お使いのゲートウェイ証明書は更新の対象となり、移行されます。

## <a name="2-check-certificate-transition-schedule"></a>2.証明書の移行スケジュールを確認する

証明書が更新の対象になる場合、お使いのゲートウェイ証明書は移行されます。 移行時間については、「**重要**」の注記をご覧ください。 更新後、P2S クライアントでは、古いプロファイルを使用して接続できなくなります。 新しい VPN クライアント プロファイルを生成して、クライアントにインストールする必要があります。

## <a name="3-generate-vpn-client-configuration-profile"></a>3.VPN クライアント構成プロファイルを生成する

証明書が移行された後に、新しい VPN プロファイル (VPN クライアント構成ファイル) を Azure portal からダウンロードします。 手順については、[VPN クライアント構成ファイルの作成とインストール](point-to-site-vpn-client-configuration-azure-cert.md)に関する記事をご覧ください。 新しいクライアント証明書を生成する必要はありません。

## <a name="4-deploy-vpn-client-profile"></a>4.VPN クライアント プロファイルを展開する

すべてのポイント対サイト VPN クライアントに新しいプロファイルを展開します。 ポイント対サイト接続用の新しい VPN プロファイルがダウンロードされ、クライアント デバイスに展開されるまで、VPN クライアントでは接続が失われます。 VPN クライアント コンピューター上に既にインストールされているクライアント証明書を再発行する必要はありません。

## <a name="5-connect-the-vpn-client"></a>5.VPN クライアントを接続する

通常どおり、VPN クライアントから Azure に接続します。
