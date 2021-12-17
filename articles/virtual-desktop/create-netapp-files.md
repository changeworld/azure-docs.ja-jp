---
title: Azure Virtual Desktop で Azure NetApp ファイルを作成する - Azure
description: この記事では、Azure Virtual Desktop で Azure NetApp Files を作成する方法について説明します。
author: Heidilohr
ms.topic: how-to
ms.date: 08/19/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: fa6052c91be73e05bc2413f57810405fe5a198a0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124818645"
---
# <a name="upload-msix-images-to-azure-netapp-files-in-azure-virtual-desktop"></a>MSIX イメージを Azure Virtual Desktop の Azure NetApp Files にアップロードする

この記事では、MSIX イメージを Azure Virtual Desktop の Azure NetApp Files にアップロードする方法について説明します。

## <a name="requirements"></a>必要条件

イメージのアップロードを開始する前に、Azure NetApp Files の設定がまだ済んでいない場合は、それを行う必要があります。

Azure NetApp Files を設定するには、次のものが必要です。

- 共同作成者または管理者アクセス権が割り当てられた Azure アカウント

- Active Directory Domain Services (AD DS) に参加している仮想マシン (VM) または物理マシン、およびそれにアクセスするためのアクセス許可

- ドメイン参加済みセッション ホストで構成される Azure Virtual Desktop ホスト プール。 各セッション ホストは、Azure NetApp ファイルを作成するリージョンと同じリージョンに配置する必要があります。 詳細については、[リージョン別の提供状況](https://azure.microsoft.com/global-infrastructure/services/?products=netapp)に関するページをご覧ください。 ご利用の既存のセッション ホストが使用可能なリージョンのいずれかに置かれていない場合は、新規に作成する必要があります。

## <a name="start-using-azure-netapp-files"></a>Azure NetApp Files の使用を開始する

Azure NetApp Files の使用を開始するには

1. 「[Azure NetApp Files アカウントの設定](create-fslogix-profile-container.md#set-up-your-azure-netapp-files-account)」の手順に従って、Azure NetApp Files アカウントを設定します。
2. 「[容量プールを設定する](../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)」の手順に従って、容量プールを作成します。
3. 「[Active Directory 接続への参加](create-fslogix-profile-container.md#join-an-active-directory-connection)」の手順に従って、Azure Active Directory (Azure AD) 接続に参加します。
4. 「[新しいボリュームの作成](create-fslogix-profile-container.md#create-a-new-volume)」および「[ボリュームのアクセス パラメーターの構成](create-fslogix-profile-container.md#configure-volume-access-parameters)」の手順に従って、新しいボリュームを作成します。
5. 「[Azure NetApp Files 共有にユーザーがアクセスできることを確認する](create-fslogix-profile-container.md#make-sure-users-can-access-the-azure-netapp-file-share)」の手順に従って、Azure NetApp Files 共有への接続が機能することを確認します。

## <a name="upload-an-msix-image-to-the-azure-netapp-file-share"></a>MSIX イメージを Azure NetApp Files 共有にアップロードする

これで Azure NetApp Files 共有の設定が完了したしたので、イメージのアップロードを開始することができます。

MSIX イメージを Azure NetApp Files 共有にアップロードするには:

1. 各セッション ホストで、MSIX パッケージに署名した証明書をインストールします。 証明書は必ず、 **[信頼されたユーザー]** という名前のフォルダーに保存してください。
2. Azure NetApp Files 共有に追加する MSIX イメージをコピーします。
3. **[エクスプローラー]** に移動し、マウント パスを入力してから、MSIX イメージをマウント パス フォルダーに貼り付けます。

これで、セッション ホストでは、Azure portal または PowerShell を使用して MSIX パッケージを追加したときに、MSIX イメージにアクセスできるようになります。

## <a name="next-steps"></a>次の手順

Azure NetApp Files 共有の作成が完了したので、次に Azure Virtual Desktop でのその使用目的に関するリソースをいくつか示します。

- [Azure NetApp Files および AD DS を使用してプロファイル コンテナーを作成する](create-fslogix-profile-container.md)
- [Azure Virtual Desktop の FSLogix プロファイル コンテナーのストレージ オプション](store-fslogix-profile.md)
- [Azure NetApp Files のレプリケーション ピアリングを作成する](../azure-netapp-files/cross-region-replication-create-peering.md)
