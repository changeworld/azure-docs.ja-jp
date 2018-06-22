---
title: Azure CDN でカスタム HTTPS を有効にするために許可された認証機関 | Microsoft Docs
description: 独自の証明書を使用してカスタム ドメインで HTTPS を有効にする場合、その証明書を作成するためには許可された証明機関 (CA) を使用する必要があります。
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2018
ms.author: v-deasim
ms.custom: mvc
ms.openlocfilehash: 3c41ca7e375324ff784bf7bee347bb56400ddfbd
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/08/2018
ms.locfileid: "35237082"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Azure CDN でカスタム HTTPS を有効にするために許可された認証機関

**Azure CDN Standard from Microsoft** エンドポイント上の Azure Content Delivery Network (CDN) カスタム ドメインでは、[独自の証明書を使用して HTTPS 機能を有効にする](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates)場合、許可された証明機関 (CA) を使用して SSL 証明書を作成する必要があります。 許可されていない CA や自己署名証明書を使用すると、要求が拒否されます。

> [!NOTE]
> 独自の証明書を使用してカスタム HTTPS を有効にするオプションは、**Azure CDN Standard from Microsoft** プロファイルでのみ利用できます。 
>

次の CA は、独自の証明書を作成するときに使用できます。

- AddTrust External CA Root
- AP Root CA
- AP Root Certificate Authority 2013
- AP Root Certificate Authority 2014
- APCA-DM3P
- Autopilot Root CA
- Baltimore CyberTrust Root
- Class 3 Public Primary Certification Authority
- COMODO RSA Certification Authority
- COMODO RSA Domain Validation Secure Server CA
- D-TRUST Root Class 3 CA 2 2009
- DigiCert Cloud Services CA-1
- DigiCert Global Root CA
- DigiCert High Assurance CA-3
- DigiCert High Assurance EV Root CA
- DigiCert SHA2 High Assurance Server CA
- DigiCert SHA2 Secure Server CA
- GlobalSign
- GlobalSign Extended Validation CA - SHA256 - G2
- GlobalSign Organization Validation CA - G2
- GlobalSign Root CA
- Microsoft Authenticode(tm) Root Authority
- Microsoft Exchange Services CA 2015
- Microsoft Internal Corporate Root
- Microsoft IT ITO SSL CA 1
- Microsoft IT SSL SHA1
- Microsoft IT SSL SHA2
- Microsoft IT TLS CA 1
- Microsoft IT TLS CA 2
- Microsoft IT TLS CA 4
- Microsoft IT TLS CA 5
- Microsoft Root Authority
- Microsoft Root Certificate Authority
- Microsoft Root Certificate Authority 2010
- Microsoft Root Certificate Authority 2011
- Microsoft Secure Server CA 2011
- Microsoft Services Partner Root
- Microsoft Time Stamping Service Root
- Microsoft Windows Hardware Compatibility
- MSIT CA Z2
- MSIT Enterprise CA 1
- MSIT Enterprise CA 3
- Root Agency
- Symantec Class 3 EV SSL CA - G3
- Symantec Class 3 Secure Server CA - G4
- Symantec Enterprise Mobile Root for Microsoft
- Thawte Timestamping CA
- UTN-USERFirst-Object
- VeriSign Class 3 Extended Validation SSL CA
- VeriSign Class 3 Extended Validation SSL SGC CA
- VeriSign Class 3 Public Primary Certification Authority - G5
- VeriSign International Server CA - Class 3
- VeriSign Time Stamping Service Root
- VeriSign Universal Root Certification Authority
- WMSvc-SHA2-DALEDGE1008

