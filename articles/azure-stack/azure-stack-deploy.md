---
title: "Azure Stack 開発キットのデプロイの前提条件 | Microsoft Docs"
description: "Azure Stack 開発キットの環境とハードウェアの要件を確認できます (クラウド オペレーター)。"
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 32a21d9b-ee42-417d-8e54-98a7f90f7311
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/08/2017
ms.author: jeffgilb
ms.openlocfilehash: 0fa0d00112e731a9f2effd453ba74f5561fca358
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/13/2017
---
# <a name="azure-stack-deployment-prerequisites"></a>Azure Stack のデプロイの前提条件

*適用対象: Azure Stack 開発キット*

[Azure Stack Development Kit](azure-stack-poc.md) をデプロイする前に、コンピューターが次の要件を満たしていることを確認してください。


## <a name="hardware"></a>ハードウェア
| コンポーネント | 最小値 | 推奨 |
| --- | --- | --- |
| ディスク ドライブ: オペレーティング システム |システム パーティション用に最低 200 GB の空きがある1 OS ディスク (SSD または HDD) |システム パーティション用に最低 200 GB の空きがある1 OS ディスク (SSD または HDD) |
| ディスク ドライブ: 開発キット データ全般* |4 つのディスク。 各ディスクに最低 140 GB の空き容量が必要 (SSD または HDD)。 すべての使用可能なディスクが使われます。 |4 つのディスク。 各ディスクに最低 250 GB の空き容量が必要 (SSD または HDD)。 すべての使用可能なディスクが使われます。 |
| コンピューティング: CPU |デュアル ソケット: 12 個の物理コア (合計) |デュアル ソケット: 16 個の物理コア (合計) |
| コンピューティング: メモリ |96 GB RAM |128 GB RAM (これは、PaaS リソース プロバイダーをサポートするための最低限の量です。)|
| コンピューティング: BIOS |Hyper-V 有効 (SLAT サポートあり) |Hyper-V 有効 (SLAT サポートあり) |
| ネットワーク: NIC |NIC には Windows Server 2012 R2 の認定が必要です。特別な機能は必要ありません |NIC には Windows Server 2012 R2 の認定が必要です。特別な機能は必要ありません |
| ハードウェア ロゴ認定 |[Windows Server 2012 R2 認定](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |[Windows Server 2012 R2 認定](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |

\* Azure から [Marketplace アイテム](azure-stack-download-azure-marketplace-item.md)の多くを追加する計画の場合は、この推奨容量より多くが必要です。

**データ ディスク ドライブの構成:** すべてのデータ ドライブは同じ種類 (すべて SAS、すべて SATA、またはすべて NVMe)、同じ容量である必要があります。 SAS ディスク ドライブを使う場合、ディスク ドライブは 1 つのパス経由で接続する必要があります (MPIO 、マルチパスはサポートされません)。

**HBA 構成オプション**

* (推奨) 単純な HBA
* RAID HBA – "パススルー" モードでアダプターを構成する必要があります。
* RAID HBA – ディスクは単一のディスク、RAID-0 として構成する必要があります。

**サポート対象のバスとメディアの組み合わせ**

* SATA HDD
* SAS HDD
* RAID HDD
* RAID SSD (メディアの種類が指定されていない場合、または不明な場合\*)
* SATA SSD + SATA HDD
* SAS SSD + SAS HDD
* NVMe

\* パススルー機能のない RAID コントローラーは、メディアの種類を認識できません。 このようなコントローラーでは、SSD と HDD の両方が "Unspecified" (指定なし) としてマークされます。 その場合、SSD がキャッシュ デバイスではなく、永続的なストレージとして使用されます。 したがって、それらの SSD には開発キットをデプロイできます。

**HBA の例**: パススルー モードの LSI 9207-8i、LSI-9300-8i、または LSI-9265-8i

サンプル OEM 構成を使用できます。

## <a name="operating-system"></a>オペレーティング システム
|  | **要件** |
| --- | --- |
| **OS バージョン** |Windows Server 2012 R2 以降。 Azure Stack のインストールに含まれる VHD でホスト コンピューターを起動するので、デプロイが開始する前のオペレーティング システムのバージョンは重要ではありません。 OS とすべての必要な更新プログラムは、イメージに既に統合されています。 開発キットで使われている Windows Server インスタンスをアクティブ化するためにキーを使わないでください。 |

## <a name="deployment-requirements-check-tool"></a>デプロイ要件チェック ツール
オペレーティング システムをインストールした後は、[Deployment Checker for Azure Stack](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) を使って、ハードウェアがすべての要件を満たしていることを確認できます。

## <a name="account-requirements"></a>アカウントの要件
通常は、Microsoft Azure に接続できるようにインターネットに接続された状態で開発キットをデプロイします。 この場合、開発キットをデプロイするために Azure Active Directory (Azure AD) アカウントを構成する必要があります。

環境がインターネットに接続されていない場合、または Azure AD を使いたくない場合は、Active Directory フェデレーション サービス (AD FS) を使って Azure Stack をデプロイできます。 開発キットには、専用の AD FS および Active Directory Domain Services のインスタンスが含まれています。 このオプションを使ってデプロイする場合は、事前にアカウントを設定する必要はありません。

>[!NOTE]
AD FS のオプションを使ってデプロイする場合は、Azure Stack を再デプロイして Azure AD に切り替える必要があります。

### <a name="azure-active-directory-accounts"></a>Azure Active Directory アカウント
Azure AD アカウントを使って Azure Stack をデプロイするには、デプロイ用の PowerShell スクリプトを実行する前に、Azure AD アカウントを準備する必要があります。 このアカウントは、Azure AD テナントの全体管理者になります。 このアカウントは、Azure Active Directory および Graph API と対話するすべての Azure Stack サービス用のアプリケーションおよびサービス プリンシパルのプロビジョニングと委任に使われます。 また、既定のプロバイダー サブスクリプションの所有者としても使われます (これは後で変更できます)。 このアカウントを使って、Azure Stack システムの管理ポータルにログインできます。

1. 少なくとも 1 つの Azure AD のディレクトリ管理者である Azure AD アカウントを作成します。 既にある場合は、それを使うことができます。 ない場合は、[http://azure.microsoft.com/en-us/pricing/free-trial/](http://azure.microsoft.com/pricing/free-trial/) (中国の場合は <http://go.microsoft.com/fwlink/?LinkID=717821>) において無料で作成できます。 後で [Azure Stack を Azure に登録する](azure-stack-register.md)予定の場合は、この新規作成したアカウントでのサブスクリプションも必要です。
   
    「[Deploy the development kit](azure-stack-run-powershell-script.md)」(開発キットをデプロイする) の手順 6 で使うので、資格情報を保存しておきます。 この*サービス管理者*アカウントは、リソース クラウド、ユーザー アカウント、テナント プラン、クォータ、価格を構成、管理することができます。 ポータルで、Web サイト クラウド、仮想マシン プライベート クラウドを作成したり、プランの作成やユーザーのサブスクリプションの管理を行えます。
2. テナントとして開発キットにサインインできるように、少なくとも 1 つのアカウントを[作成](azure-stack-add-new-user-aad.md)します。
   
   | **Azure Active Directory アカウント** | **サポートの有無** |
   | --- | --- |
   | 有効なパブリック Azure サブスクリプションのある職場または学校アカウント |あり |
   | 有効なパブリック Azure サブスクリプションの Microsoft アカウント |あり |
   | 有効な中国の Azure サブスクリプションのある職場または学校アカウント |あり |
   | 有効な米国政府の Azure サブスクリプションのある職場または学校アカウント |あり |

## <a name="network"></a>ネットワーク
### <a name="switch"></a>スイッチ
スイッチの 1 つのポートを開発キット マシン用に使用できること。  

開発キット マシンでは、スイッチのアクセス ポートまたはトランク ポートへの接続がサポートされています。 スイッチでは特別な機能は必要ありません。 トランク ポートを使用している場合、または VLAN ID を構成する必要がある場合は、デプロイ パラメーターとして VLAN ID を指定する必要があります。 [デプロイ パラメーターの一覧](azure-stack-run-powershell-script.md)で例を見ることができます。

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
NIC を接続するネットワークで使用できる DHCP サーバーがあることを確認します。 DHCP が使用できない場合は、ホストが使用するためのものに加え、追加の静的 IPv4 ネットワークを準備する必要があります。 デプロイ パラメーターとしてその IP アドレスとゲートウェイも指定する必要があります。 [デプロイ パラメーターの一覧](azure-stack-run-powershell-script.md)で例を見ることができます。

### <a name="internet-access"></a>インターネットへのアクセス
Azure Stack は、直接または透過プロキシ経由で、インターネットにアクセスできる必要があります。 Azure Stack は、インターネット アクセスを有効にするための Web プロキシの構成をサポートしていません。 ホスト IP と (DHCP または静的 IP アドレスによって) MAS-BGPNAT01 に割り当てられた IP の両方が、インターネットにアクセスできる必要があります。 graph.windows.net および login.microsoftonline.com ドメインのポート 80 と 443 を使用します。


## <a name="next-steps"></a>次のステップ
[Azure Stack 開発キット デプロイ パッケージをダウンロードする](https://azure.microsoft.com/overview/azure-stack/try/?v=try)

[Azure Stack 開発キットをデプロイする](azure-stack-run-powershell-script.md)

