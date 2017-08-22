---
title: "Azure Active Directory のデバイス管理の概要 | Microsoft Docs"
description: "デバイス管理を利用して、環境内のリソースにアクセスしているデバイスを管理する方法について説明します。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: markvi
ms.reviewer: jairoc
ms.translationtype: HT
ms.sourcegitcommit: b6c65c53d96f4adb8719c27ed270e973b5a7ff23
ms.openlocfilehash: 679daadc2be7b55c2c180f5f88c2595844a81ea1
ms.contentlocale: ja-jp
ms.lasthandoff: 08/17/2017

---
# <a name="introduction-to-device-management-in-azure-active-directory"></a>Azure Active Directory のデバイス管理の概要

モバイル ファースト、クラウド ファーストの世界では、Azure Active Directory (Azure AD) を使用して、デバイス、アプリ、およびサービスにどこからでもシングル サインオンできます。 Bring Your Own Device (BYOD) を含むデバイスの急増によって、IT プロフェッショナルは、次の 2 つの対立する目標を達成することを迫られています。

- 場所や時間を問わず、常にエンド ユーザーの生産性を高められるようにすること
- 企業の資産を常に保護すること

ユーザーは、デバイスを通して企業の資産にアクセスしています。 IT 管理者は、企業の資産を保護するために、これらのデバイスを管理します。 管理することによって、ユーザーがセキュリティとコンプライアンスの基準と一致するデバイスからリソースにアクセスしていることを確信できます。 

このトピックでは、Azure AD のデバイス管理によって、この目標を達成する方法について説明します。

## <a name="getting-devices-under-the-control-of-azure-ad"></a>デバイスを Azure AD の管理下におく

デバイスを Azure AD の管理下におくには、次の 2 つのオプションがあります。

- 登録 
- 参加

デバイスを Azure AD に**登録**すると、デバイスの ID を管理できるようになります。 デバイスが登録されると、ユーザーが Azure AD にサインインしたときにデバイスを認証するために使用される ID が、Azure AD のデバイス登録によって指定されます。 この ID を使用して、デバイスを有効または無効にすることができます。

Microsoft Intune などのモバイル デバイス管理 (MDM) ソリューションと組み合わせて使用すると、Azure AD 内のデバイスの属性は、デバイスに関する情報が追加されて更新されます。 これにより、条件付きアクセス規則を作成できます。この規則に従い、デバイスからのアクセス時にセキュリティおよび法令遵守の基準を満たす必要があります。 Microsoft Intune へのデバイスの登録の詳細については、「管理するデバイスを Intune に登録する」をご覧ください。

デバイスを**参加**させるオプションは、デバイスを登録するオプションの拡張版です。 つまり、デバイスを登録するオプションが持っているすべての利点に加え、デバイスのローカル状態を変更することもできます。 ローカル状態を変更することで、ユーザーが個人アカウントではなく、職場または学校アカウントを使用してデバイスにサインインできるようになります。

## <a name="azure-ad-registered-devices"></a>Azure AD 登録済みデバイス   

Azure AD 登録デバイス済みの目標は、**Bring Your Own Device (BYOD)** シナリオに対するサポートを提供することです。 このシナリオでは、ユーザーは個人所有のデバイスを使用して、組織の Azure Active Directory の管理下にあるリソースにアクセスできます。  

![Azure AD 登録済みデバイス](./media/device-management-introduction/03.png)

アクセスは、デバイスに入力されている職場または学校のアカウントに基づいて行われます。  
たとえば、Windows 10 では、パーソナル コンピューター、タブレット、または電話に、ユーザーの職場または学校アカウントを追加できます。  
ユーザーがデバイスに職場または学校アカウントを追加している場合、デバイスが Azure AD に登録され、必要に応じて、組織が構成したモバイル デバイス管理 (MDM) システムに登録されます。 組織のユーザーは、個人所有のデバイスに職場または学校アカウントを非常に簡単に追加できます。

- 初めて作業アプリケーションにアクセスするとき
- Windows 10 の場合は、**[設定]** メニューを使用して手動で 

Windows 10、iOS、Android、および macOS で Azure AD 登録済みデバイスを構成できます。

## <a name="azure-ad-joined-devices"></a>Azure AD 参加済みデバイス

Azure AD 参加済みデバイスの目的は、次の操作を単純化することです。

- 職場所有のデバイスの Windows でのデプロイ 
- 任意の Windows デバイスからの組織のアプリとリソースへのアクセス

![Azure AD 登録済みデバイス](./media/device-management-introduction/02.png)


