---
title: Azure Active Directory の検証可能な資格情報の新機能 (プレビュー)
description: Azure Active Directory の検証可能な資格情報の最新の更新
author: barclayn
manager: karenh444
ms.service: active-directory
ms.subservice: verifiable-credentials
ms.topic: reference
ms.date: 10/08/2021
ms.author: barclayn
ms.openlocfilehash: 4a00a787e61e6de2eda5753262b94d8a316c9b01
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131440509"
---
# <a name="whats-new-in-azure-active-directory-verifiable-credentials-preview"></a>Azure Active Directory の検証可能な資格情報の新機能 (プレビュー)

この記事では、Azure Active Directory (Azure AD) の検証可能な資格情報サービスの最新の機能と機能強化、変更を紹介しています。

## <a name="october-2021"></a>2021 年 10 月

[要求サービス REST API](get-started-request-api.md) を使用して、ご使用の任意のプログラミング言語から資格情報の発行と検証を行うことができるアプリケーションを作成できるようになりました。 この新しい REST API では、強化された抽象化レイヤーと、Azure AD の検証可能な資格情報サービスへの統合が提供されています。

この API をすぐに使い始めることをお勧めします。NodeJS SDK は今後数か月以内に非推奨となる予定です。 ドキュメントとサンプルでは今後要求サービス REST API が使用されます。 詳細については、「[要求サービス REST API (プレビュー)](get-started-request-api.md)」を参照してください。

## <a name="april-2021"></a>2021 年 4 月

Azure AD で、[検証可能な資格情報](decentralized-identifier-overview.md)を発行できるようになりました。 このサービスは、雇用、教育、その他のクレームの証拠を提示する必要がある場合に役立ち、資格情報を共有するタイミングと共有対象者を資格情報の所有者が決定できます。 各資格情報は、ユーザーが所有および管理する分散化 ID に関連付けられた暗号化キーを使用して署名されます。
