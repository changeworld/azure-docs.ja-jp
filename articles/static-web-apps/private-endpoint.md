---
title: Azure Static Web Apps でのプライベート エンドポイントの構成
description: Azure Static Web Apps でプライベート エンドポイント アクセスを構成する方法について説明します。
services: static-web-apps
author: burkeholland
ms.author: buhollan
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 7/28/2021
ms.openlocfilehash: 8d6f5e019852200068d4db342ef4ab533d8779b2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779216"
---
# <a name="configure-private-endpoint-in-azure-static-web-apps"></a>Azure Static Web Apps でのプライベート エンドポイントの構成

プライベート エンドポイント (プライベート リンクとも呼びます) を使用して、静的 Web アプリへのアクセスを制限し、プライベート ネットワーク以外からのアクセスを禁止することができます。

> [!NOTE]
> Static Web Apps でのプライベート エンドポイントのサポートは現在、プレビュー段階です。

## <a name="how-it-works"></a>しくみ

Azure Virtual Network (VNet) は、従来のデータ センターにあるものと同様のネットワークですが、VNet 内のリソースは、Microsoft のバックボーン ネットワーク上で互いに安全に通信します。

Static Web Apps にプライベート エンドポイントを構成すると、VNet のプライベート IP アドレスを使用できるようになります。 このリンクが作成されると、静的 Web アプリが VNet に統合されます。 その結果、静的 Web アプリはパブリック インターネットから利用できなくなり、Azure VNet 内のマシンからのアクセスのみが可能になります。

> [!NOTE]
> プライベート エンドポイントの背後に配置されたアプリケーションは、VNet が位置しているリージョンでしか利用できません。 その結果、複数のポイント オブ プレゼンスでアプリケーションを利用することはできなくなります。

> [!WARNING]
> 現在、プライベート エンドポイントでは、運用環境のみがセキュリティで保護されます。 ステージング環境のサポートは、今後のサービス更新プログラムで追加される予定です。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。
  - [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Azure VNet](../virtual-network/quick-create-portal.md)。
- [Azure Static Web Apps](./get-started-portal.md) を使用してデプロイされ、Standard ホスティング プランを使用するアプリケーション。

## <a name="create-a-private-endpoint"></a>プライベート エンドポイントの作成

ここでは、静的 Web アプリのプライベート エンドポイントを作成します。

> [!IMPORTANT]
> プライベート エンドポイントを使用するには、Standard ホスティング プランで静的 Web アプリをデプロイする必要があります。 ホスティング プランは、サイド メニューの **[ホスティング プラン]** オプションから変更できます。

1. ポータルで静的 Web アプリを開きます。

1. サイド メニューから **[プライベート エンドポイント]** オプションを選択します。

1. **[追加]** をクリックします。

1. [Add Private Endpoint] (プライベート エンドポイントの追加) ダイアログで、次の情報を入力します。

   | 設定                         | 値                         |
   | ------------------------------- | ----------------------------- |
   | 名前                            | 「**myPrivateEndpoint**」と入力します。  |
   | サブスクリプション                    | サブスクリプションを選択します。     |
   | Virtual Network                 | 仮想ネットワークを選択します。  |
   | Subnet                          | サブネットを選択します。           |
   | プライベート DNS ゾーンとの統合 | 既定値の **[はい]** のままにします。 |

   :::image type="content" source="media/create-private-link-dialog.png" alt-text="./media/create-private-link-dialog.png":::

1. **[OK]** を選択します。

## <a name="testing-your-private-endpoint"></a>プライベート エンドポイントのテスト

アプリケーションは非公開になり、仮想ネットワークの内部からしかアクセスできなくなりました。 テストするには、仮想ネットワーク内に仮想マシンをセットアップし、サイトにアクセスします。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [プライベート エンドポイントの詳細](../private-link/private-endpoint-overview.md)