これらの目標は、ユーザーが職場所有のデバイスを Azure AD の管理下におくためのセルフサービス エクスペリエンスを用意することによって実現されます。  
**Azure AD に参加すること**は、クラウド ファースト/クラウド オンリーの組織向けです。 これらの組織は、通常は中規模から小規模の企業であり、オンプレミスの Windows Server Active Directory インフラストラクチャがありません。 

Azure AD 参加済みデバイスを実装すると、次のメリットを得ることができます。

- Azure が管理している SaaS アプリやサービスへの**シングル サインオン (SSO)**。 ユーザーが職場のリソースにアクセスするときに、追加の認証プロンプトが表示されることはありません。 SSO 機能は、ドメイン ネットワークに接続されていない場合も有効です。

- 参加デバイス間でのユーザー設定の**企業準拠のローミング**。 ユーザーは、デバイスの設定を確認するときに Microsoft アカウント (Hotmail など) に接続する必要はありません。

- AD アカウントを使用した**ビジネス向け Windows ストアへのアクセス**。 ユーザーは、組織によって事前選択されているアプリケーションのインベントリから選択できます。

- **Windows Hello** のサポート。

- コンプライアンス ポリシーを満たしているデバイスのみにアプリへのアクセスを許可する**アクセスの制限**。

Azure AD への参加は、主にオンプレミスの Windows Server Active Directory インフラストラクチャを持っていない組織向けですが、次のようなシナリオでも確実に使用できます。

- たとえばタブレットや電話などのモバイル デバイスを管理する必要があるが、オンプレミスのドメインへの参加を使用できない。

- ユーザーが主に必要としているのは、Office 365 や Azure AD に統合されているその他の SaaS アプリにアクセスすることである。

- ユーザーのグループを Active Directory ではなく Azure AD で管理したい。 これは、季節雇用される従業員、請負業者、受講者などに適用できます。

- リモートの支店の作業員に制限のあるオンプレミスのインフラに対する参加機能を提供したい。

Windows 10 デバイスで Azure AD 参加済みデバイスを構成できます。


## <a name="hybrid-azure-ad-joined-devices"></a>ハイブリッド Azure AD 参加済みデバイス

10 年以上にわたって、多くの組織は、オンプレミスの Active Directory へのドメインの参加を使用して、次の操作を実行できるようにしてきました。

- IT 部門が一元化された場所から職場所有のデバイスを管理する。

- ユーザーが Active Directory の職場または学校アカウントを使用して自分のデバイスにサインインする。 

通常、オンプレミスのフットプリントを持つ組織は、イメージング法を利用してデバイスをプロビジョニングし、多くの場合、**System Center Configuration Manager (SCCM)** または**グループ ポリシー(GP)** を使用してデバイスを管理します。

環境にオンプレミスの AD フットプリントがあるときに、Azure Active Directory が提供する機能も活用したい場合は、ハイブリッド Azure AD 参加済みデバイスを実装できます。 これらのデバイスは、オンプレミスの Active Directory と Azure Active Directory の両方に参加しているデバイスです。

![Azure AD 登録済みデバイス](./media/device-management-introduction/01.png)


以下に該当する場合は、ハイブリッド Azure AD 参加済みデバイスを使用する必要があります。

- NTLM または Kerberos を使用するデバイスに Win32 アプリをデプロイする。

- デバイスの管理に、GP または SCCM/DCM が必要である。

- 今後も社員のデバイスの構成にイメージング ソリューションを使用したい。

ハイブリッド Azure AD 参加済みデバイスは、Windows 10 デバイスと、Windows 8 や Windows 7 などのダウンレベルのデバイスで構成できます。

## <a name="summary"></a>まとめ

Azure AD のデバイス管理を使用して、次の操作を実行できます。 

- デバイスを Azure AD の管理下におくプロセスを簡略化する

- 組織のクラウドベースのリソースへの使いやすいアクセス方法をユーザーに提供する

おおざっぱにまとめると、次のようになります。

- 個人所有のデバイスでは、Azure AD 登録済みデバイスを使用する

- オンプレミスの AD に参加していないデバイスでは、Azure AD 参加済みデバイスを使用する 

- オンプレミスの AD に参加しているデバイスでは、ハイブリッド Azure AD 参加済みデバイスを使用する     




## <a name="next-steps"></a>次のステップ

ハイブリッド Azure AD 参加済みデバイスを設定するには、「[ハイブリッド Azure Active Directory 参加済みデバイスの構成方法](device-management-hybrid-azuread-joined-devices-setup.md)」を参照してください。



