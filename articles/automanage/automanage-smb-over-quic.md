---
title: Azure Automanage マシンのベスト プラクティスを使用した SMB over QUIC
description: Azure Automanage マシンのベスト プラクティスを使用した SMB over QUIC の管理の概要
author: daniellee-microsoft
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/1/2021
ms.author: jol
ms.openlocfilehash: 49c8e47fd695c2a65807ace4c33f07937544f149
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132719734"
---
# <a name="smb-over-quic-with-automanage-machine-best-practices"></a>Automanage マシンのベスト プラクティスを使用した SMB over QUIC

SMB over QUIC では、在宅勤務者、モバイル デバイス ユーザー、ブランチ オフィス向けに "SMB VPN" を提供することで、インターネットなどの信頼されていないネットワーク経由で、エッジ ファイル サーバーへの安全で信頼性の高い接続を実現します。 SMB over QUIC の詳細と、SMB over QUIC の構成方法については、「[SMB over QUIC](/windows-server/storage/file-server/smb-over-quic)」を参照してください。

また、SMB over QUIC は、Automanage マシンのベスト プラクティスと統合されているため、SMB over QUIC の管理がより簡単になります。 QUIC では証明書を使用して暗号化を実現するため、多くの組織では複雑な公開キー基盤の維持に苦労しています。 Automanage マシンのベスト プラクティスでは、証明書が警告なしに期限切れにならないようにして SMB over QUIC の有効性を維持し、サービスが最大限継続されるようにします。

## <a name="how-to-get-started"></a>ファースト ステップ

> [!NOTE]
> Automanage マシンのベスト プラクティスを使用するための前提条件については、[Azure portal での VM の有効化](quick-create-virtual-machines-portal.md)に関する記事を参照してください。

> [!NOTE]
> プレビュー段階では、[このリンク](https://aka.ms/automanage-ws-portal-preview)を使用して Azure portal で開始できます。

## <a name="enable-automanage-best-practices-when-creating-a-new-vm"></a>新しい VM の作成時に Automanage のベスト プラクティスを有効にする

VM 上の SMB over QUIC で Automanage マシンのベスト プラクティスを有効にするには、次の手順に従います。

1. 上記のプレビュー リンクを使用して Azure portal にサインインします。

2. _Windows Server 2022 Datacenter: Azure Edition_ イメージを使用して Azure VM を作成し、Windows Server 用の Automanage 機能 (SMB over QUIC を含む) を取得します。

3. **[管理]** タブの [Azure Automanage] セクションで、[Azure Automanage 環境] 設定に **[Dev/Test]** または **[運用]** を選択して、Automanage マシンのベスト プラクティスを有効にします。

    :::image type="content" source="media\automanage-smb-over-quic\create-vm-automanage-setting.png" alt-text="VM の作成時に Automanage を有効にします。":::

4. 必要に応じて追加の設定を構成し、VM を作成します。

## <a name="enable-automanage-best-practices-on-existing-vms"></a>既存の VM で Automanage のベスト プラクティスを有効にする

以前に作成した VM で Automanage マシンのベスト プラクティスを有効にすることもできます。 _Windows Server 2022 Datacenter: Azure Edition_ イメージを使用して VM を作成し、Windows Server 用の Automanage 機能 (SMB over QUIC を含む) を取得する必要があります。

1. 以前に作成した VM に移動します。
2. Automanage メニューを選択し、**Dev/Test** または **運用** 環境を選択し、**有効にする** をクリックします。

    :::image type="content" source="media\automanage-smb-over-quic\vm-enable-automanage.png" alt-text="既存の VM で Automanage を有効にします。":::

## <a name="viewing-automanage-best-practice-compliance"></a>Automanage のベスト プラクティスのコンプライアンスを表示する

マシンのベスト プラクティスが構成され、ベスト プラクティスのポリシーが VM で割り当ておよび評価されるには、数時間かかる場合があります。 完了すると、次に示すように、SMB over QUIC のポリシーとそれらの状態が表示されます。 これらのポリシーは継続的に自動的に評価され、SMB over QUIC が正しく構成され、使用される証明書が有効で正常な状態であることが確認されます。

:::image type="content" source="media\automanage-smb-over-quic\vm-automanage-configured.png" alt-text="VM の SMB over QUIC ポリシーを表示します。":::

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [SMB over QUIC の詳細を確認する](/windows-server/storage/file-server/smb-over-quic)