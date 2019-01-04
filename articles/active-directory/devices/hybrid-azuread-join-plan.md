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
ms.date: 11/01/2018
ms.author: markvi
ms.reviewer: sandeo
ms.openlocfilehash: c951d4f646fdaec9731ec4b6320e5f625ad91a42
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/03/2019
ms.locfileid: "53993289"
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
|![○][1]|デバイスのハイブリッド Azure AD 参加を制御する方法を確認する|
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

ダウンレベルの Windows デバイスのハイブリッド Azure AD 参加:

- 非フェデレーション環境において、[Azure Active Directory シームレス シングル サインオン](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start)を介してサポート**されます**。 

- シームレス シングル サインオンを使用せずに Azure AD パススルー認証を使用する場合、サポート**されません**。

- 資格情報ローミングまたはユーザー プロファイル ローミングを使用する場合、または仮想デスクトップ インフラストラクチャ (VDI) を使用する場合、サポート**されません**。


ドメイン コントローラー (DC) ロールを実行する Windows Server の登録はサポートされていません。

組織が認証された送信プロキシ経由でインターネットにアクセスする必要がある場合は、Windows 10 コンピューターが送信プロキシに対して正常に認証されることを確認する必要があります。 Windows 10 コンピューターではマシン コンテキストを使用してデバイス登録が実行されるため、マシン コンテキストを使用して送信プロキシ認証を構成する必要があります。


ハイブリッド Azure AD 参加は、オンプレミスのドメインに参加しているデバイスを Azure AD に自動的に登録するプロセスです。 場合によっては、すべてのデバイスを自動的に登録したくないことがあります。 このような場合は、「[デバイスのハイブリッド Azure AD Join を制御する方法](hybrid-azuread-join-control.md)」を参照してください。

## <a name="review-how-to-control-the-hybrid-azure-ad-join-of-your-devices"></a>デバイスのハイブリッド Azure AD 参加を制御する方法を確認する

ハイブリッド Azure AD 参加は、オンプレミスのドメインに参加しているデバイスを Azure AD に自動的に登録するプロセスです。 場合によっては、すべてのデバイスを自動的に登録したくないことがあります。 たとえば、すべてが期待どおりに機能することを確認するための最初のロールアウト中にこのような状況が発生します。

詳しくは、「[デバイスのハイブリッド Azure AD 参加を制御する方法](hybrid-azuread-join-control.md)」をご覧ください

## <a name="select-your-scenario"></a>シナリオを選択する

以下のシナリオでは、ハイブリッド Azure AD 参加を構成できます。

- マネージド ドメイン
- フェデレーション ドメイン  



環境にマネージド ドメインがある場合、ハイブリッド Azure AD 参加は以下をサポートします:

- パススルー認証 (PTA)

- パスワード ハッシュの同期 (PHS)

バージョン 1.1.819.0 以降の Azure AD Connect には、ハイブリッド Azure AD 参加を構成するためのウィザードが用意されています。 このウィザードを使用すると、構成プロセスを大幅に簡略化できます。 詳細については、次を参照してください。

- [フェデレーション ドメイン用のハイブリッド Azure Active Directory Join の構成](hybrid-azuread-join-federated-domains.md)


- [マネージド ドメイン用のハイブリッド Azure Active Directory Join の構成](hybrid-azuread-join-managed-domains.md)


 Azure AD Connect の必要なバージョンをインストールすることができない場合は、[デバイス登録を手動で構成する方法](../device-management-hybrid-azuread-joined-devices-setup.md)に関するページを参照してください。 


## <a name="alternate-login-id-support-in-hybrid-azure-ad-join"></a>ハイブリッド Azure AD 参加における代替ログイン ID のサポート

Windows 10 のハイブリッド Azure AD 参加は、代替ログイン ID、[認証方法](https://docs.microsoft.com/azure/security/azure-ad-choose-authn)、ドメインの種類、および Windows 10 のバージョンに基づいて、[代替ログイン ID](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) の限定的なサポートを提供します。 環境内に存在できる代替ログイン ID は 2 種類あります。

 - ルーティング可能な代替ログイン ID:ルーティング可能な代替ログイン ID は、ドメイン レジストラーに登録されている有効な確認済みドメインを持ちます。 たとえば、contoso.com がプライマリ ドメインの場合、contoso.org および contoso.co.uk は、Contoso 社によって所有され、[Azure AD で確認](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)されている有効なドメインです
 
 - ルーティング不可能な代替ログイン ID:ルーティング不可能な代替ログイン ID は、確認済みドメインを持ちません。 組織のプライベート ネットワーク内でのみ適用されます。 たとえば、contoso.com がプライマリ ドメインの場合、contoso.local はインターネットで確認済みのドメインではありませんが、Contoso 社のネットワーク内で使用されます。
 
次の表は、Windows 10 のハイブリッド Azure AD 参加における、これらの代替ログイン ID のいずれかのサポートに関する詳細を示しています

|代替ログイン ID の種類|ドメインの種類|Windows 10 のバージョン|説明|
|-----|-----|-----|-----|
|ルーティング可能|フェデレーション |1703 リリースから|一般公開|
|ルーティング可能|管理者常駐型|1709 リリースから|現在はプライベート プレビューの段階です。 Azure AD SSPR はサポートされていません |
|ルーティング不可能|フェデレーション|1803 リリースから|一般公開|
|ルーティング不可能|管理者常駐型|サポートされていません||



## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [フェデレーション ドメインのハイブリッド Azure Active Directory 参加を構成する](hybrid-azuread-join-federated-domains.md)
> [マネージド ドメインのハイブリッド Azure Active Directory 参加を構成する](hybrid-azuread-join-managed-domains.md)




<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
