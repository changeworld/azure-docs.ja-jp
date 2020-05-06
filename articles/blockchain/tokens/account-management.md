---
title: Azure Blockchain Tokens のアカウント管理
description: Azure Blockchain Tokens のアカウント管理を使うと、グループを作成し、ブロックチェーン アカウントをリンクしてブロックチェーン アクションへのアクセスを制御できます。
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: 9931ef59e613501ba6feaedf3ac5d4721f0df752
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "74326097"
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

## <a name="next-steps"></a>次のステップ

利用できる [Azure Blockchain Tokens テンプレート](templates.md)について詳しく確認します。
