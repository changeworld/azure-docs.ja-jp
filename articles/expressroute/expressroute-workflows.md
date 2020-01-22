---
title: 'Azure ExpressRoute: 回線構成のワークフロー'
description: このページでは、ExpressRoute の回線とピアリングを構成するためのワークフローについて説明します。
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: cherylmc
ms.openlocfilehash: e833e20085d7cfd8f727acb394851e96e7e19368
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/10/2020
ms.locfileid: "75864368"
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>回線のプロビジョニングと回線の状態の ExpressRoute ワークフロー
このページでは、サービス プロビジョニングと上位のルーティング構成ワークフローについて段階的に説明します。

![回線のワークフロー](./media/expressroute-workflows/expressroute-circuit-workflow.png)

次の図および対応する手順では、ExpressRoute 回線をエンドツーエンドでプロビジョニングするためのタスクの概要を示します。 

1. PowerShell を利用し、ExpressRoute 回線を構成します。 詳細については、 [ExpressRoute 回線の作成](expressroute-howto-circuit-classic.md) に関する記事を参照してください。
2. サービス プロバイダーに接続を要求します。 このプロセスはさまざまです。 接続の要求方法に関する詳細については、接続プロバイダーにお問い合わせください。
3. PowerShell で ExpressRoute のプロビジョニング状態を確認することで、回線が正常にプロビジョニングされていることを確認します。 
4. ルーティング ドメインを構成します。 接続プロバイダーが代わりにレイヤー 3 構成を管理する場合、接続プロバイダーが回線のルーティングを構成します。 接続プロバイダーがレイヤー 2 サービスのみを提供する場合、[ルーティング要件](expressroute-routing.md)ページと[ルーティング構成](expressroute-howto-routing-classic.md)ページのガイドラインに従い、自分でルーティングを構成する必要があります。
   
   * Azure プライベート ピアリングを有効にする - 仮想ネットワーク内にデプロイされている VM / クラウドに接続するには、このピアリングを有効にします。

   * Microsoft ピアリングを有効にする - Office 365 などのオンライン サービスにアクセスするにはこれを有効にします。 すべての Azure PaaS サービスは、Microsoft ピアリング経由でアクセスできます。
     
     > [!IMPORTANT]
     > Microsoft に接続するには、インターネットに使用しているプロキシ/エッジとは別のプロキシ/エッジを使用する必要があります。 ExpressRoute とインターネットの両方に同じエッジを使用すると、ルーティングが非同期になり、ネットワークの接続が停止します。
     > 
     > 
     
     ![ワークフローのルーティング](./media/expressroute-workflows/routing-workflow.png)
5. ExpressRoute 回線に仮想ネットワークをリンクする - 仮想ネットワークを ExpressRoute 回線にリンクできます。 指示に従い、回線に [VNet をリンク](expressroute-howto-linkvnet-arm.md) してください。 VNet は ExpressRoute 回線と同じ Azure サブスクリプションに配置するか、別のサブスクリプションに配置できます。

## <a name="expressroute-circuit-provisioning-states"></a>ExpressRoute 回線のプロビジョニング状態
各 ExpressRoute 回線には 2 つの状態があります。

* サービス プロバイダーのプロビジョニング状態
* Status

状態は Microsoft のプロビジョニングの状態を表します。 Expressroute 回線を作成したとき、このプロパティは "有効" に設定されます。

接続プロバイダーのプロビジョニング状態は接続プロバイダー側の状態を表します。 これは「*プロビジョニングされていません*」、「*プロビジョニングしています*」、「*プロビジョニング済み*」のいずれかになります。 ピアリングを構成するためには、ExpressRoute 回線がプロビジョニング状態になっている必要があります。

### <a name="possible-states-of-an-expressroute-circuit"></a>ExpressRoute 回線の状態
このセクションでは、ExpressRoute 回線の状態を一覧にまとめています。

**作成時**

ExpressRoute 回線は、リソースの作成時に次の状態を報告します。

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


**接続プロバイダーが回線にプロビジョニングしているとき**

ExpressRoute 回線は、接続プロバイダーが回線のプロビジョニングを処理している間に次の状態を報告します。

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled


**接続プロバイダーがプロビジョニングを完了したとき**

ExpressRoute 回線は、接続プロバイダーが回線を正常にプロビジョニングしたときに次の状態を報告します。

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled


**接続プロバイダーが回線をプロビジョニング解除しているとき**

ExpressRoute 回線をプロビジョニング解除する必要がある場合、サービス プロバイダーがプロビジョニング解除プロセスを完了したときに、回線は次の状態を報告します。

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


必要に応じて再度有効にしたり、PowerShell コマンドレットを実行して回線を削除したりできます。  

> [!IMPORTANT]
> ServiceProviderProvisioningState が Provisioning または Provisioned である場合、回線を削除することはできません。 接続プロバイダーを削除するには、その前に回線をプロビジョニング解除する必要があります。 Microsoft は、ExpressRoute 回線リソースが Azure で削除されるまで、引き続き回線の課金を行います。
> 

## <a name="routing-session-configuration-state"></a>ルーティング セッション構成の状態
BGP プロビジョニング状態は、Microsoft エッジで BGP セッションが有効になっているかどうかを報告します。 プライベートまたは Microsoft ピアリングを使用するには、状態を有効にする必要があります。

特に Microsoft ピアリングの場合、BGP セッションの状態を確認することが重要です。 BGP プロビジョニング状態に加え、「 *アドバタイズされたパブリック プレフィックス*」と呼ばれているもう 1 つの状態があります。 BGP セッションを開始し、ルーティングをエンドツーエンドで動作させるには、「アドバタイズされたパブリック プレフィックス」状態を「*構成済み*」にする必要があります。 

「アドバタイズされたパブリック プレフィックス」状態が「*検証必須*」に設定されている場合、BGP セッションは無効になっています。アドバタイズされたプレフィックスがルーティング レジストリの AS 番号に一致しなかったためです。 

> [!IMPORTANT]
> 「アドバタイズされたパブリック プレフィックス」状態が「*手動検証*」の場合、[Microsoft サポート](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)でサポート チケットを開き、関連自律システム番号と共にアドバタイズされた IP アドレスを所有している証拠を提出する必要があります。
> 
> 

## <a name="next-steps"></a>次のステップ
* ExpressRoute 接続を構成します。
  
  * [ExpressRoute 回線の作成](expressroute-howto-circuit-arm.md)
  * [ルーティングの構成](expressroute-howto-routing-arm.md)
  * [ExpressRoute 回線への VNet のリンク](expressroute-howto-linkvnet-arm.md)

