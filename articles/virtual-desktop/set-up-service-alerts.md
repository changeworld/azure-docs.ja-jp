---
title: Windows Virtual Desktop のサービス アラートを設定する - Azure
description: Windows Virtual Desktop のサービス通知を受信するように Azure Service Health を設定する方法。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 035fc4840b08a0a9807c228cb5f63c191efc524a
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2020
ms.locfileid: "86256885"
---
# <a name="tutorial-set-up-service-alerts"></a>チュートリアル:サービス アラートを設定する

>[!IMPORTANT]
>このコンテンツは、Spring 2020 更新プログラムと Azure Resource Manager Windows Virtual Desktop オブジェクトの組み合わせを対象としています。 Azure Resource Manager オブジェクトなしで Windows Virtual Desktop Fall 2019 リリースを使用している場合は、[こちらの記事](./virtual-desktop-fall-2019/set-up-service-alerts-2019.md)を参照してください。
>
> Windows Virtual Desktop Spring 2020 更新プログラムは現在、パブリック プレビュー段階です。 このプレビュー バージョンはサービス レベル アグリーメントなしで提供されており、運用環境のワークロードに使用することは推奨されません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Azure Service Health を使用すると、Windows Virtual Desktop のサービスの問題と正常性に関するアドバイザリを監視できます。 Azure Service Health によって、さまざまな種類のアラート (メールや SMS など) の通知を受け取り、問題の影響を把握し、問題が解決したときに最新の情報を得ることができます。 さらに、Azure Service Health は、ダウンタイムを軽減し、お使いのリソースの可用性に影響を与える可能性がある計画的なメンテナンスや変更に備えるためにも役立ちます。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * サービス アラートを作成および構成する。

Azure Service Health の詳細については、[Azure Health のドキュメント](https://docs.microsoft.com/azure/service-health/)を参照してください。

## <a name="create-service-alerts"></a>サービス アラートを作成する

このセクションでは、Azure Service Health を構成する方法と、Azure portal でアクセスできる通知を設定する方法について説明します。 さまざまな種類のアラートを設定し、それらを適切なタイミングで通知するようにスケジュールすることができます。

### <a name="recommended-service-alerts"></a>推奨されるサービス アラート

次の正常性イベントの種類に対してサービス アラートを作成することをお勧めします。

- **サービスの問題:** ユーザーとサービス、または Windows Virtual Desktop テナントを管理する機能との接続に影響を与える主な問題に関する通知を受け取ります。
- **正常性に関するアドバイザリ:** 注意が必要な通知を受け取ります。 この種の通知の例を次に示します。
    - オープン ポート 3389 として安全に構成されていない仮想マシン (VM)
    - 機能の廃止

### <a name="configure-service-alerts"></a>サービス アラートを構成する

サービス アラートを構成するには:

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. **[Service Health]** を選択します。
3. 「[サービス通知のアクティビティ ログ アラートを作成する](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log-service-notifications?toc=%2Fazure%2Fservice-health%2Ftoc.json#alert-and-new-action-group-using-azure-portal)」の手順に従ってアラートと通知を設定します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Service Health を設定および使用して Windows Virtual Desktop のサービスの問題と正常性に関するアドバイザリを監視する方法について学習しました。 Windows Virtual Desktop にサインインする方法について詳しくは、引き続き Windows Virtual Desktop への接続方法に関するページを参照してください。

> [!div class="nextstepaction"]
> [Windows 7 および Windows 10 上でリモート デスクトップ クライアントに接続する](./connect-windows-7-10.md)
