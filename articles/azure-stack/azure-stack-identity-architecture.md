---
title: Azure Stack の ID アーキテクチャ | Microsoft Docs
description: Azure Stack で使用できる ID アーキテクチャについて説明します。
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/01/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: bf69c71a8b361e4a147263bc60324573c710818f
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39412698"
---
# <a name="identity-architecture-for-azure-stack"></a>Azure Stack の ID アーキテクチャ
Azure Stack で使用する ID プロバイダーを選択する前に、Azure Active Directory (Azure AD) と Active Directory フェデレーション サービス (AD FS) のオプションの重要な違いを理解してください。 

## <a name="capabilities-and-limitations"></a>機能と制限 
選択する ID プロバイダーによっては、マルチテナントのサポートなどのオプションが制限されることがあります。 

  

|機能またはシナリオ        |Azure AD  |AD FS  |
|------------------------------|----------|-------|
|インターネットに接続されている     |[はい]       |省略可能|
|マルチテナントのサポート     |[はい]       |いいえ       |
|Marketplace で項目を提供する |[はい]       |はい。 [オフラインの Marketplace シンジケーション](azure-stack-download-azure-marketplace-item.md#disconnected-or-a-partially-connected-scenario) ツールを使用する必要があります。|
|Active Directory Authentication Library (ADAL) のサポート |[はい] |[はい]|
|Azure CLI、Visual Studio、PowerShell などのツールのサポート  |[はい] |[はい]|
|Azure Portal を通じてサービス プリンシパルを作成する     |[はい] |いいえ |
|証明書でサービス プリンシパルを作成する      |[はい] |[はい]|
|シークレット (キー) でサービス プリンシパルを作成する    |[はい] |いいえ |
|アプリケーションが Graph サービスを使用できる           |[はい] |いいえ |
|アプリケーションがサインインに ID プロバイダーを使用できる |[はい] |はい。 アプリケーションがオンプレミスの AD FS インスタンスとフェデレーションする必要があります。 |

## <a name="topologies"></a>トポロジ
以降のセクションでは、使用できる各種 ID トポロジについて説明します。

### <a name="azure-ad-single-tenant-topology"></a>Azure AD: シングルテナント トポロジ 
既定では、Azure Stack をインストールして Azure AD を使用すると、Azure Stack はシングルテナント トポロジを使用します。 

シングルテナント トポロジは、次のような場合に便利です。
- すべてのユーザーが、同じテナントに属している。
- サービス プロバイダーが、組織の Azure Stack インスタンスをホストする。 

![Azure AD による Azure Stack のシングルテナント トポロジ](media/azure-stack-identity-architecture/single-tenant.png)

このトポロジには次の特徴があります。
- Azure Stack がすべてのアプリケーションとサービスを同一の Azure AD テナント ディレクトリに登録します。 
- Azure Stack が、トークンを含めて、そのディレクトリのユーザーとアプリケーションのみを認証します。 
- 管理者 (クラウド オペレーター) とテナント ユーザーの ID が、同じディレクトリ テナントにあります。 
- 別のディレクトリのユーザーがこの Azure Stack 環境にアクセスできるようにするには、テナント ディレクトリに[ユーザーをゲストとして招待](azure-stack-identity-overview.md#guest-users)する必要があります。 

### <a name="azure-ad-multi-tenant-topology"></a>Azure AD: マルチテナント トポロジ
クラウド オペレーターは、1 つまたは複数の組織のテナントがアプリケーションにアクセスすることを許可するように Azure Stack を構成することができます。 ユーザーは、ユーザー ポータルを通じて、アプリケーションにアクセスします。 この構成では、管理ポータル (クラウド オペレーターが使用) は単一のディレクトリのユーザーに限定されます。 

マルチテナント トポロジは、次のような場合に便利です。
- サービス プロバイダーが、複数の組織のユーザーに Azure Stack へのアクセスを許可しようとしている。

![Azure AD による Azure Stack のマルチテナント トポロジ](media/azure-stack-identity-architecture/multi-tenant.png)

このトポロジには次の特徴があります。
- リソースへのアクセスは、組織単位で行う必要があります。 
- 1 つの組織のユーザーが組織外のユーザーにリソースへのアクセスを許可できないようにする必要があります。 
- 管理者 (クラウド オペレーター) の ID は、ユーザーの ID とは別のディレクトリ テナントに置くことができます。 このように分けることで、ID プロバイダー レベルでのアカウント分離が実現します。 
 
### <a name="ad-fs"></a>AD FS  
AD FS トポロジは、次のいずれかの条件に該当する場合に必要です。
- Azure Stack がインターネットに接続しない。
- Azure Stack がインターネットに接続できるが、ID プロバイダーのために AD FS を使用することを選択する。
  
![AD FS を使用する Azure Stack トポロジ](media/azure-stack-identity-architecture/adfs.png)

このトポロジには次の特徴があります。
- 運用環境でのこのトポロジの使用をサポートするには、フェデレーションの信頼を介して、組み込みの Azure Stack AD FS インスタンスを、Active Directory で支援されている既存の AD FS インスタンスに統合する必要があります。 
- Azure Stack の Graph サービスを既存の Active Directory インスタンスと統合することができます。 また、Azure AD Graph API との一貫性がある API をサポートしている OData ベースの Graph API サービスを使用することもできます。 

  Graph API が Active Directory インスタンスと対話するためには、その Active Directory インスタンスに、読み取り専用アクセス許可を持つユーザーの資格情報が必要となります。 
  - 組み込みの AD FS インスタンスは、Windows Server 2016 がベースになっています。 
  - 使用する AD FS と Active Directory インスタンスは、Windows Server 2012 またはそれ以降がベースである必要があります。 
  
  使用する Active Directory インスタンスと組み込み AD FS インスタンスの間で、対話は OpenID Connect に限定されず、相互にサポートされている任意のプロトコルを使用できます。 
  - ユーザー アカウントは、オンプレミスの Active Directory インスタンスで作成され、管理されます。
  - アプリケーションのサービス プリンシパルと登録は、組み込みの Active Directory インスタンスで管理されます。



## <a name="next-steps"></a>次の手順
- [ID の概要](azure-stack-identity-overview.md)   
- [データセンターの統合 - ID](azure-stack-integrate-identity.md)
