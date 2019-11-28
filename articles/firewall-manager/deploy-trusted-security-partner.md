---
title: Azure Firewall Manager の信頼されたセキュリティ パートナーをデプロイする
description: Azure portal を使用して Azure Firewall Manager の信頼されたセキュリティをデプロイする方法について説明します。
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: bcea9a8674e4b1979698b7d28eb4192172b0dc11
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931316"
---
# <a name="deploy-a-trusted-security-partner-preview"></a>信頼されたセキュリティ パートナー (プレビュー) のデプロイ

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Azure Firewall Manager の*信頼されたセキュリティ パートナー*では、使い慣れた、最適なサード パーティのサービスとしてのセキュリティ (SECaaS) オファリングを使用して、ユーザーのインターネット アクセスを保護することができます。

サポートされているシナリオとベスト プラクティスのガイドラインの詳細については、「[信頼されたセキュリティ パートナー (プレビュー) とは](trusted-security-partners.md)」を参照してください。

このプレビューでサポートされているセキュリティ パートナーは、 **ZScaler** と **iboss** です。 サポートされているリージョンは、WestCentralUS、NorthCentralUS、WestUS、WestUS2、および EastUS です。

## <a name="prerequisites"></a>前提条件

> [!IMPORTANT]
> `Register-AzProviderFeature` PowerShell コマンドを使用して、Azure Firewall Manager Preview を明示的に有効にする必要があります。

PowerShell コマンド プロンプトから、次のコマンドを実行します:

```azure-powershell
connect-azaccount
Register-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network
```
機能の登録が完了するまで、最長で 30 分かかります。 次のコマンドを実行して、登録状態を確認します。

`Get-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network`

## <a name="deploy-a-third-party-security-provider-in-a-new-hub"></a>サード パーティのセキュリティ プロバイダーを新しいハブにデプロイする

