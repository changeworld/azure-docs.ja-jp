---
title: Azure AD と Silverfort で安全にハイブリッド アクセスを行う
description: このチュートリアルでは、Silverfort を Azure AD と連携させて、安全にハイブリッド アクセスを行う方法を説明します
titleSuffix: Azure AD
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 9/13/2021
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1515bfe65a9a680a9331a5a4d2b13e4d3633fda0
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2021
ms.locfileid: "129658255"
---
# <a name="tutorial-configure-silverfort-with-azure-active-directory-for-secure-hybrid-access"></a>チュートリアル: Azure Active Directory と連携するよう Silverfort を構成し、安全にハイブリッド アクセスを行う

このチュートリアルでは、Silverfort を Azure Active Directory (Azure AD) と連携させる方法を説明します。 [Silverfort](https://www.silverfort.com/) では、エージェントもプロキシも使用しない革新的なテクノロジーにより、オンプレミスおよびクラウド上のすべての資産を Azure AD に接続します。 組織でこのソリューションを利用すれば、ID の保護、可視性、ユーザー エクスペリエンスを、Azure AD 上のすべての環境に適用できます。 オンプレミスおよびクラウド環境で、リスクに基づく汎用的手法により認証操作を監視、評価し、脅威を予防できます。  

Silverfort では、あらゆる種類の資産を、まるで最新のウェブ アプリケーションであるかのように、シームレスに Azure AD に接続できます。 次に例を示します。

- レガシ アプリケーション、自社製アプリケーション

- リモート デスクトップ、Secure Shell (SSH)

- コマンドライン ツールなどによる管理者のアクセス

- ファイル共有、データベース

- インフラストラクチャ、業務用システム

**ブリッジした** これらの資産は、通常のアプリケーションと同様に Azure AD に表示され、条件付きアクセス、シングルサインオン (SSO)、多要素認証、監査などによって保護できます。

このソリューションでは、組織のすべての資産とサード パーティ製の Identity and Access Management (IAM) プラットフォームを組み合わせて使用できます。 たとえば、ハイブリッド環境とマルチクラウド環境を含めて、Active Directory、Active Directory フェデレーション サービス (ADFS)、Azure AD でのリモート認証ダイヤルイン ユーザー サービス (RADIUS) をサポートしています。

## <a name="scenario-description"></a>シナリオの説明

このガイドでは、Azure AD テナントで Silverfort Azure AD ブリッジの設定とテストを行います。

設定を終えたら、さまざまな ID から Azure AD に SSO の認証要求をブリッジする Silverfort 認証ポリシーを作成できます。 一度ブリッジしたアプリケーションは Azure AD で管理できます。

次の図で、ソリューションのコンポーネントと、Silverfort の仲立ちによって実行する認証のシーケンスを図示します。

![アーキテクチャ図を示す画像](./media/silverfort-azure-ad-integration/silverfort-architecture-diagram.png)

| 手順 | 説明|
|:---------|:------------|
| 1. | Kerberos、SAML、NTLM、OIDC、LDAP(s) などのプロトコルを使用して、ユーザーから本来の ID プロバイダー (IdP) に認証要求を送信します。|
| 2. | 認証状態が有効であるかどうかを確認するため、応答をそのまま Silverfort にルーティングします。|
| 3. | Silverfort によって、Azure AD に対する可視化、検出、ブリッジを行います。|
| 4. | アプリケーションを **ブリッジ** した状態に設定すると、認証の判断を Azure AD に委ねます。 Azure AD で条件付きアクセスのポリシーを確認して認証を承認します。|
| 5. | Silverfort により、認証状態の応答を出力して、これをそのまま IdP に送信します。 |
| 6.| IdP でリソースへのアクセスを許可または拒否します。|
| 7. | アクセス要求の許可または拒否をユーザーに通知します。 |

## <a name="prerequisites"></a>前提条件

Azure AD テナントに追加したアプリケーションで SSO を設定するには、次のものが必要です。

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成できます。

- Azure アカウントに対する次のいずれかのロール - グローバル管理者、クラウド アプリケーション管理者、アプリケーション管理者またはサービス プリンシパルの所有者。

- 事前に設定を済ませて Azure AD ギャラリーに追加した、SSO をサポートしているアプリケーション。 Azure AD ギャラリーの Silverfort アプリケーションは事前に設定を済ませてあります。 これをエンタープライズ アプリケーションとしてギャラリーから追加する必要があります。

## <a name="onboard-with-silverfort"></a>Silverfort のオンボード

テナントまたはインフラストラクチャに Silverfort をデプロイするには、[Silverfort に連絡](https://www.silverfort.com/)をします。 Silverfort Desktop アプリを必要なワークステーションにインストールします。

## <a name="configure-silverfort-and-create-a-policy"></a>Silverfort を設定してポリシーを作成する

1. ブラウザーで **Silverfort 管理者コンソール** にログインします。

2. メイン メニューで **[Settings]\(設定\)** に移動し、[General]\(一般\) セクションで **[Azure AD Bridge Connector]\(Azure AD ブリッジ コネクタ\)** までスクロールします。 テナント ID を確認し、 **[Authorize]\(認可\)** をクリックします。

   ![Azure AD ブリッジ コネクタを表示する画面の画像](./media/silverfort-azure-ad-integration/azure-ad-bridge-connector.png)

   ![登録確認画面の画像](./media/silverfort-azure-ad-integration/grant-permission.png)

3. 新しいタブで登録確認画面が表示されます。このタブを閉じます。

   ![登録完了画面の画像](./media/silverfort-azure-ad-integration/registration-completed.png)

4. **[Setting]\(設定\)** ページで、 **[Save changes]\(変更の保存\)** をクリックします

   ![Azure AD アダプターを表示する画面の画像](./media/silverfort-azure-ad-integration/silverfort-azure-ad-adapter.png)

    Azure AD コンソールにログインします。 エンタープライズ アプリケーションとして登録している **Silverfort Azure AD Adapter** アプリケーションが表示されます。

   ![エンタープライズ アプリケーションｗ表示する画面の画像](./media/silverfort-azure-ad-integration/enterprise-application.png)

5. Silvervort 管理者コンソールで **[Policies]\(ポリシー\)** ページに移動し、 **[Create Policy]\(ポリシーの作成\)** をクリックします。

6. **[New Policy]\(新しいポリシー\)** ダイアログ ボックスが表示されます。 **[Policy Name]\(ポリシー名\)** を入力します。Azure に作成する予定のアプリケーションの名前を反映したポリシー名を付けることができます。 たとえば、複数のサーバーまたはアプリケーションをこのポリシーに加える場合は、ポリシーの対象となるリソースを反映した名前を付けます。 例では、*SL-APP1* サーバーのためのポリシーを作成します。

   ![ポリシーを定義する画面の画像](./media/silverfort-azure-ad-integration/define-policy.png)

7. 適切な **[Authentication]\(認証\)** の種類と **[Protocol]\(プロトコル\)** を選択します。

8. **[Users and Groups]\(ユーザーとグループ\)** フィールドの編集アイコンをクリックし、ポリシーの対象となるユーザーを設定します。 これらのユーザーの認証は Azure AD にブリッジされます。

   ![ユーザーとグループの画面の画像](./media/silverfort-azure-ad-integration/user-groups.png)

9. ユーザー、グループまたは組織単位 (OU) を検索して選択します。

   ![ユーザーを検索する画面の画像](./media/silverfort-azure-ad-integration/search-users.png)

   選択したユーザーは [SELECTED]\(選択\) ボックスに表示されます。

   ![選択したユーザーの画面の画像](./media/silverfort-azure-ad-integration/select-user.png)

10. ポリシーを適用する **[Source]\(ソース\)** を選択します。 この例では、 *[All Devices]\(すべてのデバイス\)* を選択しています。

    ![ソースの画面の画像](./media/silverfort-azure-ad-integration/source.png)

11. **[Destination]\(宛先\)** を *SL-App1* に設定します。 必要であれば、編集ボタンをクリックして、リソースおよびリソースのグループの変更、追加ができます。

    ![宛先の画面の画像](./media/silverfort-azure-ad-integration/destination.png)

12. [Action]\(アクション\) に **[AZURE AD BRIDGE]\(Azure AD ブリッジ\)** を選択します。

    ![Azure AD ブリッジを保存する画面の画像](./media/silverfort-azure-ad-integration/save-azure-ad-bridge.png)

13. **[SAVE]\(保存\)** をクリックして新しいポリシーを保存します。 それを有効またはアクティブにするよう求められます。

    ![状態を変更する画面の画像](./media/silverfort-azure-ad-integration/change-status.png)

    ポリシーが [Policies]\(ポリシー\) ページの [Azure AD Bridge]\(Azure AD ブリッジ\) セクションに表示されます。

    ![ポリシーを追加する画面の画像](./media/silverfort-azure-ad-integration/add-policy.png)

14. Azure AD コンソールに戻り、 **[Enterprise applications]\(エンタープライズ アプリケーション\)** に移動します。 新しい Silverfort アプリケーションが表示されます。 このアプリケーションを [[CA policies]\(CA ポリシー\)](../authentication/tutorial-enable-azure-mfa.md?bc=/azure/active-directory/conditional-access/breadcrumb/toc.json&toc=/azure/active-directory/conditional-access/toc.json%23create-a-conditional-access-policy) に追加できるようになりました。

## <a name="next-steps"></a>次のステップ

- [Silverfort Azure AD adapter (Silverfort Azure AD アダプター)](https://azuremarketplace.microsoft.com/marketplace/apps/aad.silverfortazureadadapter?tab=overview)

- [Silverfort resources (Silverfort の資料、情報)](https://www.silverfort.com/resources/)
