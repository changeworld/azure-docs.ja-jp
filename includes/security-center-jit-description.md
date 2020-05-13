---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 02/24/2020
ms.topic: include
ms.openlocfilehash: 78bf29a170152666d82ec26504ee8f61ed90636a
ms.sourcegitcommit: acc558d79d665c8d6a5f9e1689211da623ded90a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/30/2020
ms.locfileid: "82615995"
---
## <a name="attack-scenario"></a>攻撃シナリオ

ブルート フォース攻撃では、VM にアクセスする手段として一般に管理ポートがターゲットとされます。 アクセスに成功した場合、攻撃者は VM の制御を奪い、攻撃対象の環境への足掛かりを築くことができます。

ブルート フォース攻撃への露出を減らす方法の 1 つとして、ポートの開放時間を制限するという方法があります。 管理ポートを常に開放しておく必要はありません。 管理ポートを開放しておく必要があるのは、管理タスクやメンテナンス タスクを実行する場合など、VM に接続している間だけです。 Just-In-Time が有効になっている場合、Security Center では[ネットワーク セキュリティ グループ](../articles/virtual-network/security-overview.md#security-rules) (NSG) および Azure Firewall ルールが使用されます。このルールにより管理ポートへのアクセスが制限されるため、攻撃者は管理ポートをターゲットにすることができなくなります。

![Just-In-Time のシナリオ](../articles/security-center/media/security-center-just-in-time/just-in-time-scenario.png)

## <a name="how-does-jit-access-work"></a>JIT アクセスはどのように機能しますか?

Just-In-Time が有効になっている場合、Security Center では NSG ルールの作成により Azure VM への受信トラフィックがロックダウンされます。 ユーザーは VM 上の受信トラフィックがロックダウンされるポートを選択します。 これらのポートは、Just-In-Time ソリューションによって制御されます。

ユーザーが VM へのアクセス権を要求すると、そのユーザーに VM への[ロール ベースのアクセス制御 (RBAC)](../articles/role-based-access-control/role-assignments-portal.md) のアクセス許可があることが、Security Center によってチェックされます。 要求が承認されると、Security Center では、選択したポートおよび要求されたソース IP アドレスまたは範囲への受信トラフィックを指定された時間だけ許可するように、ネットワーク セキュリティ グループ (NSG) および Azure Firewall が自動的に構成されます。 指定された時間が経過すると、Security Center により NSG が以前の状態に復元されます。 既に確立されているこれらの接続は中断されません。ただし次のようになります。

 > [!NOTE]
 > Azure Firewall の背後にある VM への JIT アクセス要求が承認されると、Security Center は NSG とファイアウォールの両方のポリシー規則を自動的に変更します。 この規則によって、選択したポートおよび要求されたソース IP アドレスまたは範囲への受信トラフィックが指定された時間だけ許可されます。 指定された期間が経過すると、Security Center は ファイアウォールと NSG の規則を以前の状態に復元します。


## <a name="roles-that-can-read-jit-policies"></a>JIT ポリシーを読み取ることができるロール

**閲覧者**ロールと **SecurityReader** ロールは両方ともポリシーを読み取ることができます。

## <a name="permissions-needed-to-configure-and-use-jit"></a>JIT を構成および使用するために必要なアクセス許可

JIT で動作するカスタム ロールを作成する場合は、次の詳細情報が必要になります。

| ユーザーを有効にする目的: | 設定するアクセス許可|
| --- | --- |
| VM の JIT ポリシーを構成または編集する | *これらのアクションをロールに割り当てます。*  <ul><li>VM に関連付けられているサブスクリプションまたはリソース グループの範囲:<br/> `Microsoft.Security/locations/jitNetworkAccessPolicies/write` </li><li> VM のサブスクリプションまたはリソース グループの範囲: <br/>`Microsoft.Compute/virtualMachines/write`</li></ul> | 
|VM への JIT アクセスを要求する | *これらのアクションをユーザーに割り当てます。*  <ul><li>VM に関連付けられているサブスクリプションまたはリソース グループの範囲:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action` </li><li>VM に関連付けられているサブスクリプションまたはリソース グループの範囲:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/*/read` </li><li>  VM のサブスクリプションまたはリソース グループの範囲:<br/> `Microsoft.Compute/virtualMachines/read` </li><li>  VM のサブスクリプションまたはリソース グループの範囲:<br/> `Microsoft.Network/networkInterfaces/*/read` </li></ul>|
|JIT ポリシーの読み取り| *これらのアクションをユーザーに割り当てます。*  <ul><li>`Microsoft.Security/locations/jitNetworkAccessPolicies/read`</li><li>`Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action`</li><li>`Microsoft.Security/policies/read`</li><li>`Microsoft.Compute/virtualMachines/read`</li><li>`Microsoft.Network/*/read`</li>|