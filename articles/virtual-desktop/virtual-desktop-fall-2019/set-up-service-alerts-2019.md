---
title: Azure Virtual Desktop (クラシック) のサービス アラートを設定する - Azure
description: Azure Virtual Desktop (クラシック) のサービス通知を受信するように Azure Service Health を設定する方法。
author: Heidilohr
ms.topic: tutorial
ms.date: 05/27/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 452dd8ce28589d473b730e1215e86c67fa490e48
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/09/2021
ms.locfileid: "111751741"
---
# <a name="tutorial-set-up-service-alerts-for-azure-virtual-desktop-classic"></a>チュートリアル: Azure Virtual Desktop (クラシック) のサービス アラートを設定する

>[!IMPORTANT]
>この内容は、Azure Resource Manager Azure Virtual Desktop オブジェクトをサポートしていない Azure Virtual Desktop (クラシック) に適用されます。 Azure Resource Manager Azure Virtual Desktop オブジェクトを管理しようとしている場合は、[こちらの記事](../set-up-service-alerts.md)を参照してください。

Azure Service Health を使用すると、Azure Virtual Desktop のサービスの問題と正常性に関するアドバイザリを監視できます。 Azure Service Health によって、さまざまな種類のアラート (メールや SMS など) の通知を受け取り、問題の影響を把握し、問題が解決したときに最新の情報を得ることができます。 さらに、Azure Service Health は、ダウンタイムを軽減し、お使いのリソースの可用性に影響を与える可能性がある計画的なメンテナンスや変更に備えるためにも役立ちます。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * サービス アラートを作成および構成する。

Azure Service Health の詳細については、[Azure Health のドキュメント](../../service-health/index.yml)を参照してください。

## <a name="prerequisites"></a>前提条件

- [チュートリアル: Azure Virtual Desktop でテナントを作成する](tenant-setup-azure-active-directory.md)
- [チュートリアル:PowerShell を使用してサービス プリンシパルとロールの割り当てを作成する](create-service-principal-role-powershell.md)
- [チュートリアル:](create-host-pools-azure-marketplace-2019.md)Azure Marketplace を使用してホスト プールを作成する

## <a name="create-service-alerts"></a>サービス アラートを作成する

このセクションでは、Azure Service Health を構成する方法と、Azure portal でアクセスできる通知を設定する方法について説明します。 さまざまな種類のアラートを設定し、それらを適切なタイミングで通知するようにスケジュールすることができます。

### <a name="recommended-service-alerts"></a>推奨されるサービス アラート

次の正常性イベントの種類に対してサービス アラートを作成することをお勧めします。

- **サービスの問題:** ユーザーとサービス、または Azure Virtual Desktop テナントを管理する機能との接続に影響を与える大きな問題に関する通知を受け取ります。
- **正常性に関するアドバイザリ:** 注意が必要な通知を受け取ります。 この種の通知の例を次に示します。
    - オープン ポート 3389 として安全に構成されていない仮想マシン (VM)
    - 機能の廃止

### <a name="configure-service-alerts"></a>サービス アラートを構成する

サービス アラートを構成するには:

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. **[Service Health]** を選択します。
3. 「[Create activity log alerts on service notifications (サービス通知に関するアクティビティ ログ アラートを作成する)](../../service-health/alerts-activity-log-service-notifications-portal.md?toc=%2fazure%2fservice-health%2ftoc.json)」の手順を使用してアラートと通知を設定します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Service Health を設定および使用して Azure Virtual Desktop のサービスの問題と正常性に関するアドバイザリを監視する方法について学習しました。 Azure Virtual Desktop にサインインする方法について詳しくは、引き続き Azure Virtual Desktop への接続方法に関するページを参照してください。

> [!div class="nextstepaction"]
> [Windows 7 および Windows 10 上でリモート デスクトップ クライアントに接続する](connect-windows-7-10-2019.md)