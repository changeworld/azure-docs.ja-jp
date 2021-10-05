---
title: Azure Defender for Key Vault アラートに対応する方法
description: Azure Defender for Key Vault のアラートに対応するために必要な手順について説明します。
author: memildin
ms.author: memildin
ms.date: 09/13/2021
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 45ed38f3325ae66c72fff7f0aec35347b5e28ace
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128664981"
---
# <a name="respond-to-azure-defender-for-key-vault-alerts"></a>Azure Defender の Key Vault アラートに対応する
[Azure Defender for Key Vault](defender-for-key-vault-introduction.md) からアラートを受け取った場合は、以下の説明に従って調査し、アラートに対応することをお勧めします。 Azure Defender for Key Vault によってアプリケーションと資格情報が保護されているため、アラートをトリガーしたアプリケーションまたはユーザーを熟知している場合でも、すべてのアラートを取り巻く状況を確認することが重要です。  

Azure Defender for Key Vault のアラートには、次の要素が含まれています。

- オブジェクト ID
- 疑わしいリソースのユーザー プリンシパル名または IP アドレス 

発生したアクセスの "*種類*" によっては、一部のフィールドが使用できない場合があります。 たとえば、キー コンテナーがアプリケーションからアクセスされた場合、関連付けられているユーザー プリンシパル名は表示されません。 トラフィックが Azure の外部から送信された場合、オブジェクト ID は表示されません。

> [!TIP]
> Azure 仮想マシンには、Microsoft IP が割り当てられています。 つまり、Microsoft の外部から実行されたアクティビティに関連しているアラートにも、Microsoft IP が含まれることがあります。 したがって、アラートに Microsoft IP が含まれていても、このページで説明されているとおりに、調査を行う必要があります。

## <a name="step-1-identify-the-source"></a>手順 1. ソースを特定する

1. トラフィックがご自分の Azure テナント内から送信されたかどうかを確認します。 キー コンテナーのファイアウォールが有効になっている場合、このアラートをトリガーしたユーザーまたはアプリケーションへのアクセス権を提供している可能性があります。
1. トラフィックのソースを確認できない場合は、「[手順 2. 適宜対応する](#step-2-respond-accordingly)」に進みます。
1. ご自分のテナント内でトラフィックの送信元を特定できる場合は、そのユーザーまたはアプリケーションの所有者に問い合わせてください。 

> [!CAUTION]
> Azure Defender for Key Vault は、盗まれた資格情報によって引き起こされた疑わしいアクティビティを特定できるように設計されています。 ユーザーまたはアプリケーションを認識しているという理由だけで、アラートを無視 **しないでください**。 アプリケーションの所有者またはユーザーに連絡し、アクティビティが正当なものであったことを確認します。 必要に応じて、ノイズを除去する抑制ルールを作成できます。 詳細については、[Azure Defender のアラートの抑制](alerts-suppression-rules.md)に関するページを参照してください。


## <a name="step-2-respond-accordingly"></a>手順 2. 適宜対応する 
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

## <a name="step-3-measure-the-impact"></a>手順 3. 影響を測定する
イベントが軽減されたら、影響を受けたキー コンテナーのシークレットを調査します。
1. ご自身の Azure キー コンテナーの **[セキュリティ]** ページを開き、トリガーされたアラートを表示します。
1. トリガーされた特定のアラートを選択します。
    アクセスされたシークレットとタイムスタンプの一覧を確認します。
1. 必要に応じて、キー コンテナー診断ログを有効にしている場合は、該当する呼び出し元 IP、ユーザー プリンシパル、またはオブジェクト ID に対して行われた操作を確認します。  

## <a name="step-4-take-action"></a>手順 4. アクションの実行 
疑わしいユーザーまたはアプリケーションによってアクセスされたシークレット、キー、証明書の一覧をまとめたら、それらのオブジェクトをすぐにローテーションする必要があります。

1. 影響を受けたシークレットは、無効にするか、キー コンテナーから削除する必要があります。
1. 特定のアプリケーションに対して資格情報が使用された場合は、次のようにします。
    1. アプリケーションの管理者に連絡し、侵害された資格情報が、侵害後に使用されたかどうかについて担当の環境を監査するように依頼します。
    1. 侵害された資格情報が使用された場合は、アプリケーションの所有者が、アクセスされた情報を特定し、影響を軽減する必要があります。


## <a name="next-steps"></a>次の手順

このページでは、Azure Defender for Key Vault のアラートに対応するプロセスについて説明しました。 関連情報については、次のページを参照してください。

- [Azure Defender for Key Vault の概要](defender-for-key-vault-introduction.md)
- [Azure Defender のアラートの抑制](alerts-suppression-rules.md)
- [継続的に Security Center データをエクスポートする](continuous-export.md)