---
title: "Azure での Linux VM の再起動メンテナンス | Microsoft Docs"
description: "Linux 仮想マシンの再起動メンテナンス"
services: virtual-machines-linux
documentationcenter: 
author: zivr
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: eb4b92d8-be0f-44f6-a6c3-f8f7efab09fe
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/27/2017
ms.author: zivr
ms.translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: c02b2878462afb107c81317c9ea0a317cb9ce838
ms.contentlocale: ja-jp
ms.lasthandoff: 04/19/2017


---

# <a name="vm-restarting-maintenance"></a>VM の再起動メンテナンス

基盤となるインフラストラクチャに計画メンテナンスを行う場合、VM を再起動することがあります。 Azure でホストされた VM の可用性に影響があるため、次を使用できます。

-   影響が発生する 30 日以上前での通知の送信。

-   VM ごとのメンテナンス ウィンドウの表示。

-   VM に影響を与えるメンテナンスの正確な時刻の設定による柔軟性と制御。

Microsoft Azure でのメンテナンスは繰り返しスケジュールされます。 最初の繰り返しでは、新しい修正事項や新機能の展開に関わるリスクを軽減させるため、範囲が狭く指定されます。 その後、複数のリージョンにわたって範囲が拡大されます (同じリージョンのペアになることはありません)。 1 回のメンテナンスの繰り返しにつき、1 台の VM が含まれます。 繰り返しが中止された場合、残りの VM は将来実施される別のメンテナンスに含まれます。

計画メンテナンスの反復処理には、プリエンプティブなメンテナンス期間とスケジュールされたメンテナンス期間という、2 つのフェーズがあります。

**プリエンプティブなメンテナンス期間**では、VM 上でのメンテナンスを柔軟に開始できるようにします。 これにより、VM に影響が及ぶ期間、更新の内容、各 VM 間のメンテナンス間隔を決定することができます。 各 VM のメンテナンスが計画済みかどうかをクエリして、最後に開始したメンテナンス要求の結果を確認できます。

**予定メンテナンス期間**は、Azure によってお使いの VM のメンテナンスがスケジュールされた日時を指します。 先行メンテナンス期間の後に続くこの期間でも、メンテナンス期間について照会することができますが、この時点でメンテナンスを調整することはできません。

## <a name="availability-considerations-during-planned-maintenance"></a>計画メンテナンス実施時の可用性に関する考慮事項 

### <a name="paired-regions"></a>ペアになっているリージョン

各 Azure リージョンは、同じ geo 内の別のリージョンと組み合わせて、リージョン ペアにして使用します。 メンテナンスでは、リージョン ペアの一方のリージョンの仮想マシン インスタンスだけが更新されます。 たとえば、米国中北部の仮想マシンが更新されるとき、同時に米国中南部の仮想マシンが更新されることはありません。 リージョン間のフェールオーバーまたは負荷分散が可能なように、時間をずらしてスケジュールされます。 ただし、北ヨーロッパなどのその他のリージョンは、米国東部と同時にメンテナンスされる可能性があります。
詳細については、「[Azure リージョンのペア](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)」を参照してください。

### <a name="single-instance-vms-vs-availability-set-or-vm-scale-set"></a>シングル インスタンス VM と可用性セットまたは VM Scale Sets

Azure で仮想マシンを使用してワークロードをデプロイする場合、アプリケーションの高可用性を実現するため可用性セット内に VM を作成できます。 このように構成されていれば、障害発生時やメンテナンスの間であっても、少なくとも 1 つの仮想マシンを使用できるようになります。

可用性セット内の個々の VM は最大 20 個の更新ドメインに分散されます。 計画メンテナンス中は、指定された時間に 1 つの更新ドメインのみ影響を受けます。 影響を受ける更新ドメインの順序は、計画メンテナンス時の順番とは異なる可能性があります。 シングル インスタンス VM (可用性セットの一部ではない) では、一緒に影響を受ける VM の数や種類を予測したり決定したりする方法はありません。

Virtual Machine Scale Sets は、同一の VM のセットをデプロイして管理するために使用できる Azure コンピューティング リソースです。
Scale Sets では、更新ドメインの形式で可用性セットに類似する保証を提供します。 

