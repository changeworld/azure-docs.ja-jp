---
title: IP 許可リストを使用して DRM ライセンスおよび AES キー配信へのアクセスを制限する
description: IP 許可リストを使用して、DRM および AES キーへのアクセスを制限する方法について説明します。
services: media-services
documentationcenter: ''
author: johndeu
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/11/2021
ms.author: johndeu
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: cc0a35c196956d1f6afaf3d3fb27b9f428bb9f6d
ms.sourcegitcommit: e1874bb73cb669ce1e5203ec0a3777024c23a486
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/16/2021
ms.locfileid: "112204809"
---
# <a name="restrict-access-to-drm-license-and-aes-key-delivery-using-ip-allowlists"></a>IP 許可リストを使用して DRM ライセンスおよび AES キー配信へのアクセスを制限する

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Media Services の[コンテンツ保護](./drm-content-protection-concept.md)と DRM の機能を使用してメディアを保護する際に、ネットワーク上の特定の IP 範囲のクライアント デバイスへのライセンスまたはキー要求の配信を制限しなければならない状況が発生する場合があります。 コンテンツの再生とキーの配信を制限する場合は、キー配信の IP 許可リストを使用できます。

また、許可リストを使用して、キー配信トラフィックへのすべてのパブリック インターネット アクセスを完全にブロックし、プライベート ネットワーク エンドポイントからのトラフィックのみを許可することもできます。

キー配信の IP 許可リストにより、DRM ライセンスと AES-128 キーの両方の配信が、指定された IP 許可リストの範囲内のクライアントに制限されます。

## <a name="setting-the-allowlist-for-key-delivery"></a>キー配信の許可リストの設定

キー配信の IP 許可リストの設定は、Media Services アカウント リソース上にあります。 新しい Media Services アカウントを作成するときに、[Media Services アカウント リソース](/rest/api/media/mediaservices/create-or-update)の **KeyDelivery** プロパティを使用して、許可される IP 範囲を制限できます。

**defaultAction** プロパティを "Allow" または "Deny" に設定することで、許可リストの範囲内のクライアントへのライセンスとキーの配信を制御できます。

**ipAllowList** プロパティは、[CIDR 表記](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#CIDR_notation)を使用した、単一の IPv4 アドレス、IPv4 の範囲、またはその両方の配列です。

## <a name="setting-the-allowlist-in-the-portal"></a>ポータルでの許可リストの設定

Azure portal には、キー配信の IP 許可リストを構成および更新するためのメソッドが用意されています。  Media Services アカウントに移動し、 **[設定]** の下の **[Key delivery]\(キー配信\)** メニューにアクセスします。

## <a name="next-steps"></a>次のステップ

- [アカウントの作成](./account-create-how-to.md)
- [DRM とコンテンツ保護の概要](./drm-content-protection-concept.md)
