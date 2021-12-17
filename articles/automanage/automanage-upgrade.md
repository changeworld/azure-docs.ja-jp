---
title: Azure Automanage マシンを最新の Automanage バージョンにアップグレードする
description: マシンを最新の Azure Automanage バージョンにアップグレードする方法について説明します
author: mmccrory
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 10/20/2021
ms.author: memccror
ms.openlocfilehash: d7cc53c30ead02a2ca4fc40fe00ae2235016336f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478347"
---
# <a name="upgrade-your-machines-to-the-latest-automanage-version"></a>マシンを最新の Automanage バージョンにアップグレードする

Automanage では、2021 年 11 月にマシンのベスト プラクティス オファリングの新しいバージョンがリリースされました。 新しい API では、マシンに適用するサービスと設定を選択できるカスタム プロファイルの作成がサポートされるようになりました。 また、この新しいバージョンでは、Automanage アカウントは不要になりました。 この記事では、バージョン間の違いとアップグレードの方法について説明します。 

## <a name="how-to-upgrade-your-machines"></a>マシンをアップグレードする方法

マシンを最新の API バージョンの Automanage にアップグレードする手順を以下に示します。 

### <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、始める前に[アカウントを作成](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)してください。

> [!NOTE]
> 無料試用版アカウントでは、このチュートリアルで使用されている仮想マシンを利用できません。 従量課金制サブスクリプションにアップグレードしてください。

> [!IMPORTANT]
> 新しい Automanage バージョンを使用して、サブスクリプションで Automanage を初めて有効にする場合に必要な Azure RBAC アクセス許可は、**所有者** ロール、または **共同作成者** ロールと **ユーザー アクセス管理者** ロールの併用です。

### <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure portal](https://portal.azure.com/) にサインインします。


### <a name="check-to-see-which-machines-need-to-be-upgraded"></a>アップグレードする必要があるマシンを確認する

アップグレードする必要があるマシンは、いずれも状態に **[アップグレードが必要]** と表示されます。 また、Automanage の概要ページに、マシンをアップグレードする必要があることを示すバナーが表示されます。 

:::image type="content" source="media\automanage-upgrade\overview-blade.png" alt-text="[アップグレードが必要] 状態。":::

### <a name="disable-automanage-machines-that-need-to-be-upgrade"></a>アップグレードする必要がある Automanage マシンを無効にする

マシンを新しい Automanage バージョンにアップグレードするには、以前のバージョンの Automanage からマシンを無効にしておく必要があります。 マシンを無効にするには、これらの手順に従います。
1. 無効にする仮想マシンの横にあるチェック ボックスをオンにします。
1. **[無効]** ボタンをクリックします。
1. **[Disable]\(無効\)** に同意する前に、結果として表示されたポップアップのメッセージを注意深く確認します。

:::image type="content" source="media\automanage-upgrade\disable-automanage.png" alt-text="Automanage を無効にします。":::

### <a name="re-enable-automanage-on-your-machines"></a>マシンで Automanage を再度有効にする

マシンが Automanage からオフボードされた後、Automanage を再度有効にすることができます。 Automanage を再度有効にすると、自動的に最新の Automanage バージョンが使用されます。 

1. **[Enable on existing VM]\(既存の VM で有効にする\)** を選択します。

    :::image type="content" source="media\automanage-upgrade\zero-vm-list-view.png" alt-text="[Enable on existing VM]\(既存の VM で有効にする\)":::

2. **[構成プロファイル]** で、プロファイルの種類として、 **[Azure のベスト プラクティス - 運用]** 、 **[Azure のベストプラクティス - Dev/Test]** 、または [ **[カスタム プロファイル]** ](virtual-machines-custom-profile.md) を選択します。

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-quick-create.png" alt-text="環境を選択します。":::

    > [!NOTE]
    > **運用** 環境は、 **[Azure のベスト プラクティス - 運用]** 構成プロファイルにマップされます。 **Dev/Test** 環境は、 **[Azure のベスト プラクティス - Dev/Test]** 構成プロファイルにマップされます。 **[Configuration Preferences]\(構成の基本設定\)** を利用していた場合は、同じ変更を加えて **カスタム プロファイル** を作成できます。 

3. **[マシンの選択]** ブレードで次のようにします。
    1. 実際の **サブスクリプション** と **リソース グループ** でリストをフィルター処理します。
    1. オンボードする各仮想マシンのチェック ボックスをオンにします。
    1. **[選択]** ボタンをクリックします。
    > [!NOTE]
    > Azure VM と Azure Arc 対応サーバーの両方を選択できます。

    :::image type="content" source="media\automanage-upgrade\existing-vm-select-machine.png" alt-text="使用可能な VM のリストから既存の VM を選択します。":::

4. **[有効]** ボタンをクリックします。

これで、マシンは最新バージョンの Automanage にオンボードされます。

## <a name="differences-in-the-automanage-versions"></a>Automanage のバージョン間の違い

### <a name="environment-and-configuration-profiles"></a>環境と構成プロファイル
以前のバージョンの Automanage では、環境の種類 (Dev/Test または運用) を選択していました。 新しいバージョンの Automanage では、環境が構成プロファイルにマップされます。 構成プロファイルのオプションとして、Azure のベスト プラクティス - Dev/Test、Azure のベスト プラクティス - 運用、カスタム プロファイルがあります。 **Dev/Test** 環境のサービスと設定のセットは、**Azure のベスト プラクティス- Dev/Test** 構成プロファイルの内容と同じです。 同様に、**運用** 環境のサービスと設定のセットは、**Azure のベスト プラクティス- 運用** 構成プロファイルの内容と同じです。 

### <a name="configuration-preferences-and-custom-profiles"></a>構成の基本設定とカスタム プロファイル
以前のバージョンの Automanage では、 **[Configuration Preferences]\(構成の基本設定\)** を使用して設定のサブセットをカスタマイズできました。 最新バージョンの Automanage では、カスタマイズが強化されています。オンボードする各サービスを選択し、 **[カスタム プロファイル]** を使用してサービスの一部の設定を変更できるようになりました。 

### <a name="automanage-account-and-first-party-application"></a>Automanage アカウントとファースト パーティ アプリケーション
以前のバージョンの Automanage では、Automanage アカウントを MSI として使用して、マシン上でアクションを実行していました。 しかし、最新バージョンの Automanage では、Automanage はファースト パーティ アプリケーション (アプリケーション ID: d828acde-4b48-47f5-a6e8-52460104a052) を使用して、Automanage マシン上でアクションを実行します。 

Automanage の以前のバージョンと新しいバージョンのどちらについても、次のアクセス許可が必要です。
* サブスクリプションで Automanage を初めてオンボードする場合は、**所有者** ロール、または **共同作成者** ロールと **ユーザー アクセス管理者** ロールの併用が必要です。
* Automanage マシンが既に含まれているサブスクリプションで Automanage をオンボードする場合は、マシンが存在するリソース グループに対する **共同作成者** ロールが必要です。 
> [!NOTE]
> Automanage にオンボードするマシンが、マシンとは異なるサブスクリプションの Log Analytics ワークスペースに既に接続されている場合は、Log Analytics ワークスペース サブスクリプションに対しても上記のアクセス許可が必要です。

## <a name="next-steps"></a>次の手順 

よく寄せられる質問とその回答については、FAQ を参照してください。 

> [!div class="nextstepaction"]
> [よく寄せられる質問](faq.yml)