1. Azure Portal ( https://portal.azure.com ) にサインインします。
2. **[検索]** で、 **「Firewall Manager」** と入力し **[サービス]** でそれを選択します。
3. **[はじめに]** に移動します。 **[Create a Secured Virtual Hub]\(セキュリティ保護付き仮想ハブを作成\)** を選択します。 
4. サブスクリプションとリソース グループを入力し、サポートされているリージョンを選択して、ハブと仮想 WAN の情報を追加します。 
5. **[Deploy VPN gateway]\(VPN ゲートウェイのデプロイ\)** は既定で有効になっています。 信頼されたセキュリティ パートナーをハブにデプロイするには、VPN Gateway が必要です。 
6. **[次へ:Azure Firewall]** を選択します。
   > [!NOTE]
   > 信頼されたセキュリティ パートナーは、VPN Gateway トンネルを使用してハブに接続します。 VPN Gateway を削除すると、信頼されたセキュリティ パートナーへの接続が失われます。
7. プライベート トラフィックをフィルター処理するための Azure Firewall をサード パーティのサービス プロバイダーと一緒にデプロイし、インターネット トラフィックをフィルター処理する場合は、Azure Firewall のポリシーを選択します。 [サポートされているシナリオ](trusted-security-partners.md#key-scenarios)を参照してください。
8. ハブにサード パーティのセキュリティ プロバイダーのみをデプロイする場合は、 **[Azure Firewall:Enabled/Disabled]\(有効/無効\)** を選択し、 **[無効]** に設定します。 
9. **[次へ:信頼されたセキュリティ パートナー]** を選択します。
10. **[信頼されたセキュリティ パートナー]** を選択して、 **[有効]** に設定します。 パートナーを選択します。 
11. **[次へ]** を選択します。 
12. 内容を確認し、 **[作成]** を選択します。

VPN ゲートウェイのデプロイには 30 分以上かかることがあります。

ハブが作成されたことを確認するには、[Azure Firewall Manager] -> [Secured Hubs]\(セキュリティ保護付きハブ) に移動します。 [ハブ] -> [概要] ページを選択すると、パートナー名と状態が「**セキュリティ接続保留中**」として表示されます。

ハブが作成され、セキュリティ パートナーがセットアップされたら、引き続きセキュリティ プロバイダーをハブに接続します。

## <a name="deploy-a-third-party-security-provider-in-an-existing-hub"></a>サード パーティのセキュリティ プロバイダーを既存のハブにデプロイする

また、Virtual WAN 内の既存のハブを選択し、それを*セキュリティ保護付き仮想ハブ*に変換することもできます。

1. **[はじめに]** で、 **[Convert Existing Hubs]\(既存のハブの変換)** を選択します。
2. サブスクリプションと既存のハブを選択します。 残りの手順に従って、新しいハブにサード パーティ プロバイダーをデプロイします。

既存のハブをサード パーティ プロバイダーによってセキュリティ保護付きハブに変換するには、VPN ゲートウェイをデプロイする必要があることに注意してください。

## <a name="configure-third-party-security-providers-to-connect-to-a-secured-hub"></a>セキュリティ保護付きハブに接続するようにサード パーティのセキュリティ プロバイダーを構成する

サード パーティ プロバイダーで仮想ハブの VPN Gateway へのトンネルを設定するには、ハブへのアクセス権が必要です。 これを行うには、サービス プリンシパルをサブスクリプションまたはリソース グループに関連付け、アクセス権を付与します。 次に、ポータルを使用して、これらの資格情報をサード パーティに付与する必要があります。

1. Azure Active Directory (AD) サービス プリンシパルを作成します。リダイレクト URL はスキップできます。 

   [方法: リソースにアクセスできる Azure AD アプリケーションとサービス プリンシパルをポータルで作成する](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)
2. サービス プリンシパルのアクセス権とスコープを追加します。
   [方法: リソースにアクセスできる Azure AD アプリケーションとサービス プリンシパルをポータルで作成する](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)

   > [!NOTE]
   > より詳細に制御するには、アクセスをリソース グループのみに制限します。
3. 「[ZScaler:Microsoft Azure Virtual WAN との統合を構成する](https://help.zscaler.com/zia/configuring-microsoft-azure-virtual-wan-integration)」の手順に従って、以下を実行します。

   - パートナー ポータルにサインインし、資格情報を追加して、信頼されたパートナーがセキュリティ保護付きハブにアクセスできるようにします。
   - パートナー ポータルで仮想ハブを同期し、仮想ハブへのトンネルを設定します。 Azure AD の認証資格情報が検証されると、それを実行することができます。
   
4. Azure の Azure Virtual WAN ポータルで、トンネル作成の状態を確認できます。 トンネルが Azure とパートナー ポータルの両方で **[connected]\(接続済み\)** と表示されたら、次の手順に進み、ルートを設定してパートナーにインターネット トラフィックを送信するブランチと VNet を選択します。

## <a name="configure-route-settings"></a>ルート設定の構成

1. [Azure Firewall Manager] -> [Secured Hubs]\(セキュリティ保護付きハブ) を参照します。 
2. ハブを選択します。 ハブの状態には、「**セキュリティ接続保留中**」ではなく、「**プロビジョニング済み**」と表示されます。

   サード パーティ プロバイダーがハブに接続できることを確認してください。 VPN ゲートウェイのトンネルは、 **[connected]\(接続済み\)** 状態である必要があります。 この状態は、以前の状態と比較して、ハブとサード パーティ パートナー間の接続の正常性をより反映します。
3. ハブを選択し、 **[Route Settings]\(ルート設定\)** に移動します。

   ハブにサード パーティ プロバイダーをデプロイすると、ハブが*セキュリティ保護付き仮想ハブ*に変換されます。 これにより、サード パーティ プロバイダーがハブへの 0.0.0.0/0 (既定) ルートをアドバタイズします。 ただし、どの接続がこの既定のルートを取得すべきかを選択しない限り、VNet 接続とハブに接続されているサイトはこのルートを取得しません。
4. **[Internet traffic]\(インターネット トラフィック\)** で、 **[VNet-to-Internet]\(VNet - インターネット間\)** または **[Branch-to-Internet]\(ブランチ - インターネット間\)** 、あるいはその両方を選択し、ルートがサード パーティによって送信されるように構成します。

   これはハブにルーティングする必要があるトラフィックの種類のみを指定しますが、VNet またはブランチのルートには影響しません。 これらのルートは、既定ではハブにアタッチされているすべての VNet およびブランチには反映されません。
5. **[secure connections]\(セキュリティ保護付きの接続\)** を選択し、これらのルートを設定する接続を選択する必要があります。 これは、サード パーティ プロバイダーへのインターネット トラフィックの送信を開始できる VNet およびブランチを指定します。
6. **[Route Settings]\(ルート設定\)** から、[Internet traffic]\(インターネット トラフィック\) の **[Secure connections]\(セキュリティ保護付きの接続\)** を選択し、セキュリティで保護する VNet またはブランチ (Virtual WAN 内の*サイト*) を選択します。 **[Secure Internet traffic]\(セキュリティで保護されたインターネット トラフィック\)** を選択します。
   ![[Secure Internet traffic]\(セキュリティ保護付きのインターネット トラフィック\)](media/deploy-trusted-security-partner/secure-internet-traffic.png)
7. [ハブ] ページに戻ります。 ハブの **[信頼されたセキュリティ パートナー]** の状態は、 **[Secured]\(セキュリティ保護付き\)** となっているはずです。

## <a name="branch-or-vnet-internet-traffic-via-third-party-service"></a>サード パーティのサービス経由のブランチまたは VNet のインターネット トラフィック

次に、VNet 仮想マシンまたはブランチ サイトがインターネットにアクセスできるかどうかを確認し、トラフィックがサード パーティのサービスに送信されていることを検証します。

ルート設定の手順が完了すると、VNet の仮想マシンとブランチサイトの両方で、サード パーティのサービス ルートに 0/0 が送信されます。 これらの仮想マシンに RDP または SSH 接続することはできません。 サインインするには、ピアリングされた VNet に [Azure Bastion](../bastion/bastion-overview.md) サービスをデプロイします。

## <a name="next-steps"></a>次の手順

- [チュートリアル:Azure portal を使用して Azure Firewall Manager Preview でクラウド ネットワークをセキュリティで保護する](secure-cloud-network.md)




