---
title: Azure Stack Development Kit (ASDK) のデプロイの前提条件 | Microsoft Docs
description: Azure Stack Development Kit (ASDK) の環境とハードウェアの要件を確認します。
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
ms.date: 12/12/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 4bcb71829b2fb5f35398f36dee36d4e98c5a609b
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2019
ms.locfileid: "54106735"
---
# <a name="azure-stack-deployment-planning-considerations"></a>Azure Stack のデプロイ計画に関する考慮事項
Azure Stack Development Kit (ASDK) をデプロイする前に、開発キットのホスト コンピューターがこの記事で説明されている要件を満たしていることをご確認ください。


## <a name="hardware"></a>ハードウェア
| コンポーネント | 最小値 | 推奨 |
| --- | --- | --- |
| ディスク ドライブ: オペレーティング システム |システム パーティション用に最低 200 GB の空きがある、1 つのオペレーティング システム ディスク (SSD または HDD) |システム パーティション用に最低 200 GB の空きがある1 OS ディスク (SSD または HDD) |
| ディスク ドライブ: 開発キット データ全般<sup>*</sup>  |4 つのディスク。 各ディスクに最低 240 GB の空き容量が必要 (SSD または HDD)。 すべての使用可能なディスクが使われます。 |4 つのディスク。 各ディスクに最低 400 GB の空き容量が必要 (SSD または HDD)。 すべての使用可能なディスクが使われます。 |
| コンピューティング: CPU |デュアル ソケット: 16 個の物理コア (合計) |デュアル ソケット: 20 個の物理コア (合計) |
| コンピューティング: メモリ |192 GB RAM |256 GB RAM |
| コンピューティング: BIOS |Hyper-V 有効 (SLAT サポートあり) |Hyper-V 有効 (SLAT サポートあり) |
| ネットワーク: NIC |Windows Server 2012 R2 認定。 特別な機能は必要ありません |Windows Server 2012 R2 認定。 特別な機能は必要ありません |
| ハードウェア ロゴ認定 |[Windows Server 2012 R2 認定](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |[Windows Server 2016 認定](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |

<sup>*</sup> Azure から[マーケットプレース項目](asdk-marketplace-item.md)の多くを追加する計画の場合は、この推奨容量より多くが必要です。

**データ ディスク ドライブの構成:** すべてのデータ ドライブは同じ種類 (すべて SAS、すべて SATA、またはすべて NVMe)、同じ容量である必要があります。 SAS ディスク ドライブを使う場合、ディスク ドライブは 1 つのパス経由で接続する必要があります (MPIO 、マルチパスはサポートされません)。

**HBA 構成オプション**

* (推奨) 単純な HBA
* RAID HBA – "パススルー" モードでアダプターを構成する必要があります。
* RAID HBA – ディスクは単一のディスク、RAID-0 として構成する必要があります。

**サポート対象のバスとメディアの組み合わせ**

* SATA HDD
* SAS HDD
* RAID HDD
* RAID SSD (メディアの種類が指定されていない場合、または不明な場合<sup>*</sup>)
* SATA SSD + SATA HDD
* SAS SSD + SAS HDD
* NVMe

<sup>*</sup> パススルー機能のない RAID コントローラーは、メディアの種類を認識できません。 このようなコントローラーでは、SSD と HDD の両方が "Unspecified" (指定なし) としてマークされます。 その場合、SSD がキャッシュ デバイスではなく、永続的なストレージとして使われます。 したがって、それらの SSD には開発キットをデプロイできます。

**HBA の例**: パススルー モードの LSI 9207-8i、LSI-9300-8i、または LSI-9265-8i

サンプル OEM 構成を使用できます。

## <a name="operating-system"></a>オペレーティング システム
|  | **要件** |
| --- | --- |
| **OS バージョン** |Windows Server 2016 以降。 Azure Stack のインストールに含まれる VHD でホスト コンピューターを起動するので、デプロイが開始する前のオペレーティング システムのバージョンは重要ではありません。 オペレーティング システムと必要なすべての修正プログラムは、イメージに既に統合されています。 開発キットで使われている Windows Server インスタンスをアクティブ化するためにキーを使わないでください。 |

> [!TIP]
> オペレーティング システムをインストールした後は、[Deployment Checker for Azure Stack](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) を使って、ハードウェアがすべての要件を満たしていることを確認できます。

## <a name="account-requirements"></a>アカウントの要件
通常は、Microsoft Azure に接続できるようにインターネットに接続された状態で開発キットをデプロイします。 この場合、開発キットをデプロイするために Azure Active Directory (Azure AD) アカウントを構成する必要があります。

環境がインターネットに接続されていない場合、または Azure AD を使いたくない場合は、Active Directory フェデレーション サービス (AD FS) を使って Azure Stack をデプロイできます。 開発キットには、専用の AD FS および Active Directory Domain Services のインスタンスが含まれています。 このオプションを使ってデプロイする場合は、事前にアカウントを設定する必要はありません。

>[!NOTE]
AD FS のオプションを使ってデプロイする場合は、Azure Stack を再デプロイして Azure AD に切り替える必要があります。

### <a name="azure-active-directory-accounts"></a>Azure Active Directory アカウント
Azure AD アカウントを使って Azure Stack をデプロイするには、デプロイ用の PowerShell スクリプトを実行する前に、Azure AD アカウントを準備する必要があります。 このアカウントは、Azure AD テナントの全体管理者になります。 このアカウントは、Azure Active Directory および Graph API と対話するすべての Azure Stack サービス用のアプリケーションおよびサービス プリンシパルのプロビジョニングと委任に使われます。 また、既定のプロバイダー サブスクリプションの所有者としても使われます (これは後で変更できます)。 このアカウントを使って、Azure Stack システムの管理ポータルにログインできます。

1. 少なくとも 1 つの Azure AD のディレクトリ管理者である Azure AD アカウントを作成します。 既にある場合は、それを使うことができます。 それ以外の場合は、[https://azure.microsoft.com/free/](https://azure.microsoft.com/pricing/free/) で無料で作成できます (中国では、代わりに <https://go.microsoft.com/fwlink/?LinkID=717821> にアクセスしてください)。 後で [Azure Stack を Azure に登録する](asdk-register.md)予定の場合は、この新規作成したアカウントでのサブスクリプションも必要です。
   
    サービス管理者として使うためにこれらの資格情報を保存します。 このアカウントは、リソース クラウド、ユーザー アカウント、テナント プラン、クォータ、価格を構成、管理することができます。 ポータルで、Web サイト クラウド、仮想マシン プライベート クラウドを作成したり、プランの作成やユーザーのサブスクリプションの管理を行えます。
1. テナントとして開発キットにサインインできるように、Azure AD で少なくとも 1 つのテスト ユーザー アカウントを作成します。
   
   | **Azure Active Directory アカウント** | **サポートの有無** |
   | --- | --- |
   | 有効なパブリック Azure サブスクリプションのある職場または学校アカウント |[はい] |
   | 有効なパブリック Azure サブスクリプションの Microsoft アカウント |[はい] |
   | 有効な中国の Azure サブスクリプションのある職場または学校アカウント |[はい] |
   | 有効な米国政府の Azure サブスクリプションのある職場または学校アカウント |[はい] |

デプロイの後、Azure Active Directory の全体管理者のアクセス許可は必要ありません。 ただし、一部の操作では、全体管理者の資格情報が必要な場合があります。 たとえば、リソース プロバイダーのインストーラー スクリプトや、アクセス許可を付与する必要のある新機能などがあります。 アカウントの全体管理者のアクセス許可を一時的に再配置するか、*既定のプロバイダー サブスクリプション*の所有者である個別の全体管理者アカウントを使用するかのいずれかを行うことができます。

## <a name="network"></a>ネットワーク
### <a name="switch"></a>Switch
スイッチの 1 つのポートを開発キット マシン用に使用できること。  

開発キット マシンでは、スイッチのアクセス ポートまたはトランク ポートへの接続がサポートされています。 スイッチでは特別な機能は必要ありません。 トランク ポートを使用している場合、または VLAN ID を構成する必要がある場合は、デプロイ パラメーターとして VLAN ID を指定する必要があります。

### <a name="subnet"></a>サブネット
次のサブネットには、開発キット マシンを接続しないでください。

* 192.168.200.0/24
* 192.168.100.0/27
* 192.168.101.0/26
* 192.168.102.0/24
* 192.168.103.0/25
* 192.168.104.0/25

これらのサブネットは、開発キット環境の内部ネットワーク用に予約されています。

### <a name="ipv4ipv6"></a>IPv4/IPv6
IPv4 のみがサポートされています。 IPv6 ネットワークを作成することはできません。

### <a name="dhcp"></a>DHCP
NIC を接続するネットワークで使用できる DHCP サーバーがあることを確認します。 DHCP が使用できない場合は、ホストが使用するためのものに加え、追加の静的 IPv4 ネットワークを準備する必要があります。 デプロイ パラメーターとしてその IP アドレスとゲートウェイも指定する必要があります。

### <a name="internet-access"></a>インターネットへのアクセス
Azure Stack は、直接または透過プロキシ経由で、インターネットにアクセスできる必要があります。 Azure Stack は、インターネット アクセスを有効にするための Web プロキシの構成をサポートしていません。 ホスト IP と (DHCP または静的 IP アドレスによって) AzS-BGPNAT01 に割り当てられた IP の両方が、インターネットにアクセスできる必要があります。 graph.windows.net および login.microsoftonline.com ドメインのポート 80 と 443 を使用します。


## <a name="next-steps"></a>次の手順
[ASDK 展開パッケージをダウンロードする](asdk-download.md)
