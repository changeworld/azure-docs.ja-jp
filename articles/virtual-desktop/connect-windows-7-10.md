---
title: Windows Virtual Desktop に接続する Windows 10 または 7 - Azure
description: Windows デスクトップ クライアントを使用して Windows Virtual Desktop に接続する方法。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 73811aa58b09e394468596f42e0ff221b2bb240b
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2020
ms.locfileid: "86261566"
---
# <a name="connect-with-the-windows-desktop-client"></a>Windows デスクトップ クライアントを使用して接続する

> 適用対象:Windows 7、Windows 10、および Windows 10 IoT Enterprise

>[!IMPORTANT]
>このコンテンツは、Azure Resource Manager Windows Virtual Desktop オブジェクトを含む Spring 2020 更新プログラムに適用されます。 Azure Resource Manager オブジェクトなしで Windows Virtual Desktop Fall 2019 リリースを使用している場合は、[この記事](./virtual-desktop-fall-2019/connect-windows-7-10-2019.md)を参照してください。
>
> Windows Virtual Desktop Spring 2020 更新プログラムは現在、パブリック プレビュー段階です。 このプレビュー バージョンはサービス レベル アグリーメントなしで提供されており、運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Windows デスクトップ クライアントを使用して、Windows 7 または Windows 10、および Windows 10 IoT Enterprise を使用しているデバイス上の Windows Virtual Desktop リソースにアクセスできます。

>[!NOTE]
>Windows クライアントは自動的に既定の Windows Virtual Desktop Fall 2019 リリースになります。 ただしクライアントは、ユーザーが Azure Resource Manager リソースも所有していることを検出した場合、そのリソースを自動的に追加するか、リソースが利用可能であることをユーザーに通知します。

> [!IMPORTANT]
> Windows Virtual Desktop では、RemoteApp とデスクトップ接続 (RADC) クライアントおよびリモート デスクトップ接続 (MSTSC) クライアントはサポートされていません。

> [!IMPORTANT]
> Windows Virtual Desktop では、現在、Windows Store のリモート デスクトップ クライアントはサポートされていません。

## <a name="install-the-windows-desktop-client"></a>Windows デスクトップ クライアントをインストールする

ご自身の Windows バージョンに合ったクライアントを選択してください。

- [Windows (64 ビット)](https://go.microsoft.com/fwlink/?linkid=2068602)
- [Windows (32 ビット)](https://go.microsoft.com/fwlink/?linkid=2098960)
- [Windows ARM64](https://go.microsoft.com/fwlink/?linkid=2098961)

現在のユーザー用にのクライアントをインストールできます。この場合、管理者権限は必要ありません。または、管理者がクライアントをインストールして構成し、デバイス上のすべてのユーザーがアクセスできるようにすることができます。

インストールが完了すると、クライアントはスタート メニューから**リモート デスクトップ**を検索することにより起動できます。

## <a name="subscribe-to-a-workspace"></a>ワークスペースのサブスクライブ

ワークスペースをサブスクライブするには、2 つの方法があります。 職場または学校のアカウントから自分が利用できるリソースを検出することをクライアントで試行できます。あるいは、クライアントでリソースを検出できない場合は自分で直接、自分のリソースが置かれている URL を指定できます。 ワークスペースをサブスクライブしたら、次のいずれかの方法でリソースを起動できます。

- 接続センターに移動し、リソースをダブルクリックして起動します。
- [スタート] メニューに移動し、ワークスペース名のあるフォルダーを探すか、検索バーにリソース名を入力することもできます。

### <a name="subscribe-with-a-user-account"></a>ユーザー アカウントを使用してサブスクライブする

1. クライアントのメイン ページから、 **[サブスクライブする]** を選択します。
2. メッセージが表示されたら、自分のユーザー アカウントでサインインします。
3. リソースが接続センターに表示され、ワークスペース別にグループ化されます。

### <a name="subscribe-with-a-url"></a>URL を使用してサブスクライブする

1. クライアントのメイン ページから、 **[Subscribe with URL]** \(URL を使用してサブスクライブする\) を選択します。
2. ワークスペース URL または電子メール アドレスを入力します。
   - **ワークスペース URL** を使用する場合、管理者から付与されたものを使用します。 Windows 仮想デスクトップからリソースにアクセスする場合、次の URL のいずれかを使用できます。
     - Windows Virtual Desktop Fall 2019: `https://rdweb.wvd.microsoft.com/api/feeddiscovery/webfeeddiscovery.aspx`
     - Windows Virtual Desktop Spring 2020: `https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery`
   - 代わりに **[メール アドレス]** フィールドを使用する場合、メール アドレスを入力します。 これにより、メール アドレスに関連付けられている URL を検索するようにクライアントに指示されます (管理者が[メール検出](/windows-server/remote/remote-desktop-services/rds-email-discovery)を設定している場合)。
3. **[次へ]** を選択します。
4. メッセージが表示されたら、自分のユーザー アカウントでサインインします。
5. リソースがワークスペース別にグループ化された状態で接続センターに表示されるはずです。

## <a name="next-steps"></a>次のステップ

Windows デスクトップ クライアントの使用方法の詳細については、「[Windows デスクトップ クライアントの概要](/windows-server/remote/remote-desktop-services/clients/windowsdesktop/)」を参照してください。
