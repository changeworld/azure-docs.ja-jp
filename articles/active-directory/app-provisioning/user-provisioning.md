---
title: Azure Active Directory での SaaS アプリ ユーザー プロビジョニングの自動化とは
description: Azure Active Directory を使用して、複数のサードパーティ SaaS アプリケーション間でユーザー アカウントを自動的にプロビジョニング、プロビジョニング解除、継続的に更新する方法の紹介。
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: overview
ms.workload: identity
ms.date: 05/28/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 78533b519c2410744b3ec8adc7dd3269e018715f
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "129990560"
---
# <a name="what-is-app-provisioning-in-azure-active-directory"></a>Azure Active Directory でのアプリのプロビジョニングとは

Azure Active Directory (Azure AD) で *アプリのプロビジョニング* という用語は、アプリケーションのユーザーの ID とロールを自動的に作成することを意味します。
    
![プロビジョニング シナリオを示す図。](../governance/media/what-is-provisioning/provisioning.png)

Azure AD からサービスとしてのソフトウェア (SaaS) アプリケーションへのプロビジョニングは、ユーザーがアクセスする必要のあるクラウド ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) アプリケーションに、ユーザーの ID とロールを自動的に作成することを意味します。 自動プロビジョニングには、ユーザー ID の作成に加えて、状態または役割が変化したときのユーザー ID のメンテナンスおよび削除が含まれます。 一般的なシナリオには、[Dropbox](../../active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial.md)、[Salesforce](../../active-directory/saas-apps/salesforce-provisioning-tutorial.md)、[ServiceNow](../../active-directory/saas-apps/servicenow-provisioning-tutorial.md) などのアプリケーションへの Azure AD ユーザーのプロビジョニングが含まれます。

Azure AD では、SaaS アプリケーション、およびオンプレミスでホストされているアプリケーション、または仮想マシンなどのサービスとしてのインフラストラクチャ (IaaS) ソリューションへのユーザーのプロビジョニングがサポートされています。 LDAP ユーザー ストアや SQL データベースに依存するレガシ アプリケーションがある場合があります。 Azure AD プロビジョニング サービスを使用すると、ファイアウォールを開いたり、TCP ポートに対処したりすることなく、オンプレミス アプリケーションに対してユーザーの作成、更新、削除が行えます。 

軽量エージェントを使用すると、オンプレミス アプリケーションにユーザーをプロビジョニングして、アクセスを管理できます。 Azure AD をアプリケーション プロキシと組み合わせて使用すると、オンプレミス アプリケーションへのアクセスを管理し、自動ユーザー プロビジョニング (プロビジョニング サービスを使用) とシングル サインオン (アプリ プロキシを使用) を提供できます。 

アプリのプロビジョニングを使用すると、次のことができます。

- **プロビジョニングを自動化する:** 新しいユーザーがチームまたは組織に加わるときに、適切なシステムで新しいアカウントを自動的に作成できます。
- **プロビジョニング解除を自動化する**: ユーザーがチームまたは組織を離れるときに、適切なシステムでアカウントを自動的に非アクティブ化できます。
- **システム間でデータを同期する**: アプリとシステムの ID は、ディレクトリまたは人事システムでの変更に基づいて最新の状態に保たれることが保証されます。
- **グループをプロビジョニングする**: グループをサポートするアプリケーションにグループをプロビジョニングします。
- **アクセスを管理する**: アプリケーションにプロビジョニングされたユーザーの監視と監査を行います。
- **ブラウン フィールドのシナリオでシームレスにデプロイする**: ターゲット システムにユーザーが既に存在する場合でも、システム間で既存の ID を一致させ、簡単に統合できるようにします。
- **リッチなカスタマイズを使用する**: ソース システムからターゲット システムに送られる必要があるユーザー データが定義された、カスタマイズ可能な属性マッピングを利用します。
- **重大なイベントに関するアラートを受け取る**: プロビジョニング サービスで重大なイベントに関するアラートが提供され、ビジネス ニーズに合わせてカスタム アラートを定義できる Log Analytics の統合が可能になります。

