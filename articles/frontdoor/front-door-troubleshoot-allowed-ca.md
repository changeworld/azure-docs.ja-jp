---
title: Azure Front Door Service でカスタム HTTPS を有効にするために許可された CA
description: 独自の証明書を使用してカスタム ドメインで HTTPS を有効にする場合、その証明書を作成するためには許可された証明機関 (CA) を使用する必要があります。
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.assetid: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2018
ms.author: sharadag
ms.openlocfilehash: 62420889d9a4cb1e9d1c570a0845c704fca56cb3
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184584"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door-service"></a>Azure Front Door Service でカスタム HTTPS を有効にするために許可された認証機関

Azure Front Door Service のカスタム ドメインでは、[独自の証明書を使用して HTTPS 機能を有効にする](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate)場合、許可された証明機関 (CA) を使用して SSL 証明書を作成する必要があります。 許可されていない CA や自己署名証明書を使用すると、要求が拒否されます。

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
