---
title: Azure Virtual Desktop での MSIX アプリのアタッチによるアプリケーションのデプロイ - Azure
description: Azure Virtual Desktop 用に MSIX アプリのアタッチを使用してアプリをデプロイする方法。
author: Heidilohr
ms.topic: how-to
ms.date: 07/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 929f69ca4503a48e8e8456111c85043cbf2db9f2
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2021
ms.locfileid: "114730246"
---
# <a name="deploy-apps-with-msix-app-attach"></a>MSIX アプリのアタッチを使用してアプリをデプロイする

この記事では、MSIX アプリのアタッチ機能を使用して Azure Virtual Desktop でアプリケーションを発行する方法の基本的な概要を説明します。 この記事では、さらに詳細な説明と手順がわかるリソースへのリンクも提供します。

## <a name="what-is-msix-app-attach"></a>MSIX アプリのアタッチとは

MSIX アプリのアタッチは、Azure Virtual Desktop のアクティブなユーザー セッションにアプリケーションを配信できるアプリケーション レイヤー化ソリューションです。 MSIX パッケージ システムは、アプリをオペレーティング システムから分離することで、仮想マシンのイメージを簡単に作成できるようにします。 また、MSIX パッケージを使用すると、ユーザーが自分の仮想マシンでアクセスできるアプリをより細かく制御できます。 アプリをマスター イメージから分離し、後でユーザーに提供することもできます。

詳細については、「[MSIX アプリのアタッチとは](../what-is-app-attach.md)」を参照してください。

## <a name="requirements"></a>要件

Azure Virtual Desktop で MSIX アプリのアタッチを使用するには、次のものが必要です。

- MSIX でパッケージ化されたアプリケーション
- 展開された MSIX アプリケーションから作成された MSIX イメージ
- MSIX 共有 (MSIX イメージを格納するネットワークの場所)
- 使用するホスト プール内にある、少なくとも 1 つの正常でアクティブなセッション ホスト
- MSIX パッケージ化アプリケーションでプライベート証明書が使用されている場合は、ホスト プール内のすべてのセッション ホストでその証明書を使用できる必要があります
- MSIX アプリのアタッチ用の Azure Virtual Desktop の構成 (ユーザーの割り当て、MSIX アプリケーションとアプリ グループの関連付け、ホスト プールへの MSIX イメージの追加)

## <a name="create-an-msix-package-from-an-existing-installer"></a>既存のインストーラーから MSItX パッケージを作成する

MSIX アプリのアタッチを使い始めるには、MSIX パッケージ内にアプリケーションを格納する必要があります。 既に MSIX 形式になっているアプリもありますが、MSI や ClickOnce などの従来のインストーラーを使用している場合は、アプリを MSIX パッケージ形式に変換する必要があります。 既存のアプリを MSIX 形式に変換する方法については、[MSIX の概要に関する記事](/windows/msix/packaging-tool/create-an-msix-overview)を参照してください。

## <a name="test-the-application-fidelity-of-your-packaged-app"></a>パッケージ化されたアプリのアプリケーションの忠実性をテストする 

アプリケーションを MSIX パッケージとして再パッケージ化した後は、アプリケーションの忠実性が高いことを確認する必要があります。 アプリの忠実性とは、再パッケージ化の前と後でのアプリケーションの動作とパフォーマンスです。 アプリの忠実性が高いアプリ パッケージのパフォーマンスは、前と後で似ています。

再パッケージ化の後でアプリの忠実性が低下する場合は、アプリをテストし、そのパフォーマンスがユーザーの基準を満たしていることを確認する必要があります。 そうでない場合は、アプリを更新して問題を回避するか、もう一度再パッケージ化を試みる必要があります。

## <a name="create-an-msix-image"></a>MSIX イメージを作成する

次に、パッケージ化されたアプリから MSIX イメージを作成する必要があります。 MSIX イメージは、MSIX アプリ パッケージを展開し、結果のアプリを VHD(X) または CIM ストレージに格納すると存在するようになります。 MSIX イメージを作成する方法については、「[MSIX イメージを作成する](../app-attach-msixmgr.md#create-an-msix-image)」を参照してください。

## <a name="configure-an-msix-file-share"></a>MSIX ファイル共有を構成する

次に、MSIX イメージを格納するための MSIX ネットワーク共有を設定する必要があります。 構成が済むと、セッション ホストにより MSIX 共有を使用して MSIX パッケージがアクティブなユーザー セッションにアタッチされ、ユーザーにアプリが配信されます。 MSIX 共有を設定する方法については、「[MSIX アプリのアタッチ用にファイル共有を設定する](../app-attach-file-share.md)」を参照してください。

## <a name="configure-msix-app-attach-for-azure-virtual-desktop-host-pool"></a>Azure Virtual Desktop ホスト プール用に MSIX アプリのアタッチを構成する

MSIX イメージを MSIX 共有にアップロードした後、Azure portal を開き、MSIX アプリのアタッチを受け入れるのに使用するホスト プールを構成する必要があります。 ホスト プールを構成する方法については、「[Azure portal で MSIX アプリのアタッチを設定する](../app-attach-azure-portal.md)」を参照してください。
