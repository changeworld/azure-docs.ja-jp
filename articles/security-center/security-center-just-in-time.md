---
title: Azure Security Center でのジャスト イン タイム仮想マシン アクセス | Microsoft Docs
description: このドキュメントでは、Azure Security Center でのジャスト イン タイム VM アクセスにより、Azure 仮想マシンへのアクセスを制御しやすくする方法を示します。
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/05/2018
ms.author: rkarlin
ms.openlocfilehash: 18c05444c151a87048db71b039845e26796126b8
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39525945"
---
# <a name="manage-virtual-machine-access-using-just-in-time"></a>ジャスト イン タイムを使用して仮想マシンへのアクセスを管理する

ジャスト イン タイム仮想マシン (VM) アクセスを使用すると、Azure VM への受信トラフィックをロックダウンすることができるので、攻撃に対する露出が減り、VM への接続が必要な場合は簡単にアクセスできます。

> [!NOTE]
> ジャスト イン タイム機能は、Security Center の Standard レベルで利用できます。  Security Center の価格レベルの詳細については、[価格](security-center-pricing.md)に関するページを参照してください。
>
>

## <a name="attack-scenario"></a>攻撃シナリオ

ブルート フォース攻撃では、VM にアクセスする手段として一般に管理ポートがターゲットとされます。 アクセスに成功した場合、攻撃者は VM の制御を奪い、攻撃対象の環境への足掛かりを築くことができます。