## <a name="what-is-scim"></a>SCIM とは

プロビジョニングとプロビジョニング解除の自動化を助けるため、アプリでは独自のユーザー API とグループ API を公開します。 ただし、複数のアプリでユーザーを管理しようとすると、すべてのアプリで同じ操作 (ユーザーの作成や更新、ユーザーのグループへの追加、ユーザーのプロビジョニング解除など) が実行されることがわかります。 ただし、これらのすべての操作が、異なるエンドポイント パス、異なるユーザー情報指定方法、および情報の各要素を表す異なるスキーマを使用して、わずかに異なる方法で実装されています。

これらの課題に対処するため、クロスドメイン ID 管理システム (SCIM) 仕様では、アプリへの、アプリからの、およびアプリ内での移動に対し、共通のユーザー スキーマが提供されています。 SCIM は、プロビジョニングに対する事実上の標準になりつつあり、SAML (Security Assertions Markup Language) や OpenID Connect (OIDC) などのフェデレーション標準と一緒に使用すると、エンドツーエンドの標準ベースのアクセス管理用ソリューションが管理者に提供されます。

アプリケーションに対するユーザーとグループのプロビジョニングおよびプロビジョニング解除を自動化するための SCIM エンドポイントの開発について詳しくは、「[SCIM エンドポイントの構築とユーザー プロビジョニングの構成](use-scim-to-provision-users-and-groups.md)」をご覧ください。 Slack、Azure Databricks、Snowflake などのギャラリーにある事前に統合されたアプリケーションの場合は、開発者向けドキュメントをスキップし、「[SaaS アプリケーションと Azure Active Directory との統合に関するチュートリアル](../../active-directory/saas-apps/tutorial-list.md)」で説明されているチュートリアルを使用できます。

## <a name="manual-vs-automatic-provisioning"></a>手動プロビジョニングと自動プロビジョニングの比較

Azure AD ギャラリーのアプリケーションは、次の 2 つのプロビジョニング モードのいずれかをサポートしています。

* **手動** プロビジョニングとは、アプリの自動 Azure AD プロビジョニング コネクタがまだないことを意味します。 ユーザー アカウントは手動で作成する必要があります。 たとえば、アプリの管理ポータルにユーザーを直接追加したり、ユーザー アカウントの詳細を含むスプレッドシートをアップロードしたりすることでこれを行います。 アプリから提供されるドキュメントを確認するか、アプリの開発者に問い合わせて、どのようなメカニズムが使用できるか判断してください。
* "**自動**" とは、このアプリケーション用の Azure AD プロビジョニング コネクタが開発済みであることを意味します。 そのアプリケーションのプロビジョニングの設定に固有の設定チュートリアルに従ってください。 アプリのチュートリアルは、「[SaaS アプリケーションと Azure Active Directory との統合に関するチュートリアル](../../active-directory/saas-apps/tutorial-list.md)」から見つけることができます。

アプリケーションでサポートされているプロビジョニング モードは、アプリケーションをエンタープライズ アプリに追加した後の **[プロビジョニング]** タブにも表示されます。

## <a name="benefits-of-automatic-provisioning"></a>自動プロビジョニングの利点

現代の組織では使用されるアプリケーションの数が増え続けており、IT 管理者は大規模なアクセス管理を強いられます。 SAML や OIDC などの標準により、管理者はシングル サインオン (SSO) をすばやく設定できますが、アクセスのためにユーザーをアプリにプロビジョニングする必要があります。 多くの管理者にとって、プロビジョニングとは、すべてのユーザー アカウントを手動で作成したり、毎週 CSV ファイルをアップロードしたりすることを意味します。 これらのプロセスは時間がかかり、コストがかかり、エラーが発生しやすくなります。 プロビジョニングを自動化するために、SAML Just-In-Time (JIT) などのソリューションが採用されています。 企業では、ユーザーが組織からいなくなる場合や、役割の変更のために特定のアプリにアクセスする必要がなくなる場合に、ユーザーをプロビジョニング解除するためのソリューションも必要です。

