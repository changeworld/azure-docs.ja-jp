---
title: Kerberos キー配布センター プロキシ Windows Virtual Desktop のセットアップ - Azure
description: Kerberos キー配布センター プロキシを使用するように Windows Virtual Desktop ホスト プールを設定する方法について説明します。
author: Heidilohr
ms.topic: how-to
ms.date: 01/26/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: f123659941eaeb3b8ceeb6a999abf836eb9cf5ea
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/26/2021
ms.locfileid: "98798012"
---
# <a name="configure-a-kerberos-key-distribution-center-proxy"></a>Kerberos キー配布センター プロキシを構成する

この記事では、ホスト プールに対して Kerberos キー配布センター (KDC) プロキシを構成する方法について説明します。 このプロキシにより、組織は企業の境界外で Kerberos を使用して認証を行うことができます。 たとえば、KDC プロキシを使用して、外部クライアントのスマートカード認証を有効にすることができます。

## <a name="how-to-configure-the-kdc-proxy"></a>KDC プロキシを構成する方法

KDC プロキシを構成するには、次の手順を実行します。

1. Azure Portal に管理者としてサインインします。

2. [Windows Virtual Desktop] ページにアクセスします。

3. KDC プロキシを有効にするホスト プールを選択し、 **[RDP プロパティ]** を選択します。

    > [!div class="mx-imgBorder"]
    > ![[Azure portal] ページのスクリーンショット。ホスト プールを選択したユーザー、ホスト プールの例の名前、RDP プロパティの順に表示されます。](media/rdp-properties.png)

4. **[詳細設定]** タブを選択し、次の形式でスペースなしで値を入力します。

    > kdcproxyname:s:\<fqdn\>

    > [!div class="mx-imgBorder"]
    > ![選択した [詳細設定] タブが表示されたスクリーンショット。手順 4 で説明したように値を入力します。](media/advanced-tab-selected.png)

5. **[保存]** を選択します。

6. これで、選択したホスト プールで、入力した kdcproxyname フィールドを含む RDP 接続ファイルの発行が開始されます。

## <a name="next-steps"></a>次のステップ

リモート デスクトップ サービスの RDGateway ロールには、KDC プロキシ サービスが含まれています。 Windows Virtual Desktop のターゲットに設定する方法については、「[Windows Virtual Desktop で RD ゲートウェイのロールをデプロイする](rd-gateway-role.md)」を参照してください。
