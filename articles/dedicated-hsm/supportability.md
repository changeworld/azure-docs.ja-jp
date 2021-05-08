---
title: サポート - Azure の専用 HSM | Microsoft Docs
description: さまざまなシナリオにおける Azure の専用 HSM のサポート オプションと責任の範囲
services: dedicated-hsm
author: johndaw
manager: rkarlin
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.custom: seodec18
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: cd949bdb7c489478df6a16d6dccd0bf358637604
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105606931"
---
# <a name="azure-dedicated-hsm-supportability"></a>Azure の専用 HSM のサポート

Azure Dedicated HSM サービスでは、完全な管理制御と管理責任が備えられた、お客様専用の物理デバイスが提供されます。 使用できるようになったデバイスは、[Thales Luna 7 HSM モデル A790](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms) です。 お客様がプロビジョニングを行った後は、監視の役割で物理シリアル ポートに接続することを除き、Microsoft による管理アクセスはできなくなります。  アクセス権がないため、Microsoft には継続的なソフトウェア レベルのメンテナンスまたはシステム管理の責任はありません。 そのため、お客様が一般的な運用アクティビティを担当する必要があります。
お客様は、HSM を使用しているアプリケーションについての全責任を負い、サポートまたはコンサルティングベースの支援を受けるために Thales と連携する必要があります。 運用上の健全性についてお客様が所有する範囲に基づき、Microsoft はこのサービスについていかなる高可用性も保証することはできません。 高可用性を実現するためにアプリケーションが正しく構成されていることを確認するのは、お客様の責任です。 Microsoft は、デバイスの正常性とネットワーク接続の監視および管理を行います。

## <a name="getting-support"></a>サポートの利用

専用 HSM のカスタマー サポートは、Microsoft と Thales の間の共同の取り組みです。 ハードウェアの問題やネットワーク パスの問題はすべて Microsoft が対処し、実際の HSM (構成、ソフトウェア、ファームウェア、およびアプリケーション開発など) に関連する問題は Thales が対処します。 このサポート モデルにより、最も効率的なサポートを受けるための最短ルートが確保されます。 ある特定の問題について不確かな場合は、Microsoft にサポート要求を上げていただければ、適切な部署が対処するようにします。 Microsoft はすべてのサポート ナリオに関与し続けて、お客様が最良のサポート体験をできるように努力します。

## <a name="thales-support"></a>Thales のサポート