高可用性を実現するための仮想マシン構成の詳細については、「[*Windows 仮想マシンの可用性管理*](manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。

### <a name="scheduled-events"></a>スケジュールされたイベント

Azure Metadata Service では、Azure でホストされている仮想マシンに関する情報を発見することができます。 公開されているカテゴリの 1 つであるスケジュールされたイベントによって、今後のイベント (再起動など) に関する情報が表示されるため、アプリケーションはそのイベントに対して準備をして、中断を最小限に抑えることができます。

スケジュールされたイベントの詳細については、「[Azure Metadata Service: スケジュールされたイベント](../virtual-machines-scheduled-events.md)」を参照してください。

## <a name="maintenance-discovery-and-notifications"></a>メンテナンスの検出と通知

メンテナンス スケジュールは、個々の VM のレベルで顧客に表示されます。 Azure Portal、API、PowerShell、または CLI を使用して、先行メンテナンス期間または予定メンテナンス期間を照会できます。 また、処理中に 1 つ以上の VM が影響を受ける場所は、通知 (電子メール) を受信することができます。

先行メンテナンス期間および予定メンテナンス期間は、いずれも開始時に通知されます。 Azure サブスクリプションごとに 1 つの通知を受信することができます。 既定では、サブスクリプションの管理者と共同管理者に通知が送信されます。 また、メンテナンスの通知の対象ユーザーを設定することもできます。

### <a name="view-the-maintenance-window-in-the-portal"></a>ポータルでのメンテナンス期間の表示 

Azure Portal を使用して、VM のメンテナンス スケジュールを検索できます。

1.  Azure ポータルにサインインします。

2.  [**仮想マシン**] ブレードが開きます。

3.  [**列**] をクリックすると、選択可能な列の一覧が開きます。

4.  [**メンテナンス期間**] 列を選択して追加します。 該当するメンテナンス期間にスケジュールされたメンテナンスがある VM が表示されます。 メンテナンスが完了するか中止されると、メンテナンス期間は表示されなくなります。

### <a name="query-maintenance-details-using-the-azure-api"></a>Azure API を使用したメンテナンス詳細の照会

[[API での VM 情報の取得](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-get)] を使用してインスタンス ビューを検索し、個々の VM のメンテナンスに関する詳細を検出します。 応答には、次の要素が含まれています。

  - isCustomerInitiatedMaintenanceAllowed: VM で先行メンテナンス期間の再デプロイを開始できるかどうかが示されます。

  - preMaintenanceWindowStartTime: 先行メンテナンス期間の開始時刻。

  - preMaintenanceWindowEndTime: 先行メンテナンス期間の終了時刻。 この後は VM 上のメンテナンスを開始できなくなります。
    
  - maintenanceWindowStartTime: VM が影響を受ける場合、予定メンテナンス期間の開始時刻。

  - maintenanceWindowEndTime: 予定メンテナンス期間の終了時刻。
  
  - lastOperationResultCode: 最後のメンテナンスの再デプロイ操作の結果。
 
  - lastOperationMessage: 最後のメンテナンスの再デプロイ操作の結果を記述したメッセージ。


## <a name="pre-emptive-redeploy"></a>プリエンプティブな再デプロイ

プリエンプティブな再デプロイ操作では、Azure でメンテナンスを VM に適用する時刻を制御できる柔軟性を提供します。 計画メンテナンスは、各 VM を再起動する正確な時間を決定できるプリエンプティブなメンテナンス期間に開始します。 このような機能が便利となるユース ケースを次に示します。

-   メンテナンスでは、エンド カスタマーとの調整が必要です。

-   Azure によって提供される予定メンテナンス期間は十分ではありません。
    該当期間が 1 週間で最も忙しい時間帯だったり、期間が長すぎたりする場合があります。

-   マルチ インスタンスまたは多層アプリケーションでは、2 つの VM 間に十分な時間を提供するか、特定の順序に従うことが必要です。

VM のプリエンプティブな再デプロイを要求するときは、VM をすでに更新済みのノードに移動してから電源をオンにすると、すべての構成オプションと関連するリソースが保持されます。 一方で、VM を再デプロイすると、一時ディスクが失われ、仮想ネットワーク インターフェイスに関連付けられた動的 IP アドレスが更新されます。

プリエンプティブな再デプロイは VM ごとに 1 回有効です。 処理中にエラーが発生する場合は、操作が中止されるため VM に影響はありません。また、このデプロイは計画メンテナンスの繰り返しから除外されます。 後で新しいスケジュールについて通知を受け、VM での影響をスケジュール、順序付ける新しい機会が提示されます。