ブルート フォース攻撃への露出を減らす方法の 1 つとして、ポートの開放時間を制限するという方法があります。 管理ポートを常に開放しておく必要はありません。 管理ポートを開放しておく必要があるのは、管理タスクやメンテナンス タスクを実行する場合など、VM に接続している間だけです。 ジャスト イン タイムが有効になっている場合、Security Center では[ネットワーク セキュリティ グループ](../virtual-network/security-overview.md#security-rules) (NSG) ルールが使用されます。このルールにより管理ポートへのアクセスが制限されるため、攻撃者は管理ポートをターゲットにすることができなくなります。

![ジャスト イン タイムのシナリオ][1]

## <a name="how-does-just-in-time-access-work"></a>ジャスト イン タイム アクセスの仕組み

ジャスト イン タイムが有効になっている場合、Security Center では NSG ルールの作成により Azure VM への受信トラフィックがロックダウンされます。 ユーザーは VM 上の受信トラフィックがロックダウンされるポートを選択します。 これらのポートは、ジャスト イン タイム ソリューションによって制御されます。

ユーザーが VM へのアクセスを要求すると、そのユーザーに、VM への書き込みアクセス権を付与する[ロールベースのアクセス制御 (RBAC)](../role-based-access-control/role-assignments-portal.md) アクセス許可があるかとうかが、Security Center によってチェックされます。 ユーザーが書き込みアクセス許可を持っている場合は要求が承認され、Security Center では、選択したポートへの受信トラフィックを指定された時間だけ許可するように、ネットワーク セキュリティ グループ (NSG) が自動的に構成されます。 指定された時間が経過すると、Security Center により NSG が以前の状態に復元されます。 既に確立されているこれらの接続は中断されません。ただし次のようになります。

> [!NOTE]
> Security Center のジャスト イン タイム VM アクセスでは、現在 Azure Resource Manager 経由でデプロイされた VM のみがサポートされています。 クラシック デプロイ モデルと Resource Manager デプロイ モデルの詳細については、[Azure Resource Manager とクラシック デプロイの比較](../azure-resource-manager/resource-manager-deployment-model.md)に関するページを参照してください。
>
>

## <a name="using-just-in-time-access"></a>ジャスト イン タイム アクセスの使用

1. **[Security Center]** ダッシュボードを開きます。

2. 左のウィンドウで、**[Just In Time VM アクセス]** を選択します。

![[Just In Time VM アクセス] タイル][2]

**[Just In Time VM アクセス]** ウィンドウが開きます。

![[Just In Time VM アクセス] タイル][10]

**[Just In Time VM アクセス]** には、VM の状態に関する情報が表示されます。

- **[構成済み]** - ジャスト イン タイム VM アクセスをサポートするように構成されている VM。 表示されるデータは 1 週間以内のものであり、VM ごとに承認済みの要求の数、最終アクセス日時、最後のユーザーの情報が含まれます。
- **[推奨]** - ジャスト イン タイム VM アクセスをサポートできるが、そのように構成されてはいない VM。 これらの VM ではジャスト イン タイム VM アクセス制御を有効にすることをお勧めします。 「[ジャスト イン タイム アクセス ポリシーの構成](#configuring-a-just-in-time-access-policy)」を参照してください。
- **[推奨なし]** - 以下のような VM には、ジャスト イン タイム VM アクセスは推奨されない場合があります。
  - NSG がない - ジャスト イン タイム ソリューションには設定済みの NSG が必要です。
  - クラシック VM - Security Center のジャスト イン タイム VM アクセスでは、現在 Azure Resource Manager 経由でデプロイされた VM のみがサポートされています。 ジャスト イン タイム ソリューションではクラシック デプロイはサポートされていません。
  - その他 - VM がこのカテゴリに含まれるのは、サブスクリプションまたはリソース グループのセキュリティ ポリシー内でジャスト イン タイム ソリューションが無効になっている場合か、VM にパブリック IP と設定済みの NSG がない場合です。

## <a name="configuring-a-just-in-time-access-policy"></a>ジャスト イン タイム アクセス ポリシーの構成

有効にする VM を選択するには、以下の手順に従います。

1. **[Just In Time VM アクセス]** で **[推奨]** タブを選択します。

  ![ジャスト イン タイム アクセスを有効にする][3]

2. **[仮想マシン]** で、有効にする VM を選択します。 VM の横にチェックマークが付きます。
3. **[<選択した VM 数> 台の VM で JIT を有効にする]** を選択します。
4. **[保存]** を選択します。

### <a name="default-ports"></a>既定のポート

Security Center でジャスト イン タイムの有効化が推奨される既定のポートを確認できます。

1. **[Just In Time VM アクセス]** で **[推奨]** タブを選択します。

  ![既定のポートを表示する][6]

2. **[VM]** で VM を選択します。 VM の横にチェックマークが付き、**[JIT VM アクセス構成]** が開きます。 このブレードに既定のポートが表示されます。

### <a name="add-ports"></a>ポートを追加する

**[JIT VM アクセス構成]** では、ジャスト イン タイム ソリューションを有効にする新しいポートを追加して構成することもできます。

1. **[JIT VM アクセス構成]** で **[追加]** を選択します。 **[Add port configuration]\(ポート構成の追加\)** が開きます。

  ![ポート構成][7]

2. **[Add port configuration]\(ポート構成の追加\)** では、ポート、プロトコルの種類、許可されるソース IP、最大要求時間を指定します。

  許可されるソース IP とは、承認された要求に応じてアクセスが許可される IP の範囲です。

  最大要求時間とは、特定のポートを開放しておくことができる最大時間枠です。

3. **[OK]** を選択します。

## <a name="requesting-access-to-a-vm"></a>VM へのアクセス権の要求

VM へのアクセス権を要求するには、以下の手順に従います。

1. **[Just In Time VM アクセス]** で、**[構成済み]** タブを選択します。
2. **[VM]** でアクセスを有効にする VM を選択します。 VM の横にチェックマークが付きます。
3. **[アクセス権の要求]** を選択します。 **[アクセス権の要求]** が開きます。

  ![VM へのアクセス権を要求する][4]

4. **[アクセス権の要求]** では、開放するポート、ポートの開放先のソース IP、ポートを開放しておく時間枠を VM ごとに構成します。 要求できるのは、ジャスト イン タイム ポリシーで構成されているポートへのアクセス権のみです。 ポートごとにジャスト イン タイム ポリシーから派生した最大許容時間があります。
5. **[Open ports]\(ポートを開く\)** を選択します。

> [!NOTE]
> ユーザーが VM へのアクセスを要求すると、そのユーザーに、VM への書き込みアクセス権を付与する[ロールベースのアクセス制御 (RBAC)](../role-based-access-control/role-assignments-portal.md) アクセス許可があるかとうかが、Security Center によってチェックされます。 書き込みのアクセス許可がある場合は、要求が承認されます。
>
>

> [!NOTE]
> アクセスを要求しているユーザーがプロキシの背後にいる場合は、"My IP" オプションが機能しない場合があります。 組織のすべての範囲を定義する必要がある場合があります。
>
>

## <a name="editing-a-just-in-time-access-policy"></a>ジャスト イン タイム アクセス ポリシーの編集

VM の既存のジャスト イン タイム ポリシーを変更する場合、その VM 用に開放する新しいポートを追加して構成するか、既に保護されているポートに関連するその他のパラメーターを変更します。

VM の既存のジャスト イン タイム ポリシーを編集するには、以下のように **[構成済み]** タブを使用します。

1. **[VM]** で、ポートを追加する VM の行内にある 3 つの点をクリックしてその VM を選択します。 メニューが開きます。
2. メニューの **[編集]** を選択します。 **[JIT VM アクセスの構成]** が開きます。

  ![ポリシーを編集する][8]

3. **[JIT VM アクセス構成]** では、既に保護されているポートをクリックしてそのポートの既存の設定を編集するか、または **[追加]** を選択することができます。 **[Add port configuration]\(ポート構成の追加\)** が開きます。

  ![ポートを追加する][7]

4. **[Add port configuration]\(ポート構成の追加\)** では、ポート、プロトコルの種類、許可されるソース IP、最大要求時間を指定します。
5. **[OK]** を選択します。
6. **[保存]** を選択します。

## <a name="auditing-just-in-time-access-activity"></a>ジャスト イン タイム アクセス アクティビティの監査

ログ検索を使用して VM アクティビティについての情報が得ることができます。 ログを表示するには、以下の手順に従います。

1. **[Just In Time VM アクセス]** で、**[構成済み]** タブを選択します。
2. **[VM]** で、情報を表示する VM の行内にある 3 つの点をクリックしてその VM を選択します。 メニューが開きます。
3. メニューで **[アクティビティ ログ]** を選択します。 **[アクティビティ ログ]** が開きます。

  ![[アクティビティ ログ] を選択する][9]

  **[アクティビティ ログ]** には、選択した VM の前回の操作、時間、日付、サブスクリプションがフィルター処理されて表示されます。

  ![アクティビティ ログを表示する][5]

**[すべての項目を csv としてダウンロードするにはここをクリックしてください。]** を選択すると、ログ情報をダウンロードできます。

フィルターを変更し、**[適用]** を選択して検索ログを作成します。

## <a name="using-just-in-time-vm-access-via-rest-apis"></a>REST API によるジャスト イン タイム VM アクセスの使用

Just In Time VM アクセス機能は、Azure Security Center API を通じて使用できます。 この API を使用すると、構成済みの VM に関する情報を取得したり、新しい VM を追加したり、VM へのアクセスを要求したりすることができます。 Just In Time REST API について詳しくは、「[Jit Network Access Policies (JIT ネットワーク アクセス ポリシー)](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies)」をご覧ください。

### <a name="configuring-a-just-in-time-policy-for-a-vm"></a>VM のジャスト イン タイム ポリシーの構成

特定の VM でジャスト イン タイム ポリシーを構成するには、PowerShell セッションで次のコマンドを実行する必要があります: Set-ASCJITAccessPolicy。
詳細については、コマンドレットに関するドキュメントを参照してください。

### <a name="requesting-access-to-a-vm"></a>VM へのアクセス権の要求

ジャスト イン タイム ソリューションで保護されている特定の VM にアクセスするには、PowerShell セッションで次のコマンドを実行する必要があります: Invoke-ASCJITAccess。
詳細については、コマンドレットに関するドキュメントを参照してください。

## <a name="next-steps"></a>次の手順
この記事では、Security Center のジャスト イン タイム VM アクセスを活用して Azure 仮想マシンへのアクセスを制御する方法について説明しました。

セキュリティ センターの詳細については、次を参照してください。

- [セキュリティ ポリシーの設定](security-center-policies.md) -- Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明します。
- [セキュリティに関する推奨事項の管理](security-center-recommendations.md) -- 推奨事項に従って Azure リソースを保護する方法について説明します。
- [セキュリティ正常性の監視](security-center-monitoring.md) -- Azure リソースの正常性を監視する方法について説明します。
- [セキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md) -- セキュリティの警告の管理と対応の方法について説明します。
- [パートナー ソリューションの監視](security-center-partner-solutions.md) -- パートナー ソリューションの正常性状態を監視する方法について説明します。
- [Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md) -- このサービスの使用に関してよく寄せられる質問が記載されています。
- [Azure セキュリティ ブログ](https://blogs.msdn.microsoft.com/azuresecurity/) -- Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。


<!--Image references-->
[1]: ./media/security-center-just-in-time/just-in-time-scenario.png
[2]: ./media/security-center-just-in-time/just-in-time.png
[10]: ./media/security-center-just-in-time/just-in-time-access.png
[3]: ./media/security-center-just-in-time/enable-just-in-time-access.png
[4]: ./media/security-center-just-in-time/request-access-to-a-vm.png
[5]: ./media/security-center-just-in-time/activity-log.png
[6]: ./media/security-center-just-in-time/default-ports.png
[7]: ./media/security-center-just-in-time/add-a-port.png
[8]: ./media/security-center-just-in-time/edit-policy.png
[9]: ./media/security-center-just-in-time/select-activity-log.png
