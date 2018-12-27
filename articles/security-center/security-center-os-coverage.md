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
ms.openlocfilehash: f4bc90b2d1a80125ae88b4b5c4c11e42a34a985a
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2018
ms.locfileid: "51240428"
---
# <a name="platforms-and-features-supported-by-azure-security-center"></a>Azure Security Center でサポートされている機能とプラットフォーム

セキュリティ状態の監視と推奨事項は、クラシック デプロイ モデルと Resource Manager デプロイ モデルのどちらかで作成された仮想マシン (VM)、およびコンピューターで利用できます。

> [!NOTE]
> Azure リソースの詳細については、[クラシック デプロイ モデルと Resource Manager デプロイ モデル](../azure-classic-rm.md)に関するページを参照してください。
>
>

## <a name="supported-platforms"></a>サポートされるプラットフォーム 

このセクションでは、Azure Security Center エージェントで実行できるプラットフォーム、およびデータを収集する元となるプラットフォームが一覧されます。

### <a name="supported-platforms-for-windows-computers-and-vms"></a>Windows コンピューターおよび VM でサポートされているプラットフォーム
サポートされている Windows オペレーティング システム:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016


### <a name="supported-platforms-for-linux-computers-and-vms"></a>Linux コンピューターおよび VM でサポートされているプラットフォーム
サポートされている Linux オペレーティング システム:

* Ubuntu バージョン 12.04 LTS、14.04 LTS、16.04 LTS
* Debian バージョン 6、7、8、9
* CentOS バージョン 5、6、7
* Red Hat Enterprise Linux (RHEL) バージョン 5、6、7
* SUSE Linux Enterprise Server (SLES) バージョン 11、12
* Oracle Linux バージョン 5、6、7
* Amazon Linux 2012.09 から 2017
* Openssl 1.1.0 は、x86_64 プラットフォーム (64 ビット) でのみサポート

> [!NOTE]
> 仮想マシンの動作分析は、Linux オペレーティング システムではまだ利用できません。
>
>

## <a name="vms-and-cloud-services"></a>VM とクラウド サービス
クラウド サービスで実行されている VM もサポートされます。 監視されるのは、運用スロットで実行されているクラウド サービスの Web ロールと worker ロールだけです。 クラウド サービスの詳細については、 [Cloud Services の概要](../cloud-services/cloud-services-choose-me.md)に関するページをご覧ください。


## <a name="supported-iaas-features"></a>サポートされている IaaS 機能

> [!div class="mx-tableFixed"]
> 

|サーバー|Windows||Linux||
|----|----|----|----|----|
|環境|Azure|Azure 以外|Azure|Azure 以外|
|VMBA 脅威検出アラート|✔|✔|✔ (サポートされているバージョン)|✔|
|ネットワーク経由の脅威検出アラート|✔|X|✔|X|
|Windows Defender ATP の統合*|✔ (サポートされているバージョン)|✔|X|X|
|不足しているパッチ|✔|✔|✔|✔|
|セキュリティ構成|✔|✔|✔|✔|
|マルウェア対策|✔|✔|X|X|
|JIT VM アクセス|✔|X|✔|X|
|アダプティブ アプリケーション制御|✔|X|X|X|
|FIM|✔|✔|✔|✔|
|ディスクの暗号化|✔|X|✔|X|
|サード パーティのデプロイ|✔|X|✔|X|
|NSG|✔|X|✔|X|
|ファイル脅威検出|✔|✔|X|X|
|ネットワーク マップ|✔|X|✔|X|
|アダプティブ ネットワーク制御|✔|X|✔|X|

\* 現在、これらの機能はパブリック プレビューでサポートされています。


## <a name="supported-paas-features"></a>サポートされている PaaS 機能


|Service|Recommendations|脅威の検出|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL*|✔| ✔|
|MySQL*|✔| ✔|
|BLOB ストレージ アカウント*|✔| ✔|
|App Services|✔| ✔|
|クラウド サービス|✔| X|
|VNet|✔| 該当なし|
|サブネット|✔| 該当なし|
|NIC|✔| ✔|
|NSG|✔| 該当なし|
|サブスクリプション|✔| ✔|

\* 現在、これらの機能はパブリック プレビューでサポートされています。

## <a name="next-steps"></a>次の手順

- 「[Azure Security Center 計画および運用ガイド](security-center-planning-and-operations-guide.md)」 - Azure Security Center を導入するための設計上の考慮事項を計画および理解する方法について説明します。
- [Azure Security Center のタイプ別のセキュリティ アラート](security-center-alerts-type.md#virtual-machine-behavioral-analysis) - Security Center における仮想マシンの動作分析とクラッシュ ダンプ メモリ分析について説明します。
- [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md) 」 -- このサービスの使用に関してよく寄せられる質問が記載されています。
- [Azure セキュリティ ブログ](https://blogs.msdn.com/b/azuresecurity/) - Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。