専用 HSM サービスを使用しているお客様は、ご利用の Plus サポート プランにより、Thales からのサポートを受けることができます。 これに必要なのは、Thales サポート ポータルを使用した登録プロセスだけです。 このための顧客 ID と手順は、専用 HSM サービスにアクセスするための Microsoft との初期契約の一部として提供されます。 Thales のサポートは、[カスタマー サポート ポータル](https://supportportal.thalesgroup.com/csm)を通して受けることができます。
注目すべき重要な点は、HSM を使用するために必要なすべてのソフトウェアと資料 (たとえば、クライアント アクセス ソフトウェアや SDK) は、カスタマー サポート ポータルからのダウンロードによって Thales が提供するという点です。

### <a name="software-components"></a>ソフトウェア コンポーネント

HSM デバイスの構成には、さまざまなソフトウェア コンポーネントが使用されています。

* クライアント ソフトウェア
* SDK
* ツール

### <a name="guidance"></a>ガイダンス

Thales は、[Thales カスタマー サポート ポータル](https://supportportal.thalesgroup.com/csm)で管理および構成についてのガイダンスを公開しています。 有効な顧客 ID を使用してサインインすると、これらのドキュメントをダウンロードできます。 Thales は、さまざまなシナリオやソフトウェアの統合を支援する一連の統合ガイドも提供しています。 詳細については、[Thales の Microsoft パートナー サイト](https://cpl.thalesgroup.com/partners/overview)を参照してください。

### <a name="support"></a>サポート

専用 HSM サービスの一部で、ソフトウェア レベルの問題や HSM の使用に関する質問が発生した場合、Thales のサポートが直接対応します。 上記の一覧のすべてのソフトウェア コンポーネントおよびプロビジョニング後のカスタム HSM 構成は、Thales が対応します。 詳細については、[Thales カスタマー サポート ポータル](https://supportportal.thalesgroup.com/csm)を参照してください。

### <a name="consulting-services"></a>コンサルティング サービス

HSM を使用するカスタム アプリケーションの設計、開発、およびデプロイのサポートについては、Thales のアカウント担当者にお問い合わせください。

## <a name="microsoft-support"></a>Microsoft のサポート

Microsoft では、物理 HSM デバイスがネットワークを通じてアクセス可能で、1 人のお客様が占有して使用できる動作状態になるようにします。 お客様は、デバイスの構成、管理、マネージメントの責任を負います。 Microsoft の責任は次のとおりです。

* デバイスの電源および冷却
* HSM の動作状態の維持 (障害シナリオなど)
* ネットワーク経由でのデバイスへのアクセス

次のような問題は、Microsoft に報告されます。

* コンポーネント障害
* 完全なデバイス障害
* ネットワーク アクセスの問題
* プロビジョニングおよびプロビジョニング解除の問題

Microsoft は、監視ロール (つまり、管理ロールではない) でデバイスの物理シリアル ポートにアクセスすることで、基本的な正常性テレメトリを実施します。  これにより、Microsoft は、お客様に問題の事前通知を行えるようになります。ただし、お客様がこのアクセス許可を制限した場合は除きます。 

### <a name="provisioning-and-decommissioning"></a>プロビジョニングとプロビジョニング解除

お客様が専用 HSM サービスの登録を承認すると、HSM リソースを作成できるようになります (現在のところ、PowerShell またはコマンド ライン インターフェイスを使用します。Azure portal は使用できません)。 リソースの割り当てプロセスが実行され、指定されたリージョンの物理デバイスがお客様によって事前に定義された仮想ネットワーク (VNet) にマップされます。 デバイスが VNet に表示されると、デバイスにアクセスして要件に従って構成できるようになります。 お客様は、Thales クライアント ソフトウェアおよびツールを使用して、専用 HSM にアクセスします。 リソース作成プロセスは、Microsoft がサポートを行います。 カスタム構成プロセス以降は、Thales がサポートを行います。 (上記の Thales サポートを参照して下さい)。 HSM の使用が終わった際には、データが永続化されないように、リセット (またはゼロ埋め) を行う必要があります。 デバイスをリセットするプロセスによって、すべてのカスタム構成とデータが削除されます。 Microsoft は、デバイスの割り当てを解除し、きれいな状態でプールに返します。 つまり、デバイスがプールに返される際には、以前のお客様のアクティビティの証拠は残されません。 

### <a name="hardware-issues"></a>ハードウェアの問題

HSM デバイスの電源とファン ユニットは、冗長かつ交換可能になっています。  ただし、ファン ユニットを取り外しても、改ざんイベントは発生します。 コンポーネントの障害が発生した場合、Microsoft は中断時間が最も短く、お客様のサービス可用性に対するリスクが最も低くなるように、最適なプロセスを使用してコンポーネント レベルの問題に対処します。
デバイスにより深刻な障害が発生した場合、そのデバイスを空きプールの新しいデバイスと交換します。 お客様が新しいデバイスを既存の HA ペアに含めると、デバイスが同期されて完全な運用状態に戻ります。 障害が発生したデバイスは、データ関連のデバイスを切り離させ、データ センターのサイト上に細分化させます。 

### <a name="networking-issues"></a>ネットワークの問題

HSM デバイスに対するネットワーク アクセスの問題が発生した場合は、Microsoft のサポートに問い合わせる必要があります。 ネットワーク アクセスを確認する簡単なテストでは、SSH を使用して HSM デバイスに接続します。 これに失敗した場合は、Microsoft サポートにお問い合わせください。

## <a name="service-level-expectations-for-support"></a>サポートで期待されるサービス レベル

Microsoft のサポートのサービス レベルについては、[Azure のサポート プラン](https://azure.microsoft.com/support/plans/)を参照してください
Thales のサポートのサービス レベルについては、[Thales サポートの要点に関するページ](https://azure.microsoft.com/support/plans/)を参照してください。

## <a name="next-steps"></a>次のステップ

デバイスのプロビジョニングやアプリケーションの設計、アプリケーションのデプロイ前に、高可用性やセキュリティの主要概念を十分に理解しておくことをお勧めします。

* [デプロイ アーキテクチャ](deployment-architecture.md)
* [高可用性](high-availability.md)
* [物理的なセキュリティ](physical-security.md)
* [ネットワーク](networking.md)

