---
title: Azure の Red Hat Enterprise Linux イメージ | Microsoft Docs
description: Microsoft Azure の Red Hat Enterprise Linux イメージについて説明します
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/18/2019
ms.author: alsin
ms.openlocfilehash: 27cd5b775fbd2af58d93d539420262665d70ead4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75476937"
---
# <a name="overview-of-red-hat-enterprise-linux-images"></a>Red Hat Enterprise Linux イメージの概要
この記事では、名前付けと保有期間に関するポリシーと、Azure Marketplace 内で利用可能な Red Hat Enterprise Linux (RHEL) イメージについて説明します。

すべてのバージョンの RHEL に対する Red Hat のサポート ポリシーに関する情報は、「[Red Hat Enterprise Linux Life Cycle \(Red Hat Enterprise Linux のライフ サイクル\)](https://access.redhat.com/support/policy/updates/errata)」ページに記載されています。 料金の詳細については、[Azure 料金計算ツール](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)を参照してください。

>[!IMPORTANT]
> Azure マーケットプレースで現在使用可能な RHEL イメージは、BYOS (Bring-Your-Own-Subscription)または従量課金制 (PAYG) ライセンス モデルをサポートしています。 [Azure ハイブリッド特典](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing)と、BYOS と PAYG の動的な切り替えはサポートされていません。 ライセンス モードの切り替えには、対応するイメージから VM を再デプロイする必要があります。

>[!NOTE]
> Azure Marketplace の RHEL イメージに関する問題については、Microsoft にサポート チケットを提出してください。

## <a name="images-available-in-azure"></a>Azure で使用できるイメージ
Marketplace で "Red Hat" を検索するときや、Azure portal の UI でリソースを作成するときは、利用可能なすべての RHEL イメージのサブセットのみが表示されます。 Azure CLI/PowerShell/API を使用して、利用可能な VM イメージの完全なセットをいつでも取得することができます。

Azure で利用可能な Red Hat イメージの完全なセットを表示するには、次のコマンドを実行します

```azurecli-interactive
az vm image list --publisher RedHat --all
```

### <a name="naming-convention"></a>命名規則
Azure の VM イメージは、発行元、プラン、SKU、バージョンごとに整理されます。 発行元:プラン:SKU:バージョンの組み合わせはイメージ URN であり、使用されるイメージを一意に識別します。

たとえば、`RedHat:RHEL:7-LVM:7.6.2018103108` は、2018 年 10 月 31 日に構築された、RHEL 7.6 の LVM パーティション分割されたイメージを表します。

RHEL 7.6 VM を作成する方法の例を以下に示します。
```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-LVM:7.6.2018103108 --no-wait
```

### <a name="the-latest-moniker"></a>"最新" のモニカー
Azure REST API では、特定のバージョンではなく、バージョンで "最新" のモニカーを使用できます。 "最新" のものを使用することで、特定の発行元、プラン、SKU で利用可能な最新のイメージがプロビジョニングされます。

たとえば、`RedHat:RHEL:7-LVM:latest` は、利用可能な最新の RHEL 7 ファミリの LVM パーティション分割されたイメージを表します。

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-LVM:latest --no-wait
```

>[!NOTE]
> 通常、最新のものを判断するためのバージョン比較では、[CompareTo メソッド](https://msdn.microsoft.com/library/a5ts8tb6.aspx)の規則に従います。

### <a name="rhel-6-image-types"></a>RHEL 6 イメージの種類
RHEL 6.x イメージの場合、イメージの種類は次のようになります。

|Publisher | プラン | SKU 値 | Version | 詳細
|----------|-------|-----------|---------|--------
|RedHat | RHEL | マイナー バージョン (例: 6.9) | RHEL マイナー バージョンと公開日の連結された値 (例: 6.9.2018010506) | すべての標準 RHEL 6.x イメージは、この規則に従います。
|RedHat | rhel-byos | rhel-raw69 | RHEL マイナー バージョンと公開日の連結された値 (例: 6.9.20181023) | このイメージは、RHEL 6.9 BYOS イメージです。
|RedHat | RHEL | RHEL-SAP-APPS | RHEL マイナー バージョンと公開日の連結された値 (例: 6.8.2017053118) | これは RHEL 6.8 for SAP アプリケーション イメージです。 ベース RHEL リポジトリに加えて、SAP アプリケーション リポジトリへのアクセス権を持ちます。
|RedHat | RHEL | RHEL-SAP-HANA | RHEL マイナー バージョンと公開日の連結された値 (例: 6.7.2017053121) | これは RHEL 6.7 for SAP HANA イメージです。 ベース RHEL リポジトリに加えて、SAP HANA リポジトリへのアクセス権を持ちます。

### <a name="rhel-7-image-types"></a>RHEL 7 イメージの種類
RHEL 7.x イメージの場合、いくつかの異なる種類のイメージがあります。 次の表は、提供されているさまざまなイメージのセットを示しています。 完全な一覧を表示するには、AZ CLI コマンド `az vm image list --publisher redhat --all` を使用します。

>[!NOTE]
> 特に指定がない限り、すべてのイメージは LVM パーティション分割されており、通常の RHEL リポジトリ (つまり、E4S ではなく EUS) に接続されます。 今後は、LVM パーティション分割されたイメージのみを公開するように移行しますが、この決定についてフィードバックをお寄せください。 SAP 用の拡張更新サポートおよび更新サービスの詳細については、[Red Hat Enterprise Linux のライフ サイクルに関するページ](https://access.redhat.com/support/policy/updates/errata)を参照してください。

|Publisher | プラン | SKU 値 | Version | 詳細
|----------|-------|------------|---------|--------
|RedHat | RHEL | マイナー バージョン (例: 7.6) | RHEL マイナー バージョンと公開日の連結された値 (例: 7.6.2019102813) | 2019 年 4 月より前に発行されたイメージは、標準 RHEL リポジトリに接続されます。 2019 年 4 月以降に発行されたイメージは、特定のマイナー バージョンのバージョン ロックを可能にするために、Red Hat の Extended Update Support (EUS) リポジトリに接続されます。 通常のリポジトリを使用するお客様は、SKU 値に 7-LVM または 7-RAW が含まれるイメージを使用する必要があります (詳細は以下を参照)。 RHEL 7.7 以降のイメージは、LVM パーティション分割されています。 このカテゴリ内の他のすべてのイメージは、未加工でパーティション分割されています。
|RedHat | RHEL | 7-RAW | RHEL マイナー バージョンと公開日の連結された値 (例: 7.6.2019102813) | これらのイメージは、未加工でパーティション分割されています (つまり、論理ボリュームは追加されていません)。
|RedHat | RHEL | 7-RAW-CI | RHEL マイナー バージョンと公開日の連結された値 (例: 7.6.2019072418) | これらのイメージは、未加工でパーティション分割されており (つまり、論理ボリュームは追加されていません)、プロビジョニングにクラウド初期化が使用されます。
|RedHat | RHEL | 7-LVM | RHEL マイナー バージョンと公開日の連結された値 (例: 7.6.2019062414) | これらのイメージは、LVM パーティション分割されています。
|RedHat | rhel-byos | rhel-{lvm,raw} | RHEL マイナー バージョンと公開日の連結された値 (例: 7.7.20190819) | これらのイメージは RHEL 7 BYOS イメージです。 これらはどのリポジトリにも接続されず、RHEL Premium 料金は課金されません。 RHEL BYOS イメージに関心がある場合は、[アクセス権を要求してください](https://aka.ms/rhel-byos)。 SKU 値は末尾がマイナー バージョンであり、イメージが未加工であるか、または LVM パーティション分割されているかを示します。 たとえば、SKU 値 rhel-lvm77 は、LVM でパーティション分割された RHEL 7.7 イメージを示します。
|RedHat | RHEL | RHEL-SAP | RHEL マイナー バージョンと公開日の連結された値 (例: 7.6.2019071300) | これらのイメージは RHEL for SAP イメージです。 RHEL E4S リポジトリに加えて、SAP HANA リポジトリとアプリケーション リポジトリへのアクセス権を持ちます。 課金には、基本コンピューティング料金に加えて RHEL Premium と SAP Premium が含まれます。
|RedHat | RHEL | RHEL-SAP-HA | RHEL マイナー バージョンと公開日の連結された値 (例: 7.6.2019062320) | これらのイメージは、高可用性および更新サービスを備えた RHEL for SAP イメージです。 RHEL E4S リポジトリに加えて、SAP HANA リポジトリとアプリケーション リポジトリ、および高可用性リポジトリへのアクセス権を持ちます。 課金には、基本コンピューティング料金に加えて RHEL Premium、SAP Premium、および HA Premium が含まれます。
|RedHat | RHEL | RHEL-HA | RHEL マイナー バージョンと公開日の連結された値 (例: 7.6.2019062019) | これらは、高可用性アドオンへのアクセス権も持つ RHEL イメージです。 これらは、HA アドオン Premium により、RHEL および基本コンピューティング料金に加えて若干の追加料金が発生します。
|RedHat | RHEL | RHEL-SAP-APPS | RHEL マイナー バージョンと公開日の連結された値 (例: 7.3.2017053118) | SAP アプリケーション リポジトリと SAP HANA リポジトリが SAP リポジトリに結合されているため、これらのイメージは最新ではありません。 これらは、RHEL for SAP アプリケーション イメージです。 ベース RHEL リポジトリに加えて、SAP アプリケーション リポジトリへのアクセス権を持ちます。
|RedHat | RHEL | RHEL-SAP-HANA | RHEL マイナー バージョンと公開日の連結された値 (例: 7.3.2018051421) | SAP アプリケーション リポジトリと SAP HANA リポジトリが SAP リポジトリに結合されているため、これらのイメージは最新ではありません。 これらは RHEL for SAP HANA イメージです。 ベース RHEL リポジトリに加えて、SAP HANA リポジトリへのアクセス権を持ちます。

### <a name="rhel-8-image-types"></a>RHEL 8 イメージの種類
次に、RHEL 8 イメージの種類の詳細を示します。

|Publisher | プラン | SKU 値 | Version | 詳細
|----------|-------|------------|---------|--------
|RedHat | RHEL | 8 | RHEL マイナー バージョンと公開日の連結された値 (例: 8.0.20191023) | これらのイメージは、標準の Red Hat リポジトリに接続されている、LVM パーティション分割された RHEL 8.0 イメージです。
|RedHat | RHEL | 8-gen2 | RHEL マイナー バージョンと公開日の連結された値 (例: 8.0.20191024) | これらのイメージは、標準の Red Hat リポジトリに接続されている、LVM パーティション分割された Hyper-V 第 2 世代 RHEL 8.0 イメージです。 Azure の第 2 世代 VM の詳細については、[こちら](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2)を参照してください。

## <a name="extended-update-support-eus"></a>Extended Update Support (EUS)
2019 年 4 月現在、RHEL イメージは、既定では Extended Update Support (EUS) リポジトリに接続されて提供されています。 RHEL EUS の詳細については、[Red Hat のドキュメント](https://access.redhat.com/articles/rhel-eus)をご覧ください。

EUS リポジトリへの切り替えは可能であり、サポートされています。 VM を EUS に切り替える方法の手順と EUS サポート終了日の詳細については、[こちら](https://aka.ms/rhui-update#rhel-eus-and-version-locking-rhel-vms)をご覧ください。

>[!NOTE]
> EUS は、RHEL Extras ではサポートされていません。 つまり、通常 RHEL Extras チャネルから利用できるパッケージをインストールする場合、EUS を使用している間はそれを実行できないことになります。 Red Hat Extras の製品ライフサイクルの詳細については、[こちら](https://access.redhat.com/support/policy/updates/extras/)をご覧ください。

### <a name="differentiating-between-regular-and-eus-images"></a>標準イメージと EUS イメージの区別
EUS リポジトリに接続されているイメージを使用するお客様は、SKU に RHEL マイナー バージョン番号を含む RHEL イメージを使用する必要があります。

たとえば、次の 2 つの RHEL 7.4 イメージが利用可能と表示されるとします。
```bash
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7.6:7.6.2019102813
```
この場合、`RedHat:RHEL:7.6:7.6.2019102813` は既定では EUS リポジトリに接続され (SKU 値 7.4)、`RedHat:RHEL:7-LVM:7.6.2019062414` は既定では非 EUS リポジトリに接続されます (SKU 値 7-LVM)。

通常の (非 EUS) リポジトリを使用する場合は、SKU にマイナー バージョン番号が含まれていないイメージを使用してデプロイします。

#### <a name="rhel-images-with-eus"></a>EUS を含む RHEL イメージ
次の表は、EUS リポジトリに接続されている RHEL イメージに適用されます。

>[!NOTE]
> この記事の執筆時点で EUS をサポートしているのは、RHEL 7.4 以降のマイナー バージョンのみです。 EUS は、RHEL 7.3 以前ではサポートされなくなりました。
>
> RHEL EUS の可用性の詳細については、[こちら](https://access.redhat.com/support/policy/updates/errata)を参照してください。

マイナー バージョン |EUS イメージの例              |EUS の状態                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7.4      |RedHat:RHEL:7.4:7.4.2019041718 | 2019 年 4 月以降に発行されたイメージは、既定で EUS になります|
RHEL 7.5      |RedHat:RHEL:7.5:7.5.2019060305 | 2019 年 6 月以降に発行されたイメージは、既定で EUS になります |
RHEL 7.6      |RedHat:RHEL:7.6:7.6.2019052206 | 2019 年 5 月以降に発行されたイメージは、既定で EUS になります  |
RHEL 8.0      |該当なし                            | Red Hat から使用可能な EUS はありません                               |





### <a name="other-available-offers-and-skus"></a>その他の利用可能なプランと SKU
利用可能なプランと SKU の完全なリストには、上記の表にリストされているもの以外の追加のイメージ (`RedHat:rhel-ocp-marketplace:rhel74:7.4.1` など) が含まれる場合があります。 これらのプランは特定のマーケットプレース ソリューションのサポートを提供するために使用される場合があります。あるいは、プレビューとテストの目的で発行される可能性があります。 これらは、警告なく随時変更または削除される可能性があります。 Microsoft または Red Hat によって公式に文書化されていない限り、これらを使用しないでください。

## <a name="publishing-policy"></a>発行ポリシー
Microsoft および Red Hat では、新しいマイナー バージョンのリリース時、特定の CVE に対応する必要がある場合、あるいは不定期の構成変更/更新のために、イメージを更新します。 できるだけ早く (CVE の修正プログラムがリリースされてから、または利用可能になってから 3 営業日内に) 更新されたイメージを提供できるよう努めています。

特定のイメージ ファミリでのみ、現在のマイナー リリースを更新します。 新しいマイナー バージョンをリリースする場合、以前のマイナー バージョンの更新を停止します。 たとえば、RHEL 7.6 をリリースする場合、RHEL 7.5 イメージは更新されなくなります。

>[!NOTE]
> RHEL 従量課金制イメージからプロビジョニングされたアクティブな Azure VM は、Azure RHUI に接続されます。これらの VM では、Red Hat によってリリースされ、Azure RHUI にレプリケートされるとすぐに (通常は、Red Hat による公式リリース後 24 時間未満で) 更新プログラムと修正プログラムを受け取ることができます。 これらの VM には、更新プログラムを取得するために新たに発行されるイメージは必要ありません。更新を開始するタイミングはお客様が完全に制御できます。

## <a name="image-retention-policy"></a>イメージの保持期間ポリシー
現在のポリシーは、以前に発行されたすべてのイメージを保持するというものです。 Microsoft には、何らかの問題を発生させる原因になるイメージを削除する権利があります。 たとえば、今後のプラットフォームまたはコンポーネントの更新により構成が不適切となるイメージは削除される可能性があります。 削除される可能性のあるイメージでは、現在の Marketplace ポリシーに従い、イメージが削除される最大 30 日前に通知を提供します。

## <a name="next-steps"></a>次のステップ
* [Azure の RHEL イメージ](./redhat-imagelist.md)の完全な一覧をご覧ください。
* Azure Red Hat Update Infrastructure の詳細については、[こちら](https://aka.ms/rhui-update)を参照してください。
* [RHEL BYOS プラン](./redhat-byos.md)の詳細を参照してください。
* すべてのバージョンの RHEL に対する Red Hat のサポート ポリシーに関する情報は、「[Red Hat Enterprise Linux Life Cycle \(Red Hat Enterprise Linux のライフ サイクル\)](https://access.redhat.com/support/policy/updates/errata)」ページに記載されています。
