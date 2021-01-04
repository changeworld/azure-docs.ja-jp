---
title: Azure Active Directory B2C と Zscaler を構成するためのチュートリアル
titleSuffix: Azure AD B2C
description: Azure AD B2C 認証を Zscaler と統合する方法について説明します
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/09/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: ff51c2a71dfcaec580733a92e265628ac816e229
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2020
ms.locfileid: "97095845"
---
# <a name="tutorial-to-configure-zscaler-private-access-with-azure-active-directory-b2c-for-secure-hybrid-access"></a>セキュリティで保護されたハイブリッド アクセスのために Azure Active Directory B2C で Zscaler Private Access を構成するためのチュートリアル

このチュートリアルでは、Azure AD B2C 認証を [Zscaler Private Access (ZPA)](https://www.zscaler.com/products/zscaler-private-access) と統合する方法について説明します。 ZPA は、仮想プライベート ネットワーク (VPN) のコスト、手間、またはセキュリティ上のリスクが生じることなく、非公開のアプリケーションおよび資産へのセキュリティで保護された、ポリシーベースのアクセスを実現します。 Zscaler のセキュリティで保護されたハイブリッド アクセス オファリングを Azure AD B2C と組み合わせて使用すると、コンシューマー向けアプリケーションの攻撃対象領域をなくすことができます。

## <a name="prerequisites"></a>前提条件

作業を開始するには、以下が必要です。

- Azure サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。

- お使いの Azure サブスクリプションにリンクされている [Azure AD B2C テナント](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant)。

- [ZPA サブスクリプション](https://azuremarketplace.microsoft.com/marketplace/apps/aad.zscalerprivateaccess?tab=Overview)

## <a name="scenario-description"></a>シナリオの説明

ZPA 統合には、次のコンポーネントが含まれています。

- Azure AD B2C – ユーザーの資格情報の検証を受け持つ ID プロバイダー (IdP)。 また、新しいユーザーのサインアップも受け持ちます。

- ZPA - [ゼロトラスト アクセス](https://www.microsoft.com/security/blog/2018/12/17/zero-trust-part-1-identity-and-access-management/#:~:text=Azure%20Active%20Directory%20%28Azure%20AD%29%20provides%20the%20strong%2C,to%20express%20their%20access%20requirements%20in%20simple%20terms.)を適用することによって Web アプリケーションのセキュリティ保護を受け持つサービス。

- Web アプリケーション - ユーザーがアクセスしようとしているサービスをホストします。

次の図は、ZPA を Azure AD B2C と統合する方法を示しています。

![Zscaler アーキテクチャ図を示す画像](media/partner-zscaler/zscaler-architecture-diagram.png)

|手順 | 説明 |
|:-----| :-----------|
| 1. | ユーザーが、ZPA User Portal またはZPA Browser Access アプリケーションにアクセスします。
| 2. | ZPA は、ユーザーが Web アプリケーションにアクセスすることを許可するかどうかを決定する前に、ユーザー コンテキスト情報を必要とします。 ユーザーを認証するために、ZPA は Azure AD B2C ログイン ページへの SAML リダイレクトを実行します。  
| 3. | ユーザーが、Azure AB B2C ログイン ページにたどり着きます。 新しいユーザーである場合、ユーザーはサインアップして新しいアカウントを作成します。 既存のユーザーは、既存の資格情報を使用してサインインします。 Azure AD B2C により、ユーザーの ID が検証されます。
| 4. | 認証が成功すると、Azure AD B2C によってユーザーは SAML アサーションと共に ZPA にリダイレクトされます。 ZPA で SAML アサーションが検証され、ユーザー コンテキストが設定されます。
| 5. | ZPA で、ユーザーのアクセス ポリシーが評価されます。 ユーザーが Web アプリケーションへのアクセスを許可されている場合、接続は通過できます。

## <a name="onboard-to-zpa"></a>ZPA にオンボードする

このチュートリアルでは、ZPA の動作セットアップが既に行われていることを前提としています。 ZPA の使用を開始する場合は、「[ZPA の詳細な構成ガイド](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa)」を参照してください。

## <a name="integrate-with-azure-ad-b2c"></a>Azure AD B2C との統合

### <a name="part-1---configure-azure-ad-b2c-as-an-idp-on-zpa"></a>パート 1 - ZPA で IdP として Azure AD B2C を構成する

[ZPA での ID プロバイダー (IdP)](https://help.zscaler.com/zpa/configuring-idp-single-sign) として Azure AD B2C を構成するには、次のようにします。

1. [ZPA Admin Portal](https://admin.private.zscaler.com) にログインします

2. **[Administration]\(管理\)**  >  **[IdP Configuration]\(IdP 構成\)** に移動します

3. **[Add IdP Configuration]\(IdP 構成の追加\)** を選択します

4. **[1 IdP Information]\(1 IdP 情報\)** で、以下を入力します。

   a. **[名前]** : Azure AD B2C

   b. **[Single Sign-On]\(シングル サインオン\)** :ユーザーの選択

   c. **[Domains]\(ドメイン\)** :この IdP に関連付ける認証ドメインを選択し、 **[Done]\(完了\)** を選択します

   ![IdP 情報を示す画像](media/partner-zscaler/add-idp-configuration.png)

5. **[次へ]** を選択します

6. **[2 SP Metadata]\(2 SP メタデータ\)** で、以下を行います

   a. サービス プロバイダー URL をコピーし、後で使用するためにメモしておきます。

   b. サービス プロバイダーのエンティティ ID をコピーし、後で使用するためにメモしておきます。

   ![SP メタデータ情報を示す画像](media/partner-zscaler/sp-metadata.png)

7. **[Pause]\(一時停止\)** を選択します

IdP 構成の残りの部分は、Azure AD B2C の構成後に再開します。

### <a name="part-2---configure-custom-policy-in-azure-ad-b2c"></a>パート 2 - Azure AD B2C でカスタム ポリシーを構成する

>[!Note]
>この手順は、カスタム ポリシーをまだ設定していない場合にのみ必要です。 1 つ以上のカスタム ポリシーが既にある場合は、この手順を省略できます。

記事「[Azure Active Directory B2C でのカスタム ポリシーの概要](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started)」に、Azure AD B2C テナントでカスタム ポリシーを構成する方法の手順が記載されています。

### <a name="part-3---register-zpa-as-a-saml-application-in-azure-ad-b2c"></a>パート 3 - ZPA を SAML アプリケーションとして Azure AD B2C に登録する

記事「[SAML アプリケーションを Azure AD B2C に登録する](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers)」に、Azure AD B2C で SAML アプリケーションを構成する手順が記載されています。 [手順 3.2](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers#32-upload-and-test-your-policy-metadata) で、Azure AD B2C によって使用される IdP SAML メタデータ URL が得られます。 これは後で必要になります。

[手順 4.2](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers#42-update-the-app-manifest) に記載されている手順に従います。 手順 4.2 では、アプリ マニフェストを更新する必要があります。 次のようにプロパティを更新します。

- **identifierUris**:**パート 1 の手順 6a** でメモしたサービス プロバイダーのエンティティ ID を使用します。

- **samlMetadataUrl**:ZPA が SAML メタデータ URL をホストしないため、このプロパティはスキップします。

- **replyUrlsWithType**:**パート 1 の手順 6b** でメモしたサービス プロバイダー URL を使用します。

- **logoutUrl**:ZPA ではログアウト URL がサポートされていないため、このプロパティはスキップします。

残りの手順は、このチュートリアルには関係ありません。

### <a name="part-4---extract-the-idp-saml-metadata-from-azure-ad-b2c"></a>パート 4 - Azure AD B2C から IdP SAML メタデータを抽出する

前の手順から、次の形式で SAML メタデータ URL を取得する必要があります。

```https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/Samlp/metadata```

ここで `<tenant-name>` は Azure AD B2C テナントの名前、`<policy-name>` は最後の手順で作成したカスタム SAML ポリシーの名前です。

たとえば、https://safemarch.b2clogin.com/safemarch.onmicrosoft.com/B2C_1A_signup_signin_saml//Samlp/metadata のように指定します。

Web ブラウザーを開き、SAML メタデータ URL に移動します。 ページが読み込まれたら、ページの任意の場所を右クリックします。 **[Save Page As]\(ページに名前を付けて保存\)** を選択し、お使いのコンピューターにファイルを保存します。これは次のパートで使用します。

### <a name="part-5---complete-idp-configuration-on-zpa"></a>パート 5 - ZPA で IdP 構成を完了する

[ZPA Admin Portal での IdP の構成](https://help.zscaler.com/zpa/configuring-idp-single-sign)を完了します。パート 1 で一部の構成を行いました。

1. [ZPA Admin Portal](https://admin.private.zscaler.com) にログインします

2. **[Administration]\(管理\)**  >  **[IdP Configuration]\(IdP 構成\)** に移動します。

3. パート 1 で構成した IdP を選択し、 **[Resume]\(再開\)** を選択します。

4. **[3 Create IdP]\(3 IdP の作成\)** で、以下を行います

   a. **[IdP Metadata File]\(IdP メタデータ ファイル\)**  >  **[Select File]\(ファイルの選択\)** にアクセスし、パート 4 で保存したメタデータ ファイルをアップロードします。

   b. IdP 構成の **[Status]\(状態\)** が **[Enabled]\(有効\)** になっていることを確認します。

   c. **[保存]** を選択します。

      ![IdP の作成の情報を示す画像](media/partner-zscaler/create-idp.png)

## <a name="test-the-solution"></a>ソリューションをテストする

ZPA User Portal または Browser Access アプリケーションにアクセスし、サインアップまたはサインインのプロセスをテストします。 これで SAML 認証が成功するはずです。

## <a name="next-steps"></a>次のステップ

追加情報については、次の記事を参照してください。

- [Azure Active Directory B2C でのカスタム ポリシーの概要](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started)

- [SAML アプリケーションを Azure AD B2C に登録する](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers)

- [ZPA の詳細な構成ガイド](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa)

- [シングル サインオンのための IdP の構成](https://help.zscaler.com/zpa/configuring-idp-single-sign)
