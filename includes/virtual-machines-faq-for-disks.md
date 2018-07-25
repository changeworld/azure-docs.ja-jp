---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/03/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 336e6e163178cd6d244460dbf9bee2a5bc9d714e
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "37935749"
---
# <a name="frequently-asked-questions-about-azure-iaas-vm-disks-and-managed-and-unmanaged-premium-disks"></a>Azure IaaS VM ディスクと Premium 管理ディスクおよび非管理ディスクについてよく寄せられる質問

この記事では、Azure Managed Disks と Azure Premium SSD ディスクについてよく寄せられるいくつかの質問に回答します。

## <a name="managed-disks"></a>Managed Disks

**Azure Managed Disks とは何ですか?**

Managed Disks は、ユーザーに代わってストレージ アカウントの管理を行うことで Azure IaaS VM のディスク管理を簡略化する機能です。 詳細については、[Managed Disks の概要](../articles/virtual-machines/windows/managed-disks-overview.md)に関する記事を参照してください。

**Standard 管理ディスクを 80 GB の既存の VHD から作成した場合、どのくらいの料金がかかりますか?**

80 GB の VHD から作成した Standard 管理ディスクは、1 つ上の Standard ディスク サイズである S10 ディスクとして扱われます。 S10 ディスクの価格に従って課金されます。 詳細については、 [価格に関するページ](https://azure.microsoft.com/pricing/details/storage)を参照してください。

**Standard 管理ディスクのトランザクション コストはありますか?**

はい。 トランザクションごとに課金されます。 詳細については、 [価格に関するページ](https://azure.microsoft.com/pricing/details/storage)を参照してください。

**Standard 管理ディスクでは、ディスク上の実際のデータ サイズについて課金されますか? または、ディスクのプロビジョニング容量について課金されますか?**

ディスクのプロビジョニング容量に基づいて課金されます。 詳細については、 [価格に関するページ](https://azure.microsoft.com/pricing/details/storage)を参照してください。

**Premium 管理ディスクと非管理ディスクの価格設定はどのように異なりますか?**

Premium 管理ディスクの価格は Premium 非管理ディスクと同じです。

**管理ディスクのストレージ アカウントのタイプ (Standard または Premium) を変更できますか?**

はい。 管理ディスクのストレージ アカウント タイプは、Azure Portal、PowerShell、または Azure CLI を使用して変更できます。

**Azure ストレージ アカウントの VHD ファイルを使用して、別のサブスクリプションの管理ディスクを作成できますか?**

はい。

**Azure ストレージ アカウントの VHD ファイルを使用して、別のリージョンに管理ディスクを作成できますか?**

いいえ。

**管理ディスクを使用するユーザーがスケールを制限されることはありますか?**

Managed Disks では、ストレージ アカウントに関連する制限が排除されています。 ただし、リージョンごと、およびサブスクリプションのディスクの種類ごとに 50,000 個という管理ディスクの上限が設けられています。

**管理ディスクの増分スナップショットを作成できますか?**

いいえ。 現在のスナップショット機能では、管理ディスクの完全なコピーが作成されます。

**可用性セット内の VM で管理ディスクと非管理ディスクを混在させることができますか?**

いいえ。 可用性セット内の VM は、すべて管理ディスクにするか、すべて非管理ディスクにする必要があります。 可用性セットを作成するときに、使用するディスクの種類を選択できます。

**Managed Disks は Azure Portal の既定オプションですか?**

はい。 

**空の管理ディスクを作成できますか?**

はい。 空のディスクを作成できます。 管理ディスクは VM とは独立して作成できます。たとえば、VM にアタッチせずに作成できます。

**Managed Disks を使用する可用性セットでサポートされる障害ドメイン数はいくつですか?**

Managed Disks を使用する可用性セットが配置されているリージョンに応じて、サポートされる障害ドメイン数は 2 または 3 になります。

**診断のための Standard ストレージ アカウントはどのように設定されますか?**

VM 診断用にプライベート ストレージ アカウントを設定します。

**Managed Disks では、どのようなロールベースのアクセス制御サポートを使用できますか?**

Managed Disks では 3 つの重要な既定のロールがサポートされます。

* 所有者: アクセス権を含めて、すべてを管理できます
* 共同作成者: アクセス権以外のすべてを管理できます
* 閲覧者: すべてを閲覧できますが、変更することはできません

**管理ディスクをプライベート ストレージ アカウントにコピーまたはエクスポートする方法はありますか?**

管理ディスクの読み取り専用 Shared Access Signature (SAS) URI を生成し、それを使用して、内容をプライベート ストレージ アカウントまたはオンプレミス ストレージにコピーできます。 SAS URI の使用には、Azure Portal、Azure PowerShell、Azure CLI、または [AzCopy](../articles/storage/common/storage-use-azcopy.md) を利用できます。

**管理ディスクのコピーを作成できますか?**

管理ディスクのスナップショットを作成し、そのスナップショットを使用して別の管理ディスクを作成できます。

**非管理ディスクはまだサポートされていますか?**

はい。非管理ディスクと管理ディスクの両方がサポートされています。 新しいワークロードでは管理ディスクを使用し、現在のワークロードは管理ディスクに移行することをお勧めします。


**128 GB のディスクを作成した後でサイズを 130 GB に増やした場合は、1 つ上のディスク サイズ (256 GB) として課金されますか?**

はい。

**ローカル冗長ストレージ、geo 冗長ストレージ、およびゾーン冗長ストレージ管理ディスクを作成できますか?**

Azure Managed Disks では、現在、ローカル冗長ストレージ管理ディスクだけがサポートされています。

**管理ディスクを縮小またはダウンサイズできますか?**

いいえ。 現在、この機能はサポートされていません。 

**ディスク上のリースを解除できますか?**

いいえ。 リースが存在するのはディスクを使用しているときに誤ってディスクが削除されるのを防ぐためなので、これは現在サポートされていません。

**VM のプロビジョニングに特殊な (システム準備ツールを使用して作成されたり汎用化されたりしていない) オペレーティング システム ディスクを使用する場合に、コンピューター名プロパティを変更できますか?**

いいえ。 コンピューター名プロパティを更新することはできません。 新しい VM のコンピューター名プロパティは、オペレーティング システム ディスクの作成に使用した親 VM から継承されます。 

**管理ディスクを使用して VM を作成するための Azure Resource Manager のサンプル テンプレートは、どこで見つけることができますか?**
* [Managed Disks を使用するテンプレートの一覧](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)
* https://github.com/chagarw/MDPP

**非管理ディスクと管理ディスクを同じ VM 上に共存させることはできますか?**

いいえ。

## <a name="standard-ssd-disks-preview"></a>Standard SSD ディスク (プレビュー)

**Azure Standard SSD ディスクとは何ですか?**
Standard SSD ディスクは、ソリッドステート メディアでサポートされた標準的なディスクであり、下位の IOPS レベルで一貫したパフォーマンスを必要とするワークロード向けにコスト効果の高いストレージとして最適化されています。 プレビューでは、これらを利用できるリージョンの数に加えて管理の容易性にも制限があります (Resource Manager テンプレートを介して利用可能)。

<a id="standard-ssds-azure-regions"></a>**Standard SSD ディスク (プレビュー) に対して現在サポートされている地域とは?**
* 北ヨーロッパ
* フランス中部
* 米国東部 2
* 米国中央部
* カナダ中部
* 東アジア
* 韓国南部
* オーストラリア東部

**Standard SSD ディスクを作成するにはどうすればよいですか?**
現時点では、Azure Resource Manager テンプレートを使用して Standard SSD ディスクを作成することができます。 Resource Manager テンプレートで Standard SSD ディスクを作成するには、次のパラメーターが必要です。

* Microsoft.Compute の *apiVersion* を `2018-04-01` (またはそれ以降) として設定する必要があります。
* `StandardSSD_LRS` として *managedDisk.storageAccountType* を指定します。

次の例は、Standard SSD ディスクを使用する VM の *properties.storageProfile.osDisk* セクションを示したものです。

```json
"osDisk": {
    "osType": "Windows",
    "name": "myOsDisk",
    "caching": "ReadWrite",
    "createOption": "FromImage",
    "managedDisk": {
        "storageAccountType": "StandardSSD_LRS"
    }
}
```

テンプレートを使用して Standard SSD ディスクを作成する方法における完全なテンプレート例については、[Standard SSD データ ディスクを含む Windows イメージから VM を作成](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/)に関するページを参照してください。

**既存のディスクを Standard SSD に変換できますか?**
はい、できます。 管理ディスクの変換に関する一般的なガイドラインについては、「[Azure 管理ディスクのストレージを Standard から Premium に (または Premium から Standard に) 変換する](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/convert-disk-storage)」を参照してください。 また、次の値を使用してディスクの種類を Standard SSD に更新します。
-AccountType StandardSSD_LRS

**Standard SSD を非管理ディスクとして使用できますか?**
いいえ。Standard SSD ディスクは Managed Disks としてのみ使用できます。

**Standard SSD ディスクは "単一インスタンス VM SLA" をサポートしますか?**
いいえ、Standard SSD には単一インスタンス VM SLA はありません。 単一インスタンス VM SLA が必要な場合、Premium SSD ディスクを使用します。

## <a name="migrate-to-managed-disks"></a>管理ディスクに移行する 

**Managed Disks への移行前後に、既存の Azure Backup サービス構成にどのような変更が必要でしょうか?**

変更の必要はありません。 

**移行前に Azure Backup サービスを使用して作成した VM バックアップは引き続き正常に機能しますか?**

はい。バックアップはシームレスに機能します。

**Managed Disks への移行前後に、既存の Azure Disk Encryption 構成にどのような変更が必要でしょうか?**

変更の必要はありません。 

**既存の仮想マシン スケール セットに関して、非管理ディスクから Managed Disks への移行は自動化されていますか?**

いいえ。 非管理ディスクを含んだ以前のスケール セットからのイメージを使用し、Managed Disks で新しいスケール セットを作成してください。 

**Managed Disks への移行前に作成したページ BLOB のスナップショットから Managed Disks を作成することはできますか?**

いいえ。 ページ BLOB のスナップショットをページ BLOB としてエクスポートしておき、エクスポートしたページ BLOB から Managed Disks を作成してください。 

**Azure Site Recovery で保護されているオンプレミスのマシンを Managed Disks の VM にフェールオーバーすることはできますか?**

はい。Managed Disks を使用した VM にフェールオーバーすることを選択できます。

**Azure Site Recovery で Azure から Azure へのレプリケーションによって保護されている Azure VM に、何か移行の影響はありますか?**

はい。 現在、Azure Site Recovery の Azure から Azure への Managed Disks を使用した VM の保護は、パブリック プレビュー サービスとしてのみ利用できます。

**暗号化されているか、以前に暗号化されていたストレージ アカウント上に配置されている非管理ディスクを持つ VM を管理ディスクに移行できますか?**

[はい]

## <a name="managed-disks-and-storage-service-encryption"></a>Managed Disks と Storage Service Encryption 

**Azure Storage Service Encryption は、管理ディスクを作成するときに、既定で有効になりますか?**

はい。

**だれが暗号化キーを管理するのですか?**

暗号化キーは Microsoft が管理します。

**自分の管理ディスクの Storage Service Encryption を無効にすることはできますか?**

いいえ。

**Storage Service Encryption は、特定のリージョンだけで使用可能ですか?**

いいえ。 Managed Disks を使用できるすべてのリージョンで使用できます。 Managed Disks は、すべてのパブリック リージョンおよびドイツで使用できます。 中国でも使用できますが、Microsoft 管理キーのみが対象であり、顧客管理キーは対象外です。

**管理ディスクが暗号化されているかどうかは、どうすれば判断できますか?**

Azure Portal、Azure CLI、および PowerShell で、管理ディスクがいつ作成されたかを調べることができます。 その日時が 2017 年 6 月 9 日より後であれば、ディスクは暗号化されています。

**2017 年 6 月 10 日より前に作成された既存のディスクは、どうすれば暗号化できますか?**

2017 年 6 月 10 日以降、既存の管理ディスクに書き込まれる新しいデータは自動的に暗号化されます。 既存のデータの暗号化も予定しており、暗号化はバック グラウンドで非同期的に行われます。 既存のデータを今すぐ暗号化する必要がある場合は、ディスクのコピーを作成してください。 新しいディスクは暗号化されます。

* [Azure CLI による管理ディスクのコピー](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)
* [PowerShell による管理ディスクのコピー](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)

**管理スナップショットおよびイメージは、暗号化されますか?**

はい。 2017 年 6 月 9 日より後に作成されたすべての管理スナップショットおよびイメージは、自動的に暗号化されます。 

**暗号化されているか、以前に暗号化されていたストレージ アカウント上に配置されている非管理ディスクを持つ VM を管理ディスクに変換できますか?**

[はい]

**管理ディスクまたはスナップショットからエクスポートされた VHD も暗号化されますか?**

いいえ。 ただし、暗号化された管理ディスクまたはスナップショットから暗号化されたストレージ アカウントに VHD をエクスポートした場合は、暗号化されます。 

## <a name="premium-disks-managed-and-unmanaged"></a>Premium ディスク: 管理と非管理

**VM が使用するサイズ シリーズが Premium SSD ディスク (DSv2 など) をサポートしている場合、Premium データ ディスクと Standard データ ディスクの両方をアタッチできますか?** 

はい。

**Premium と Standard 両方のデータ ディスクを、Premium SSD ディスクをサポートしないサイズ シリーズ (D、Dv2、G、F シリーズなど) にアタッチできますか?**

いいえ。 Premium SSD ディスクをサポートするサイズ シリーズを使用しない VM にアタッチできるのは、Standard データ ディスクのみです。

**Premium データ ディスクを 80 GB の既存の VHD から作成した場合、どのくらいの料金がかかりますか?**

80 GB の VHD から作成した Premium データ ディスクは、1 つ上の Premium ディスク サイズである P10 ディスクとして扱われます。 P10 ディスクの価格に従って課金されます。

**Premium SSD ディスクを使用するためのトランザクションのコストは発生しますか?**

ディスク サイズごとに固定料金が適用されます。IOPS とスループットには一定の制限が設けられています。 それ以外にかかるコストは、送信帯域幅とスナップショットの容量です (該当する場合)。 詳細については、 [価格に関するページ](https://azure.microsoft.com/pricing/details/storage)を参照してください。

**ディスク キャッシュの IOPS とスループットの制限はどのようになっていますか?**

DS シリーズの場合、制限はキャッシュとローカル SSD の合計でコアあたり 4000 IOPS と、コアあたり 33 MB/秒です。 GS シリーズでは、コアあたり 5,000 IOPS と、コアあたり 50 MB/秒です。

**ローカル SSD は Managed Disks VM でサポートされていますか?**

ローカル SSD は、Managed Disks VM に含まれている一時的なストレージです。 この一時ストレージに追加の料金は発生しません。 このローカル SSD は Azure BLOB ストレージに永続化されないため、アプリケーション データの保存には使用しないことをお勧めします。

**TRIM を使用することによる Premium ディスクへの影響はありますか?**

Premium ディスクと Standard ディスクのどちらの場合も、Azure ディスクで TRIM を使用することに不都合な点はありません。

## <a name="new-disk-sizes-managed-and-unmanaged"></a>新しいディスク サイズ: 管理と非管理

**オペレーティング システムとデータ ディスクでサポートされている最大ディスク サイズはどれくらいですか?**

オペレーティング システム ディスクについて Azure でサポートされているパーティションの種類は、マスター ブート レコード (MBR) です。 MBR 形式では、最大で 2 TB のディスク サイズがサポートされています。 オペレーティング システム ディスクについて Azure でサポートされている最大サイズは、2 TB です。 データ ディスクについては、Azure では最大で 4 TB までサポートされています。 

**サポートされている最大ページ BLOB サイズはどれくらいですか?**

Azure でサポートされている最大ページ BLOB サイズは、8 TB (8,191 GB) です。 データ ディスクまたはオペレーティング システム ディスクとして VM にアタッチされている場合の最大ページ BLOB サイズは、4 TB (4,095 GB) です。

**1 TB を超えるディスクの作成、アタッチ、サイズ変更、およびアップロードを行うために、新しいバージョンの Azure ツールを使用する必要はありますか?**

1 TB を超えるディスクの作成、アタッチ、またはサイズ変更を行うために、既存の Azure ツールをアップグレードする必要はありません。 VHD ファイルをオンプレミスから直接 Azure にページ BLOB または非管理ディスクとしてアップロードするには、以下の最新のツール セットを使用する必要があります。

|Azure ツール      | サポートされているバージョン                                |
|-----------------|---------------------------------------------------|
|Azure PowerShell | バージョン番号 4.1.0: 2017 年 6 月以降のリリース|
|Azure CLI v1     | バージョン番号 0.10.13: 2017 年 5 月以降のリリース|
|AzCopy           | バージョン番号 6.1.0: 2017 年 6 月以降のリリース|

Azure CLI v2 および Azure ストレージ エクスプローラーは、近日中にサポートされる予定です。 

**非管理ディスクまたはページ BLOB では、P4 および P6 ディスク サイズがサポートされていますか?**

いいえ。 P4 (32 GB) および P6 (64 GB) ディスク サイズは、管理ディスクでのみサポートされています。 非管理ディスクおよびページ BLOB については、近日中にサポートされる予定です。

**64 GB 未満の既存の Premium 管理ディスクが、小規模ディスクが使用できるようになる (2017 年 6 月 15 日ごろ) より前に作成された場合は、どのように課金されますか?**

64 GB 未満の既存の小規模 Premium ディスクは、引き続き P10 価格レベルに基づいて課金されます。 

**64 GB 未満の小規模 Premium ディスクのディスク レベルを P10 から P4 または P6 に切り替えるには、どうすればよいですか?**

小規模ディスクのスナップショットを作成してからディスクを作成すると、プロビジョニングされたサイズに基づいて価格レベルを P4 または P6 に自動的に切り替えることができます。 


## <a name="what-if-my-question-isnt-answered-here"></a>ここに質問の答えがない場合はどうすればいいですか。

質問がここに表示されていない場合はご連絡ください。答えを見つけるお手伝いをします。 この記事の末尾のコメントに、質問を投稿することができます。 この記事に関して、Azure Storage チームやその他のコミュニティのメンバーとやり取りするには、MSDN の [Azure Storage フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)をご利用ください。

機能についてのご要望がある場合は、ご要望やアイデアを [Azure Storage フィードバック フォーラム](https://feedback.azure.com/forums/217298-storage)までお送りください。
