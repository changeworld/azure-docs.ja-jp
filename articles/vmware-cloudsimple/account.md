---
title: アカウント管理 - Azure VMware Solutions (AVS) ポータル
description: Azure VMware Solutions (AVS) ポータルでアカウントを管理する方法について説明します
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1fd7cb1c55fb664828448cef0b67ea9b16323bdf
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025369"
---
# <a name="manage-accounts-on-the-azure-vmware-solutions-avs-portal"></a>Azure VMware Solutions (AVS) ポータルでアカウントを管理する

AVS サービスを作成すると、AVS にアカウントが作成されます。 アカウントは、サービスが配置されている Azure サブスクリプションに関連付けられています。 サブスクリプション内に所有者ロールと共同作成者ロールを持つすべてのユーザーが AVS ポータルにアクセスできます。 AVS サービスに関連付けられた Azure サブスクリプション ID とテナント ID は、アカウント ページ上にあります。

AVS ポータルでアカウントを管理するには、[ポータルにアクセス](access-cloudsimple-portal.md)し、サイド メニューで **[アカウント]** を選択します。

**[概要]** を選択すると、会社の AVS の構成に関する情報が表示されます。 AVS プライベート クラウドの数、合計ストレージ、vSphere クラスターの構成、ノードの数、コンピューティング コアの数など、クラウド構成の現在の容量が表示されます。 現在の構成がすべてのニーズを満たしていない場合は、追加のノードを購入するためのリンクも表示されます。

## <a name="email-alerts"></a>メール アラート

AVS プライベート クラウドの構成への変更について通知するユーザーのメール アドレスを追加することができます。

1. **[Additional email alerts]\(追加のメール アラート\)** 領域で、 **[新規追加]** をクリックします。
2. メール アドレスを入力します。
3. Return キーを押します。  

エントリを削除するには、 **[X]** をクリックします。

## <a name="avs-operator-access"></a>AVS オペレーター アクセス

オペレーター アクセス設定を使用して、AVS ポータルへのサインインをサポート エンジニアに許可すると、トラブルシューティングに役立ちます。 既定では、この設定は有効です。 サポート エンジニアが顧客アカウントにログインしたときに実行するすべてのアクションが記録され、 **[アクティビティ]**  >  **[監査]** ページで確認できるようになります。

**[AVS operator access enabled]\(AVS オペレーター アクセスを有効にする\)** をクリックして、アクセスの有効または無効を切り替えます。
