---
title: Azure Security Center でサポートされている機能とプラットフォーム | Microsoft Docs
description: このドキュメントでは、Azure Security Center でサポートされる機能とプラットフォームの一覧を示します。
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 4108355415d1230f98db36a4f83497de2fa848f7
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53185581"
---
# <a name="platforms-and-features-supported-by-azure-security-center"></a>Azure Security Center でサポートされている機能とプラットフォーム

セキュリティ状態の監視と推奨事項は、クラシック デプロイ モデルと Resource Manager デプロイ モデルのどちらかを使用して作成された仮想マシン (VM)、およびコンピューターで利用できます。

> [!NOTE]
> Azure リソースの詳細については、[クラシック デプロイ モデルと Resource Manager デプロイ モデル](../azure-classic-rm.md)に関するページを参照してください。
>
>

## <a name="supported-platforms"></a>サポートされるプラットフォーム 

このセクションでは、Azure Security Center エージェントで実行できるプラットフォーム、およびデータを収集する元となるプラットフォームが一覧されます。

### <a name="supported-platforms-for-windows-computers-and-vms"></a>Windows コンピューターおよび VM でサポートされているプラットフォーム
次の Windows オペレーティング システムがサポートされています。

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016


### <a name="supported-platforms-for-linux-computers-and-vms"></a>Linux コンピューターおよび VM でサポートされているプラットフォーム
次の Linux オペレーティング システムがサポートされています。

* Ubuntu バージョン 12.04 LTS、14.04 LTS、および 16.04 LTS。
* Debian バージョン 6、7、8、および 9。
* CentOS バージョン 5、6、および 7。
* Red Hat Enterprise Linux (RHEL) バージョン 5、6、および 7。
* SUSE Linux Enterprise Server (SLES) バージョン 11 および 12。
* Oracle Linux バージョン 5、6、および 7。
* Amazon Linux 2012.09 から 2017。
* Openssl 1.1.0 は、x86_64 プラットフォーム (64 ビット) でのみサポートされます。

> [!NOTE]
> 仮想マシンの動作分析は、Linux オペレーティング システムではまだ利用できません。
>
>

## <a name="vms-and-cloud-services"></a>VM とクラウド サービス
クラウド サービスで実行する VM もサポートされます。 監視されるのは、運用スロットで実行するクラウド サービスの Web ロールと worker ロールだけです。 Cloud Services の詳細については、「[Azure Cloud Services の概要](../cloud-services/cloud-services-choose-me.md)」をご覧ください。


## <a name="supported-iaas-features"></a>サポートされている IaaS 機能

> [!div class="mx-tableFixed"]
> 

|サーバー| Windows|| Linux||
|----|----|----|----|----|
|環境|Azure|Azure 以外|Azure|Azure 以外|
|VMBA 脅威検出アラート|✔|✔|✔ (サポートされているバージョン)|✔|
|ネットワーク経由の脅威検出アラート|✔|X|✔|X|
|Windows Defender ATP の統合*|✔ (サポートされているバージョン)|✔|X|X|
|不足しているパッチ|✔|✔|✔|✔|
|セキュリティ構成|✔|✔|✔|✔|
|マルウェア対策プログラム|✔|✔|X|X|
|JIT VM アクセス|✔|X|✔|X|
|アダプティブ アプリケーション制御|✔|X|X|X|
|FIM|✔|✔|✔|✔|
|ディスクの暗号化|✔|X|✔|X|
|サード パーティ デプロイ|✔|X|✔|X|
|NSG|✔|X|✔|X|
|ファイルレス脅威の検出|✔|✔|X|X|
|ネットワーク マップ|✔|X|✔|X|
|アダプティブ ネットワーク制御|✔|X|✔|X|

\* 現在、これらの機能はパブリック プレビューでサポートされています。


## <a name="supported-paas-features"></a>サポートされている PaaS 機能 


|Service|Recommendations|脅威の検出|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL*|✔| ✔|
|MySQL*|✔| ✔|
|Azure BLOB ストレージ アカウント*|✔| ✔|
|App Services|✔| ✔|
|Cloud Services|✔| X|
|VNet|✔| NA|
|サブネット|✔| NA|
|NIC|✔| ✔|
|NSG|✔| NA|
|サブスクリプション|✔| ✔|

\* 現在、これらの機能はパブリック プレビューでサポートされています。 

## <a name="next-steps"></a>次の手順

- [Azure Security Center を導入するための設計上の考慮事項を計画し、理解する](security-center-planning-and-operations-guide.md)方法について説明しています。
- [Security Center における仮想マシンの動作分析とクラッシュ ダンプ メモリ分析](security-center-alerts-type.md#virtual-machine-behavioral-analysis)について説明します。
- [Azure Security Center の使用に関してよく寄せられる質問](security-center-faq.md)が記載されています。
- [Azure のセキュリティとコンプライアンスについてのブログ記事](https://blogs.msdn.com/b/azuresecurity/)を確認できます。
