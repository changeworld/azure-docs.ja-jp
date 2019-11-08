---
title: Azure Blockchain Tokens のアカウント管理
description: Azure Blockchain Tokens のアカウント管理を使うと、グループを作成し、ブロックチェーン アカウントをリンクしてブロックチェーン アクションへのアクセスを制御できます。
services: azure-blockchain
author: PatAltimore
ms.author: patricka
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: azure-blockchain
ms.reviewer: brendal
ms.openlocfilehash: 91dcadd3c7704b7b8c6cab45005bc83e1bba18bb
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73579775"
---
# <a name="azure-blockchain-tokens-account-management"></a>Azure Blockchain Tokens のアカウント管理

[!INCLUDE [Preview note](./includes/preview.md)]

ブロックチェーン ソリューションの場合、Azure Blockchain Tokens サービスを使って作成されたトークンに対して、さまざまなレベルのアクセスがユーザーに求められることがあります。 ほとんどのブロックチェーン シナリオでは、台帳に存在するさまざまなブロックチェーン アカウントを計画し、デプロイする必要があります。 また、参加者全体のアクセスを管理する必要もあります。 Azure Blockchain Tokens のアカウント管理を使うと、グループを作成し、ブロックチェーン アカウントをリンクしてブロックチェーン アクションへのアクセスを制御できます。

## <a name="blockchain-networks"></a>ブロックチェーン ネットワーク

Azure Blockchain Tokens を使うと、一連のブロックチェーン ネットワークにわたってトークンをデプロイおよび管理できます。 1 つのブロックチェーン台帳または複数のブロックチェーン台帳をサービスに接続できます。

## <a name="accounts"></a>アカウント

Azure Blockchain Tokens に接続されたブロックチェーン ネットワークの場合、サービスによってアカウントの秘密キーと公開キーのペアが作成および管理され、トランザクションの署名と送信が実行されます。 また、Azure Blockchain Tokens には、アカウントを台帳の公開キー ID と照合する ID マッピングも用意されています。

## <a name="groups"></a>グループ

グループを使うと、接続されたネットワーク全体で多数のブロックチェーン アカウントを管理できます。 ディレクトリ内のどのアプリケーションとユーザーが、Azure Blockchain Tokens API を介してアカウントを使用できるかを追跡および監査できます。 たとえば、さまざまな基幹業務またはさまざまなロールとブロックチェーン トークンへのアクセスを表す一連のアカウントをグループ化できます。

また、グループを Azure Active Directory ユーザーまたはサービス プリンシパルに関連付けることもできます。このプリンシパルには、グループとその関連アカウントへのアクセス許可があります。  

## <a name="next-steps"></a>次の手順

利用できる [Azure Blockchain Tokens テンプレート](templates.md)について詳しく確認します。
