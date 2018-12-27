---
title: Azure AD Connect および Connect Health とは | Microsoft Docs
description: オンプレミス環境を Azure AD と同期させ、監視するために使用されるツールについて説明します。
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: get-started-article
ms.date: 11/28/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 2fbd6edb02dfc4080d7692e43324a5b3981091f9
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53110402"
---
# <a name="what-is-azure-ad-connect"></a>Azure AD Connect とは

Azure AD Connect は、ハイブリッド ID の目標に適合し、それを達成するように設計された Microsoft のツールです。  また、以下のような特徴があります。
    
- [パスワード ハッシュ同期](whatis-phs.md) - ユーザーのオンプレミス AD パスワードのハッシュを Azure AD と同期させるサインイン方法。
- [パススルー認証](how-to-connect-pta.md) - ユーザーがオンプレミスとクラウド内で同じパスワードを使用できるようにするサインイン方法ですが、フェデレーション環境の追加のインフラストラクチャは必要ありません。
- [フェデレーション統合](how-to-connect-fed-whatis.md) - フェデレーションは Azure AD Connect のオプション部分であり、オンプレミスの AD FS インフラストラクチャを使ってハイブリッド環境を構成するために使用できます。 証明書の更新や追加の AD FS サーバー デプロイなどの AD FS 管理機能も提供されます。
- [同期](how-to-connect-sync-whatis.md) - ユーザー、グループ、およびその他のオブジェクトを作成する役割を果たします。  また、オンプレミスのユーザーやグループの ID 情報をクラウド側と一致させる役割もあります。  この同期にはパスワード ハッシュも含まれます。
-   [正常性の監視](whatis-hybrid-identity-health.md) - Azure AD Connect Health は、堅牢な監視を提供したり、このアクティビティを表示するための Azure Portal 内の中央の場所を提供したりできます。 


![What is Azure AD Connect](./media/whatis-hybrid-identity/arch.png)



## <a name="what-is-azure-ad-connect-health"></a>Azure AD Connect Health とは

Azure Active Directory (Azure AD) Connect Health では、オンプレミス ID インフラストラクチャの堅牢な監視が提供されます。 Office 365 や Microsoft Online Services への信頼性の高い接続を維持することができます。  この信頼性は、主要な ID コンポーネントの監視機能を提供することで実現されます。 また、これらのコンポーネントに関する主要なデータ ポイントにアクセスしやすくなります。

この情報は、[Azure AD Connect Health ポータル](https://aka.ms/aadconnecthealth)に表示されます。 Azure AD Connect Health ポータルを使用してアラート、パフォーマンスの監視、使用状況の分析、その他の情報を表示します。 Azure AD Connect Health では、1 つのレンズで主要な ID コンポーネントの正常性をまとめて確認できます。

![What is Azure AD Connect Health](./media/whatis-hybrid-identity-health/aadconnecthealth2.png)

## <a name="why-use-azure-ad-connect"></a>Azure AD Connect を使用する理由
オンプレミスのディレクトリと Azure AD を統合すると、クラウドとオンプレミス両方のリソースにアクセスするための共通の ID が提供されるため、ユーザーの生産性が向上します。 ユーザーや組織にとっては次の利点があります。

* ユーザーは、単一の ID を使ってオンプレミスのアプリケーションとクラウド サービス (Office 365 など) にアクセスできます。
* 単一のツールにより、同期とサインインのための容易なデプロイメントを実現できます。
* それぞれのシナリオに適した最新の機能が手に入ります。 Azure AD Connect は、DirSync や Azure AD Sync など、旧バージョンの ID 統合ツールの後継ツールです。詳細については、「 [ハイブリッド ID ディレクトリ統合ツールの比較](plan-hybrid-identity-design-considerations-tools-comparison.md)」を参照してください。

## <a name="why-use-azure-ad-connect-health"></a>Azure AD Connect Health を使用する理由
Azure AD を使用すると、クラウドとオンプレミスの両方のリソースに共通の ID でアクセスできるため、ユーザーの生産性が向上します。 ユーザーがこれらのリソースにアクセスできるように、環境を確実に信頼できるものにすることが課題となります。  Azure AD Connect Health は、オンプレミスの ID インフラストラクチャを監視して把握し、この環境の信頼性を確保するのに役立ちます。 オンプレミスの各 ID サーバーにエージェントをインストールするぐらいにシンプルです。


## <a name="next-steps"></a>次の手順

- [ハードウェアおよび前提条件](how-to-connect-install-prerequisites.md) 
- [簡単設定](how-to-connect-install-express.md)
- [カスタマイズした設定](how-to-connect-install-custom.md)
- [Azure AD Connect Health エージェントをインストールする](how-to-connect-health-agent-install.md) 
