---
title: Azure Stack 統合システムのファイアウォール計画 | Microsoft Docs
description: Azure に接続されたマルチノード Azure Stack デプロイのための Azure Stack のファイアウォールに関する注意点について説明します。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 3759a9845d4ad1514fc5f0183c78b5eca2e31464
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52960653"
---
# <a name="azure-stack-firewall-integration"></a>Azure Stack ファイアウォールの統合
Azure Stack は、ファイアウォール デバイスを使って保護することをお勧めします。 ファイアウォールは、分散型サービス拒否 (DDOS) 攻撃や侵入検出、コンテンツ検査などに効果がありますが、BLOB、テーブル、キューなどの Azure Storage サービスのスループットのボトルネックにもなります。

ID モデルの Azure Active Directory (Azure AD) または Windows Server Active Directory Federation Services (AD FS) に基づいて、AD FS のエンドポイントを発行しなければならない場合があります。 切断されたデプロイ モードを使用する場合は、AD FS のエンドポイントを発行する必要があります。 詳細については、[データ センターの統合の ID に関する記事](azure-stack-integrate-identity.md)をご覧ください。

Azure Resource Manager (管理者)、管理者ポータル、Key Vault (管理者) のエンドポイントは、外部発行が必須というわけではありません。 たとえば、サービス プロバイダーは、攻撃対象領域を制限して、インターネットからではなくネットワークの内部から Azure Stack の管理のみをすることができます。

企業組織では、外部のネットワークは既存の企業ネットワークを指定できます。 このようなシナリオでは、企業ネットワークから Azure Stack を操作するには、そのエンドポイントを公開する必要があります。

### <a name="network-address-translation"></a>ネットワーク アドレス変換
外部のリソースやインターネットに対し、デプロイ時に DVM (Deployment Virtual Machine) からアクセスしたり、登録やトラブルシューティング時に ERCS (Emergency Recovery Console) VM や PEP (Privileged End Point) からアクセスしたりできるようにするための方法としては、ネットワーク アドレス変換 (NAT) が推奨されます。

また、外部ネットワークやパブリック VIP におけるパブリック IP アドレスに代わる働きとして NAT を利用することもできます。 ただしこれは、テナントのユーザー エクスペリエンスが制限され、複雑さも増すため推奨されません。 2 つのオプションの 1 つは 1 対 1 の NAT で、これはプールでのユーザー IP ごとの 1 つのパブリック IP を必要とします。もう 1 つは多対 1 のNAT で、これはユーザーが使用する可能性があるすべてのポートへの関連付けを含むユーザー VIP ごとに 1 つの NAT 規則を必要とします。

パブリック VIP に NAT を使うことには、いくつか不利な点もあります。
- ソフトウェア定義ネットワーク (SDN) スタックでは、ユーザーが独自のエンドポイントと独自の公開規則を管理するため、ファイアウォール ルールを管理する場合に NAT はオーバーヘッドを増加させます。 ユーザーは Azure Stack オペレーターと連絡をとって、自分の VIP を公開させ、ポート リストを更新する必要があります。
- NAT の使用によりユーザー エクスペリエンスは制限されますが、オペレーターは公開要求を完全に管理できます。
- Azure のハイブリッド クラウド シナリオでは、NAT を使うエンドポイントへの VPN トンネルの設定を Azure がサポートしていないことを考慮します。

### <a name="ssl-decryption"></a>SSL 解読
現在、すべての Azure Stack トラフィックに対して SSL 解読を無効にすることをお勧めします。 将来の更新で SSL 解読がサポートされた場合は、Azure Stack に対して SSL 解読を有効にする方法に関するガイダンスが提供される予定です。

## <a name="edge-firewall-scenario"></a>エッジ ファイアウォール シナリオ
エッジ デプロイでは、エッジ ルーターまたはファイアウォールのすぐ内側に Azure Stack がデプロイされます。 これらのシナリオでは、ファイアウォールを境界より上に配置すること (シナリオ 1: アクティブ/アクティブとアクティブ/パッシブの両方のファイアウォール構成がサポートされます)、あるいはフェールオーバーに BGP または静的ルーティングを使用する ECMP (Equal Cost Multi Path) に依存して境界デバイスとして機能すること (シナリオ2: アクティブ/アクティブのファイアウォール構成だけがサポートされます) が、サポートされています。

通常、パブリックにルーティング可能な IP アドレスは、デプロイ時に、外部ネットワークからパブリック VIP プールに対して指定されます。 エッジのシナリオでは、セキュリティ上、他のどのネットワークに対しても、パブリックにルーティング可能な IP を使用することは推奨されません。 このシナリオでは、Azure などのパブリック クラウドでのエクスペリエンスのような、完全に自己管理型のクラウドを体験できます。  

![Azure Stack のエッジ ファイアウォールの例](./media/azure-stack-firewall/firewallScenarios.png)

## <a name="enterprise-intranet-or-perimeter-network-firewall-scenario"></a>企業イントラネットまたは境界ネットワークのファイアウォール シナリオ
企業イントラネットまたは境界デプロイでは、Azure Stack のデプロイ先が、マルチゾーン ファイアウォールか、またはエッジ ファイアウォールと内部の企業ネットワーク ファイアウォールとの間になります。 そのトラフィックは、セキュア ゾーン、境界ネットワーク ゾーン (DMZ)、セキュリティ保護なしゾーンに分散されます。

- **セキュア ゾーン**: これは、ルーティング可能な内部 (または企業) IP アドレスを使用した内部ネットワークです。 セキュア ネットワークは、分割できるほか、ファイアウォール上で NAT を介したインターネット アウトバウンド アクセスを設定することができます。また、通常は、内部ネットワークを介して、データセンター内のどこからでもアクセスすることができます。 Azure Stack のネットワークは、外部ネットワークのパブリック VIP プールを除き、すべてセキュア ゾーンに存在します。
- **境界ゾーン**. 通常、境界ネットワークには、Web サーバーなど、外部の (インターネットに公開される) アプリケーションがデプロイされます。 通常、DDoS や侵入 (ハッキング) などの攻撃を防ぐためにファイアウォールによって監視され、インターネットからは指定した受信トラフィックだけが許可されます。 Azure Stack の中で、DMZ ゾーンに存在してよいのは、外部ネットワークのパブリック VIP プールだけです。
- **セキュリティ保護なしゾーン** これは外部ネットワーク、つまりインターネットです。 セキュリティ保護なしゾーンに Azure Stack をデプロイすることは**推奨されません**。

![Azure Stack の境界ネットワークの例](./media/azure-stack-firewall/perimeter-network-scenario.png)

## <a name="learn-more"></a>詳細情報
[Azure Stack のエンドポイントで使用されるポートとプロトコル](azure-stack-integrate-endpoints.md)について理解を深めましょう。

## <a name="next-steps"></a>次の手順
[Azure Stack PKI の要件](azure-stack-pki-certs.md)

