---
title: Microsoft Defender for Key Vault - 利点と機能
description: Microsoft Defender for Key Vault の利点と機能について説明します。
author: memildin
ms.author: memildin
ms.date: 11/09/2021
ms.topic: overview
ms.service: defender-for-cloud
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: 2c77fa861a27cd0277ea501ae68f5d35e0cd59bf
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132525617"
---
# <a name="introduction-to-microsoft-defender-for-key-vault"></a>Microsoft Defender for Key Vault の概要

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Azure Key Vault は、暗号化キーとシークレット (証明書、接続文字列、パスワードなど) を保護するクラウド サービスです。 

**Microsoft Defender for Key Vault** を有効にして、Azure Key Vault の Azure ネイティブで高度な脅威の防止を使用することにより、セキュリティ インテリジェンスの層を追加します。 

## <a name="availability"></a>可用性

|側面|詳細|
|----|:----|
|リリース状態:|一般公開 (GA)|
|価格:|**Microsoft Defender for Key Vault** の課金については、[価格に関するページ](https://azure.microsoft.com/pricing/details/security-center/)をご覧ください。|
|クラウド:|:::image type="icon" source="./media/icons/yes-icon.png"::: 商用クラウド<br>:::image type="icon" source="./media/icons/no-icon.png"::: 各国 (Azure Government、Azure China 21Vianet)|
|||

## <a name="what-are-the-benefits-of-microsoft-defender-for-key-vault"></a>Microsoft Defender for Key Vault の利点

Microsoft Defender for Key Vault によって、異常であり、害を及ぼす可能性のある、Key Vault アカウントに対するアクセスまたは悪用の試みが検出されます。 この保護層により、セキュリティの専門家でなくても、サードパーティ製のセキュリティ監視システムを管理することなく、脅威に対処できます。

異常なアクティビティが発生した場合、Defender for Key Vault によってアラートが表示され、必要に応じて組織の関連するメンバーにメールが送信されます。 これらのアラートには、不審なアクティビティの詳細と、脅威の調査や修復方法に関する推奨事項が含まれます。 

## <a name="microsoft-defender-for-key-vault-alerts"></a>Microsoft Defender for Key Vault のアラート
Microsoft Defender for Key Vault からアラートを受け取った場合は、[Microsoft Defender for Key Vault への応答](defender-for-key-vault-usage.md)に関するページの説明に従って調査し、アラートに対応することをお勧めします。 Microsoft Defender for Key Vault によってアプリケーションと資格情報が保護されているため、アラートをトリガーしたアプリケーションまたはユーザーを熟知している場合でも、あらゆるアラートを取り巻く状況を確認することが重要です。

アラートは Key Vault の **[セキュリティ]** ページ、ワークロード保護、Defender for Cloud のアラート ページに表示されます。

:::image type="content" source="./media/defender-for-key-vault-intro/key-vault-security-page.png" alt-text="Azure Key Vault のセキュリティ ページ":::


> [!TIP]
> 「[Microsoft Defender for Cloud での Azure Key Vault 脅威検出の検証](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336)」の手順に従って、Microsoft Defender for Key Vault のアラートをシミュレートできます。


## <a name="respond-to-microsoft-defender-for-key-vault-alerts"></a>Microsoft Defender for Key Vault のアラートに対応する
[Microsoft Defender for Key Vault](defender-for-key-vault-introduction.md) からアラートを受け取った場合は、下の説明に従って調査し、アラートに対応することをお勧めします。 Microsoft Defender for Key Vault によってアプリケーションと資格情報が保護されているため、アラートをトリガーしたアプリケーションまたはユーザーを熟知している場合でも、すべてのアラートを取り巻く状況を確認することが重要です。  

Azure Defender for Key Vault のアラートには、これらの要素が含まれています。

- オブジェクト ID
- 疑わしいリソースのユーザー プリンシパル名または IP アドレス 

発生したアクセスの "*種類*" によっては、一部のフィールドが使用できない場合があります。 たとえば、キー コンテナーがアプリケーションからアクセスされた場合、関連付けられているユーザー プリンシパル名は表示されません。 トラフィックが Azure の外部から送信された場合、オブジェクト ID は表示されません。

> [!TIP]
> Azure 仮想マシンには、Microsoft IP が割り当てられています。 つまり、Microsoft の外部から実行されたアクティビティに関連しているアラートにも、Microsoft IP が含まれることがあります。 したがって、アラートに Microsoft IP が含まれていても、このページで説明されているとおりに、調査を行う必要があります。

### <a name="step-1-identify-the-source"></a>手順 1. ソースを特定する

1. トラフィックがご自分の Azure テナント内から送信されたかどうかを確認します。 キー コンテナーのファイアウォールが有効になっている場合、このアラートをトリガーしたユーザーまたはアプリケーションへのアクセス権を提供している可能性があります。
1. トラフィックのソースを確認できない場合は、「[手順 2. 適宜対応する](#step-2-respond-accordingly)」に進みます。
1. ご自分のテナント内でトラフィックの送信元を特定できる場合は、そのユーザーまたはアプリケーションの所有者に問い合わせてください。 

> [!CAUTION]
> Microsoft Defender for Key Vault は、盗まれた資格情報によって引き起こされた不審なアクティビティを特定できるように設計されています。 ユーザーまたはアプリケーションを認識しているという理由だけで、アラートを無視 **しないでください**。 アプリケーションの所有者またはユーザーに連絡し、アクティビティが正当なものであったことを確認します。 必要に応じて、ノイズを除去する抑制ルールを作成できます。 詳細については、[セキュリティ アラートの抑制](alerts-suppression-rules.md)に関するページを参照してください。


### <a name="step-2-respond-accordingly"></a>手順 2. 適宜対応する 
ユーザーまたはアプリケーションを認識できない場合、またはアクセスが許可されるべきではなかったと考える場合は、次のようにします。

- トラフィックが、認識されていない IP アドレスから送信された場合:
    1. 「[Azure Key Vault のファイアウォールと仮想ネットワークを構成する](../key-vault/general/network-security.md)」の説明に従って Azure Key Vault のファイアウォールを有効にします。
    1. 信頼できるリソースと仮想ネットワークを使用してファイアウォールを構成します。

- アラートの発生元が、未承認のアプリケーションまたは疑わしいユーザーだった場合:
    1. キー コンテナーのアクセス ポリシー設定を開きます。
    1. 該当するセキュリティ プリンシパルを削除するか、セキュリティ プリンシパルで実行できる操作を制限します。  

- テナント内のアラートの発生元に Azure Active Directory ロールが設定されている場合:
    1. 管理者に問い合わせてください。
    1. Azure Active Directory のアクセス許可を減らす、または取り消す必要があるかどうかを判断します。

### <a name="step-3-measure-the-impact"></a>手順 3. 影響を測定する
イベントが軽減されたら、影響を受けたキー コンテナーのシークレットを調査します。
1. ご自身の Azure キー コンテナーの **[セキュリティ]** ページを開き、トリガーされたアラートを表示します。
1. トリガーされた特定のアラートを選択し、アクセスされたシークレットとタイムスタンプの一覧を確認します。
1. 必要に応じて、キー コンテナー診断ログを有効にしている場合は、該当する呼び出し元 IP、ユーザー プリンシパル、またはオブジェクト ID に対して行われた操作を確認します。  

### <a name="step-4-take-action"></a>手順 4. アクションの実行 
疑わしいユーザーまたはアプリケーションによってアクセスされたシークレット、キー、証明書の一覧をまとめたら、それらのオブジェクトをすぐにローテーションする必要があります。

1. 影響を受けたシークレットは、無効にするか、キー コンテナーから削除する必要があります。
1. 特定のアプリケーションに対して資格情報が使用された場合は、次のようにします。
    1. アプリケーションの管理者に連絡し、侵害された資格情報が、侵害後に使用されたかどうかについて担当の環境を監査するように依頼します。
    1. 侵害された資格情報が使用された場合は、アプリケーションの所有者が、アクセスされた情報を特定し、影響を軽減する必要があります。

## <a name="next-steps"></a>次の手順

この記事では、Microsoft Defender for Key Vault について説明しました。

関連資料については、次の記事をご覧ください。 

- [Key Vault セキュリティ アラート](alerts-reference.md#alerts-azurekv)--すべての Microsoft Defender for Cloud アラートの参照テーブルの Key Vault セクション
- [Defender for Cloud のデータを継続的にエクスポートする](continuous-export.md)
- [セキュリティ アラートを抑制する](alerts-suppression-rules.md)
