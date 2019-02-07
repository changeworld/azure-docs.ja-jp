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
ms.topic: conceptual
ms.date: 11/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 0374db9ae207d3d9c713e6f1a2e2a25c96505b74
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2019
ms.locfileid: "55490415"
---
# <a name="what-is-federation-with-azure-ad"></a>Azure AD とのフェデレーションとは

フェデレーションとは、信頼が確立されたドメインのコレクションのことです。 信頼のレベルは異なる場合がありますが、通常は認証が含まれ、また、ほぼ常に承認が含まれます。 標準的なフェデレーションには、一連のリソースへの共有アクセスのために信頼が確立された多くの組織が含まれる場合があります。

オンプレミス環境と Azure AD とのフェデレーションを行い、認証と承認のためにこのフェデレーションを使用することができます。  このサインイン方法では、すべてのユーザー認証が確実にオンプレミスで行われます。  この方法では、管理者はより厳しいレベルのアクセス制御を実装することができます。 AD FS および PingFederate とのフェデレーションを使用できます。

![フェデレーション ID](./media/whatis-hybrid-identity/federated-identity.png)


> [!TIP]
> Active Directory フェデレーション サービス (AD FS) とのフェデレーションを使用する場合、AD FS インフラストラクチャで障害が発生した際のバックアップとして、オプションでパスワード ハッシュ同期を設定することもできます。


## <a name="next-steps"></a>次の手順

- [ハイブリッド ID とは](whatis-phs.md)
- [Azure AD Connect と Connect Health とは](whatis-azure-ad-connect.md)
- [パスワード ハッシュ同期とは](whatis-phs.md)
- [フェデレーションとは](whatis-fed.md)
- [シングル サインオンとは](how-to-connect-sso.md)
- [フェデレーションのしくみ](how-to-connect-fed-whatis.md)
- [PingFederate によるフェデレーション](how-to-connect-install-custom.md#configuring-federation-with-pingfederate)
