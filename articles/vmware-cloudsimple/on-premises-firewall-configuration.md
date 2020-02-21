---
title: オンプレミスから Azure VMware Solutions (AVS) にアクセスする
description: ファイアウォール経由でオンプレミス ネットワークから Azure VMware Solutions (AVS) にアクセスする
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/08/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a4a9760b5c7a70c58a1afe1b14b781a35f2b9b18
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/08/2020
ms.locfileid: "77082973"
---
# <a name="accessing-your-avs-private-cloud-environment-and-applications-from-on-premises"></a>オンプレミスから AVS プライベート クラウド環境およびアプリケーションにアクセスする

Azure ExpressRoute またはサイト間 VPN を使用して、オンプレミス ネットワークから AVS への接続を設定できます。 この接続を使用して、AVS プライベート クラウドの vCenter と、AVS プライベート クラウドで実行するワークロードにアクセスします。 オンプレミス ネットワークのファイアウォールを使用して、接続で開くポートを制御できます。 この記事では、一般的なアプリケーションのポート要件について説明します。 その他のアプリケーションについては、アプリケーションのドキュメントでポート要件を参照してください。

## <a name="ports-required-for-accessing-vcenter"></a>VCenter にアクセスするために必要なポート

AVS プライベート クラウドの vCenter と NSX-T Manager にアクセスするには、次の表に定義されているポートをオンプレミスのファイアウォールで開く必要があります。 

| Port       | source                           | 宛先                      | 目的                                                                                                                |
|------------|----------------------------------|----------------------------------|------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)   | オンプレミスの DNS サーバー          | AVS プライベート クラウドの DNS サーバー        | オンプレミス ネットワークから AVS プライベート クラウドの DNS サーバーに *az.cloudsimple.io* の DNS 参照を転送するために必要です。     |
| 53 (UDP)   | AVS プライベート クラウドの DNS サーバー        | オンプレミスの DNS サーバー          | オンプレミスのドメイン名の DNS 参照を、AVS プライベート クラウドの vCenter からオンプレミスの DNS サーバーに転送するために必要です。 |
| 80 (TCP)   | オンプレミス ネットワーク              | AVS プライベート クラウドの管理ネットワーク | vCenter URL を *http* から *https* にリダイレクトするために必要です。                                                         |
| 443 (TCP)  | オンプレミス ネットワーク              | AVS プライベート クラウドの管理ネットワーク | オンプレミス ネットワークから vCenter と NSX-T Manager にアクセスするために必要です。                                           |
| 8000 (TCP) | オンプレミス ネットワーク              | AVS プライベート クラウドの管理ネットワーク | オンプレミスから AVS プライベート クラウドへの仮想マシンの vMotion に必要です。                                          |
| 8000 (TCP) | AVS プライベート クラウドの管理ネットワーク | オンプレミス ネットワーク              | AVS プライベート クラウドからオンプレミスへの仮想マシンの vMotion に必要です。                                          |

## <a name="ports-required-for-using-on-premises-active-directory-as-an-identity-source"></a>オンプレミスの Active Directory を ID ソースとして使用するために必要なポート

オンプレミスの Active Directory を AVS プライベート クラウドの vCenter 上の ID ソースとして構成するには、表に定義されているポートを開く必要があります。 構成手順については、「[AVS プライベート クラウド上で vCenter の ID プロバイダーとして Azure AD を使用する](https://docs.azure.cloudsimple.com/azure-ad/)」を参照してください。

| Port         | source                           | 宛先                                         | 目的                                                                                                                                          |
|--------------|----------------------------------|-----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)      | AVS プライベート クラウドの DNS サーバー        | オンプレミスの DNS サーバー                             | オンプレミスの Active Directory ドメイン名の DNS 参照を、AVS プライベート クラウドの vCenter からオンプレミスの DNS サーバーに転送するために必要です。        |
| 389 (TCP/UDP) | AVS プライベート クラウドの管理ネットワーク | オンプレミスの Active Directory ドメイン コントローラー     | ユーザー認証のため、AVS プライベート クラウドの vCenter サーバーから Active Directory ドメイン コントローラーへの LDAP 通信に必要です。              |
| 636 (TCP)     | AVS プライベート クラウドの管理ネットワーク | オンプレミスの Active Directory ドメイン コントローラー     | ユーザー認証のため、AVS プライベート クラウドの vCenter サーバーから Active Directory ドメイン コントローラーへのセキュリティで保護された LDAP (LDAPS) 通信に必要です。 |
| 3268 (TCP)    | AVS プライベート クラウドの管理ネットワーク | オンプレミスの Active Directory グローバル カタログ サーバー | マルチドメイン コントローラーのデプロイでの LDAP 通信に必要です。                                                                      |
| 3269 (TCP)    | AVS プライベート クラウドの管理ネットワーク | オンプレミスの Active Directory グローバル カタログ サーバー | マルチドメイン コントローラーのデプロイでの LDAPS 通信に必要です。                                                                     |                                           |

## <a name="common-ports-required-for-accessing-workload-virtual-machines"></a>ワークロード仮想マシンへのアクセスに必要な一般的なポート

AVS プライベート クラウドで実行されているワークロード仮想マシンにアクセスするには、オンプレミスのファイアウォールでポートが開いている必要があります。 次の表は、必要とされる一般的なポートとその用途を示しています。 アプリケーション固有のポートの要件については、アプリケーションのドキュメントを参照してください。

| Port         | source                         | 宛先                          | 目的                                                                              |
|--------------|--------------------------------|--------------------------------------|--------------------------------------------------------------------------------------|
| 22 (TCP)      | オンプレミス ネットワーク            | AVS プライベート クラウドのワークロード ネットワーク       | AVS プライベート クラウドで実行されている Linux 仮想マシンへの Secure Shell アクセス。            |
| 3389 (TCP)    | オンプレミス ネットワーク            | AVS プライベート クラウドのワークロード ネットワーク       | AVS プライベート クラウドで実行されている Windows 仮想マシンへのリモート デスクトップ。               |
| 80 (TCP)      | オンプレミス ネットワーク            | AVS プライベート クラウドのワークロード ネットワーク       | AVS プライベート クラウドで実行されている仮想マシンにデプロイされている Web サーバーにアクセスします。      |
| 443 (TCP)     | オンプレミス ネットワーク            | AVS プライベート クラウドのワークロード ネットワーク       | AVS プライベート クラウドで実行されている仮想マシンにデプロイされている、セキュリティで保護された Web サーバーにアクセスします。 |
| 389 (TCP/UDP) | AVS プライベート クラウドのワークロード ネットワーク | オンプレミスの Active Directory ネットワーク | Windows ワークロード仮想マシンをオンプレミスの Active Directory ドメインに結合します。     |
| 53 (UDP)      | AVS プライベート クラウドのワークロード ネットワーク | オンプレミス ネットワーク                  | ワークロード仮想マシンからオンプレミスの DNS サーバーへの DNS サービス アクセス。       |

## <a name="next-steps"></a>次のステップ

* [VLAN とサブネットを作成して管理する](https://docs.azure.cloudsimple.com/create-vlan-subnet/)
* [Azure ExpressRoute を使用してオンプレミスのネットワークに接続する](https://docs.azure.cloudsimple.com/on-premises-connection/)
* [オンプレミスからのサイト間 VPN を設定する](https://docs.azure.cloudsimple.com/vpn-gateway/)
