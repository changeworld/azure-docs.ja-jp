---
title: Azure AD とのフェデレーションとは | Microsoft Docs
description: Azure AD とのフェデレーションについて説明します。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 11/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd7a5b186ebc98babe1baacc9f56f39dc8fbc093
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2020
ms.locfileid: "80049370"
---
# <a name="what-is-federation-with-azure-ad"></a>Azure AD とのフェデレーションとは

フェデレーションとは、信頼が確立されたドメインのコレクションのことです。 信頼のレベルは異なる場合がありますが、通常は認証が含まれ、また、ほぼ常に承認が含まれます。 標準的なフェデレーションには、一連のリソースへの共有アクセスのために信頼が確立された多くの組織が含まれる場合があります。

オンプレミス環境と Azure AD とのフェデレーションを行い、認証と承認のためにこのフェデレーションを使用することができます。  このサインイン方法では、すべてのユーザー認証が確実にオンプレミスで行われます。  この方法では、管理者はより厳しいレベルのアクセス制御を実装することができます。 AD FS および PingFederate とのフェデレーションを使用できます。

![フェデレーション ID](./media/whatis-hybrid-identity/federated-identity.png)


> [!TIP]
> Active Directory フェデレーション サービス (AD FS) とのフェデレーションを使用する場合、AD FS インフラストラクチャで障害が発生した際のバックアップとして、オプションでパスワード ハッシュ同期を設定することもできます。


## <a name="next-steps"></a>次の手順

- [ハイブリッド ID とは](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity)
- [Azure AD Connect と Connect Health とは](whatis-azure-ad-connect.md)
- [パスワード ハッシュ同期とは](whatis-phs.md)
- [フェデレーションとは](whatis-fed.md)
- [シングル サインオンとは](how-to-connect-sso.md)
- [フェデレーションのしくみ](how-to-connect-fed-whatis.md)
- [PingFederate によるフェデレーション](how-to-connect-install-custom.md#configuring-federation-with-pingfederate)