自動プロビジョニングを使用する一般的な動機には、次のようなものがあります。

- プロビジョニング プロセスの効率と正確さを最大限に高める。
- 独自に開発したプロビジョニング ソリューションやプロビジョニング スクリプトのホスティングとメンテナンスに伴うコストを抑える。
- ユーザーが組織を離れるときに、主要な SaaS アプリからその ID をすぐに削除することで、組織のセキュリティを高める。
- 多くのユーザーを特定の SaaS アプリまたは SaaS システムに簡単にインポートする。
- 誰をプロビジョニングし、誰がアプリにサインインできるようにするかを、1 つのポリシー セットで決定する。

Azure AD のユーザー プロビジョニングは、これらの課題に対応するのに役立ちます。 お客様による Azure AD ユーザー プロビジョニングの使用方法について詳しくは、[ASOS のケース スタディ](https://aka.ms/asoscasestudy)をご覧ください。 次のビデオでは、Azure AD でのユーザー プロビジョニングの概要について説明します。

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Azure AD の自動ユーザー プロビジョニングで利用できるアプリケーションとシステム

Azure AD は、一般的な多くの SaaS アプリや人事管理システムとの連携にあらかじめ対応しているほか、[SCIM 2.0 標準](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)の特定の領域を実装するアプリにも広く対応しています。

* **事前に統合されたアプリケーション (ギャラリー SaaS アプリ)** : Azure AD で事前統合プロビジョニング コネクタがサポートされているすべてのアプリケーションについては、「[SaaS アプリケーションと Azure Active Directory との統合に関するチュートリアル](../saas-apps/tutorial-list.md)」を参照してください。 ギャラリーに一覧表示されている事前統合アプリケーションでは、通常、プロビジョニングに SCIM 2.0 ベースのユーザー管理 API が使用されています。 

   ![DropBox、Salesforce、その他のロゴを示す画像。](./media/user-provisioning/gallery-app-logos.png)

   プロビジョニングのために新しいアプリケーションを要求したい場合は、[アプリケーションをアプリ ギャラリーと統合するよう要求する](../develop/v2-howto-app-gallery-listing.md)ことができます。 ユーザー プロビジョニング要求の場合、アプリケーションには SCIM 準拠のエンドポイントが必要です。 アプリをプラットフォームに迅速にオンボードできるよう、アプリケーションのベンダーに SCIM 標準に準拠するよう要求してください。

* **SCIM 2.0 をサポートするアプリケーション**: SCIM 2.0 ベースのユーザー管理 API を実装するアプリケーションを汎用的に接続する方法については、「[SCIM エンドポイントの構築とユーザー プロビジョニングの構成](use-scim-to-provision-users-and-groups.md)」を参照してください。

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>アプリケーションへの自動プロビジョニングを設定する方法

ギャラリーに一覧表示される事前統合アプリケーションについては、自動プロビジョニングの設定すに関するステップバイステップのガイダンスが用意されています。 「[SaaS アプリケーションと Azure Active Directory との統合に関するチュートリアル](../saas-apps/tutorial-list.md)」を参照してください。 次のビデオでは、SalesForce の自動ユーザー プロビジョニングの設定方法が説明されています。

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

SCIM 2.0 をサポートする他のアプリケーションについては、「[SCIM エンドポイントの構築とユーザー プロビジョニングの構成](use-scim-to-provision-users-and-groups.md)」の手順に従ってください。


## <a name="next-steps"></a>次の手順

- [SaaS アプリを統合する方法に関するチュートリアルの一覧](../saas-apps/tutorial-list.md)
- [ユーザー プロビジョニング用の属性マッピングのカスタマイズ](customize-application-attributes.md)
- [ユーザー プロビジョニング用のフィルターのスコープ](define-conditional-rules-for-provisioning-user-accounts.md)
