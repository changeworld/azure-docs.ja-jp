---
title: ハイブリッド Azure Active Directory 参加済みデバイスの構成方法 | Microsoft Docs
description: ハイブリッド Azure Active Directory 参加済みデバイスの構成方法について説明します。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2018
ms.author: markvi
ms.reviewer: sandeo
ms.openlocfilehash: fbc40d0768a7cf1cb83a3e78dd524fac26254bea
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2018
ms.locfileid: "42143334"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>ハイブリッド Azure Active Directory Join の実装を計画する方法

ユーザーと同じく、デバイスは、保護したいと同時に、いつでもどこでもリソースを保護するために使用したいもう 1 つの ID になりつつあります。 この目標は、次のいずれかの方法を使用してデバイスの ID を Azure AD に設定することで達成できます。

- Azure AD 参加
- ハイブリッド Azure AD 参加
- Azure AD の登録

Azure AD にデバイスを設定して、クラウドとオンプレミスのリソースでのシングル サインオン (SSO) を実現することで、ユーザーの生産性を最大化できます。 同時に、[条件付きアクセス](../active-directory-conditional-access-azure-portal.md)を使用して、クラウドとオンプレミスのリソースへのアクセスを保護することもできます。

オンプレミスの Active Directory 環境があるときに、ドメイン参加済みデバイスを Azure AD に参加させたい場合は、ハイブリッド Azure AD 参加済みデバイスを構成することによってこれを実現できます。 この記事では、ご使用の環境でハイブリッド Azure AD 参加を実装するための関連する手順について説明します。 


## <a name="prerequisites"></a>前提条件

この記事では、[Azure Active Directory でのデバイス管理の概要](../device-management-introduction.md)を理解していることを前提とします


## <a name="plan-your-implementation"></a>実装の計画

ハイブリッド Azure AD の実装を計画するには、以下を理解する必要があります。

|   |   |
|---|---|
|![○][1]|サポート対象デバイスを確認する|
|![○][1]|知っておくべきことを確認する|
|![○][1]|シナリオを選択する|


 


## <a name="review-supported-devices"></a>サポート対象デバイスを確認する 

ハイブリッド Azure AD 参加は、幅広い Windows デバイスをサポートしています。 旧バージョンの Windows を実行しているデバイスの構成の場合、追加の手順または異なる手順が必要なので、サポートされるデバイスは 2 つのカテゴリにグループ化されます。

**最新の Windows デバイス**

- Windows 10
    
- Windows Server 2016


Windows デスクトップ オペレーティング システムを実行するデバイスの場合、サポートされるバージョンは Windows 10 Anniversary Update (Version 1607) 以降です。 ベスト プラクティスとして、Windows 10 の最新バージョンにアップグレードしてください。



 **ダウンレベルの Windows デバイス**

- Windows 8.1
 
- Windows 7

- Windows Server 2012 R2
 
- Windows Server 2012 
 
- Windows Server 2008 R2 


最初の計画手順として、環境を確認し、ダウンレベルの Windows デバイスをサポートする必要があるかどうかを判断する必要があります。



## <a name="review-things-you-should-know"></a>知っておくべきことを確認する

複数の Azure AD テナントに ID データを同期させた単一フォレストで構成されている環境の場合、ハイブリッド Azure AD 参加を使用することはできません。

システム準備ツール (Sysprep) を利用している場合は、必ずハイブリッド Azure AD 参加用に構成されていない Windows のインストールからイメージを作成してください。

仮想マシン (VM) のスナップショットを利用して追加の VM を作成する場合は、必ずハイブリッド Azure AD 参加用に構成されていない VM スナップショットを使用してください。

ユーザー プロファイルのローミングまたは資格情報のローミング用に構成されたデバイスでは、ダウンレベルの Windows デバイスの登録はサポートされていません。 プロファイルまたは設定のローミングを使用している場合は、Windows 10 を使用してください。

- ダウンレベルの Windows デバイスの登録は、非フェデレーション環境において、[Azure Active Directory シームレス シングル サインオン](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start)のシームレス シングル サインオンを介してサポート**されます**。 
 
- シームレス シングル サインオンを使用せずに Azure AD パススルー認証を使用する場合、ダウンレベルの Windows デバイスの登録はサポート**されません**。

- ダウンレベルの Windows デバイスの登録は、ローミング プロファイルを使用するデバイスではサポート**されません**。 プロファイルまたは設定のローミングを使用している場合は、Windows 10 を使用してください。


ドメイン コントローラー (DC) ロールを実行する Windows Server の登録はサポートされていません。

組織が認証された送信プロキシ経由でインターネットにアクセスする必要がある場合は、Windows 10 コンピューターが送信プロキシに対して正常に認証されることを確認する必要があります。 Windows 10 コンピューターではマシン コンテキストを使用してデバイス登録が実行されるため、マシン コンテキストを使用して送信プロキシ認証を構成する必要があります。


ハイブリッド Azure AD 参加は、オンプレミスのドメインに参加しているデバイスを Azure AD に自動的に登録するプロセスです。 場合によっては、すべてのデバイスを自動的に登録したくないことがあります。 このような場合は、「[デバイスのハイブリッド Azure AD Join を制御する方法](hybrid-azuread-join-control.md)」を参照してください。



## <a name="select-your-scenario"></a>シナリオを選択する

以下のシナリオでは、ハイブリッド Azure AD 参加を構成できます。

- マネージド ドメイン
- フェデレーション ドメイン  



環境にマネージド ドメインがある場合、ハイブリッド Azure AD 参加は以下をサポートします:

- シームレス シングル サインオン (SSO) によるパス スルー認証 (PTA) 

- シームレス シングル サインオン (SSO) によるパスワード同期 (PHS) 

バージョン 1.1.819.0 以降の Azure AD Connect には、ハイブリッド Azure AD 参加を構成するためのウィザードが用意されています。 このウィザードを使用すると、構成プロセスを大幅に簡略化できます。 詳細については、次を参照してください。

- [フェデレーション ドメイン用のハイブリッド Azure Active Directory Join の構成](hybrid-azuread-join-federated-domains.md)

- [マネージド ドメイン用のハイブリッド Azure Active Directory Join の構成](hybrid-azuread-join-managed-domains.md)


 Azure AD Connect の必要なバージョンをインストールすることができない場合は、[デバイス登録を手動で構成する方法](../device-management-hybrid-azuread-joined-devices-setup.md)に関するページを参照してください。 






## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [フェデレーション ドメインのハイブリッド Azure Active Directory 参加を構成する](hybrid-azuread-join-federated-domains.md)
> [マネージド ドメインのハイブリッド Azure Active Directory 参加を構成する](hybrid-azuread-join-managed-domains.md)




<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
