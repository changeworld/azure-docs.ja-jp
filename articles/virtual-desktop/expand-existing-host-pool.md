---
title: 新しいセッション ホストで既存のホスト プールを拡張する - Azure
description: Windows Virtual Desktop で新しいセッション ホストを使用して既存のホスト プールを拡張する方法。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: d46d5618d7e3dc26775401f4a90d0c98d75ea31a
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2020
ms.locfileid: "82929215"
---
# <a name="expand-an-existing-host-pool-with-new-session-hosts"></a>新しいセッション ホストで既存のホスト プールを拡張する

>[!IMPORTANT]
>このコンテンツは、Azure Resource Manager Windows Virtual Desktop オブジェクトを含む Spring 2020 更新プログラムに適用されます。 Azure Resource Manager オブジェクトなしで Windows Virtual Desktop Fall 2019 リリースを使用している場合は、[この記事](./virtual-desktop-fall-2019/expand-existing-host-pool-2019.md)を参照してください。
>
> Windows Virtual Desktop Spring 2020 更新プログラムは現在、パブリック プレビュー段階です。 このプレビュー バージョンはサービス レベル アグリーメントなしで提供されており、運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

ホスト プール内の使用率を上げた場合、新しいセッション ホストで既存のホスト プールを拡張して、新しい負荷を処理する必要がある場合があります。

この記事では、新しいセッション ホストで既存のホスト プールを拡張する方法について説明します。

## <a name="what-you-need-to-expand-the-host-pool"></a>ホスト プールを拡張するために必要なもの

開始する前に、次のいずれかの方法を使用してホスト プールとセッション ホスト仮想マシン (VM) を作成済みであることを確認します。

- [Azure Portal](./create-host-pools-azure-marketplace.md)
- [PowerShell を使用してホスト プールを作成する](./create-host-pools-powershell.md)

ホスト プールとセッション ホスト VM を最初に作成したときから、次の情報も必要になります。

- VM のサイズ、イメージ、および名前のプレフィックス
- ドメイン参加管理者の資格情報
- 仮想ネットワーク名とサブネット名

## <a name="add-virtual-machines-with-the-azure-portal"></a>Azure portal を使用して仮想マシンを追加する

仮想マシンを追加してホスト プールを拡張するには:

1. Azure portal にサインインします。

2. **[Windows Virtual Desktop]** を検索して選択します。

3. 画面の左側にあるメニューで、 **[ホスト プール]** を選択し、仮想マシンを追加するホスト プールの名前を選択します。

4. 画面の左側にあるメニューから **[仮想マシン]** を選択します。

5. **[+追加]** を選択して、ホスト プールの作成を開始します。

6. [基本] タブを無視して、代わりに **[VM details]\(VM の詳細\)** タブを選択します。ここでは、ホスト プールに追加する仮想マシン (VM) の詳細を表示および編集できます。

7. VM を作成するリソース グループを選択し、リージョンを選択します。 現在使用しているリージョンまたは新しいリージョンを選択できます。
   
8. ホスト プールに追加するセッション ホストの数を **[Number of VMs]\(VM の数\)** に入力します。 たとえば、ホスト プールを 5 つのホストで拡張する場合は、「**5**」を入力します。
   
    >[!NOTE]
    >ホスト プール内のすべての VM を同じサイズにすることが重要であるため、VM のサイズまたはイメージを編集することはできません。
    
9. **[virtual network information]\(仮想ネットワークの情報\)** で、仮想マシンを参加させる仮想ネットワークとサブネットを選択します。 既存のマシンで現在使用しているのと同じ仮想ネットワークを選択することも、手順 7 で選択したリージョンに適した別の仮想ネットワークを選択することもできます。

10. **[管理者アカウント]** に、選択した仮想ネットワークに関連付けられている Active Directory ドメインのユーザー名とパスワードを入力します。 これらの資格情報は、仮想マシンを仮想ネットワークに参加させるために使用されます。

      >[!NOTE]
      >管理者名がここに記載されている情報に準拠していることを確認してください。 また、アカウントで MFA が有効になっていないことを確認してください。

11. 仮想マシンをグループ化するためのタグがある場合は、 **[タグ]** タブを選択します。 それ以外の場合は、このタブをスキップしてください。 

12. **[確認および作成]** タブを選択します。選択内容を確認し、問題がなければ **[作成]** を選択します。 

## <a name="next-steps"></a>次のステップ

既存のホスト プールを拡張したので、Windows Virtual Desktop クライアントにサインインして、ユーザー セッションの一部としてそれらをテストできるようになりました。 次のいずれかのクライアントとのセッションに接続できます。

- [Windows デスクトップ クライアントを使用して接続する](./connect-windows-7-and-10.md)
- [Web クライアントに接続する](./connect-web.md)
- [Android クライアントに接続する](./connect-android.md)
- [macOS クライアントに接続する](./connect-macos.md)
- [iOS クライアントに接続する](./connect-ios.md)
