---
title: 'ユーザーごとのライセンスのアクセス料金: リモート アプリ ストリーミング用の Azure Virtual Desktop - Azure'
description: リモート アプリ ストリーミングの Azure Virtual Desktop ライセンスに関する考慮事項を概説します。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 07/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 2fb4ec6ebe5960a6a782b3d76fe2aaec91603302
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/14/2021
ms.locfileid: "113798947"
---
# <a name="understanding-licensing-and-per-user-access-pricing"></a>ライセンスとユーザーごとのアクセス価格について

このアーティクルでは、Azure Virtual Desktop を使用して外部ユーザーにリモート アプリケーションをストリーミングするためのライセンス要件について説明します。 このアーティクルでは、外部ユーザー向け Azure Virtual Desktop のライセンスが内部ユーザー向けとどのように違うのか、ユーザーごとのアクセス料金の仕組みを詳しく説明し、Azure Virtual Desktop で使用する予定の他の製品のライセンスを取得する方法についても説明します。

## <a name="licensing-azure-virtual-desktop-for-external-users"></a>外部ユーザー向け Azure Virtual Desktop のライセンス

Azure Virtual Desktop を使用するには、2つの方法があります。 それぞれの方法には、さまざまなライセンス要件があります。

- RemoteApp とデスクトップを内部ユーザーにストリーミングします。 たとえば、製造会社 Fabrikam, Inc. は、Azure Virtual Desktop を使用して、Fabrikam の従業員に仮想ワークステーションや基幹業務アプリへのアクセスを提供する場合があります。 この場合、Fabrikam は、Azure Virtual Desktop にアクセスする各従業員について、[Azure Virtual desktop の価格](https://azure.microsoft.com/pricing/details/virtual-desktop/)に記載されている対象ライセンスのいずれかを購入する必要があります。

- Remoteapp とデスクトップを内部ユーザーにストリーミングします。 たとえば、Contoso というソフトウェア ベンダーが、Azure Virtual Desktop を使用して Contoso 社の顧客 (Contoso 社以外のユーザー) に対して Contoso の生産性アプリのリモート ストリームを提供する場合があります。 この場合、Contoso は Azure Virtual Desktop のユーザーごとのアクセス料金に登録する必要があります。 この種類のライセンスを使用すると、Contoso は、毎月 Azure Virtual Desktop にアクセスしたユーザー数に基づく Azure メーターを介して、それらのユーザーに代わって Azure Virtual Desktop のアクセス権を支払うことができます。 デプロイ内のユーザーには、Azure Virtual Desktop にアクセスするための Microsoft 365 のような別のライセンスは必要ありません。

## <a name="per-user-access-pricing-for-azure-virtual-desktop"></a>Azure Virtual Desktop のユーザーごとのアクセス価格に登録する方法

ユーザーごとのアクセス料金では、外部ユーザーに代わって Azure Virtual Desktop のアクセス権の支払いを行うことができます。 外部ユーザー向けに準拠したデプロイメントを構築するには、ユーザーごとのアクセス価格に登録する必要があります。 詳細は、[ユーザーごとのアクセス価格に登録する](per-user-access-pricing.md)を参照してください。

ユーザーごとのアクセス料金は、仮想マシン、ストレージ、その他の Azure サービスの料金に加えて、登録した Azure サブスクリプションまたはサブスクリプションを通じて支払うことになります。 各請求サイクルでは、サービスを実際に使用したユーザーに対してのみ支払いが行われます。 Azure Virtual Desktop に少なくとも 1 回接続したユーザーにのみ、その月にアクセス料金が発生します。

Azure Virtual Desktop のユーザーごとのアクセス料金には、2 つの価格階層があります。 料金は、ユーザーが接続した[アプリケーション グループ](../environment-setup.md#app-groups)の種類に応じて、課金サイクルごとに自動的に決定されます。

- 最初の価格階層は「アプリ」と呼ばれます。 この定額料金は、少なくとも 1 つの RemoteApp アプリケーション グループと 0 つのデスクトップ アプリケーション グループにアクセスする各ユーザーに対して請求されます。
- 2番目の階層は「アプリケーション + デスクトップ」です。 この定額料金は、少なくとも 1 つのデスクトップ アプリケーション グループにアクセスするユーザーごとに課金されます。
- ユーザーがアプリケーション グループにアクセスしない場合、料金は発生しません。

価格の詳細については、[Azure Virtual Desktop の価格](https://azure.microsoft.com/pricing/details/virtual-desktop/)を参照してください。

各価格階層では、ユーザーごとに定額のアクセス料金が設定されています。 たとえばユーザーは、請求サイクル中にサービスを使用した時間や数時間にかかわらず、サブスクリプションに対して同じ料金が発生します。

また、Azure Virtual Desktop では、Azure Virtual Desktop へのアクセス権を持つユーザーに、別途割り当てられたライセンスを請求します。 対象となるライセンスを購入している社内ユーザーがいる場合は、ユーザーごとのアクセス価格が適用されない別のサブスクリプションを通じてAzure Virtual Desktop へのアクセスを提供し、実質的な二重支払いを回避することをお勧めします。

Azure Virtual Desktop では、特定の請求期間に、特定のユーザーに対して最大で 1 つのアクセス料金が発行されます。 そのため、同じテナント内の 2 つの異なる Azure サブスクリプションにまたがる Azure Virtual Desktop リソースへのアクセスをユーザー Alice にデプロイで許可した場合、Alice がアクセスした最初のサブスクリプションのみに使用料が発生します。

## <a name="comparing-licensing-options"></a>ライセンスオプションの比較

[Azure Virtual Desktop のユーザーごとのアクセス料金](#per-user-access-pricing-for-azure-virtual-desktop)で説明したように、Azure Virtual Desktop では次の 2 種類のライセンスを選択できます。

- Windows または Microsoft 365 ライセンス:
   - 内部ユーザーのみに Azure Virtual Desktop アクセス権を付与します。
   - サブスクリプションにより前払いされます。
   - ユーザーの行動に関係なく、ユーザーごとに毎月同じコスト
   - 他の Microsoft 製品およびサービスに対する権利を含む

- ユーザーごとのアクセス料金:
   - 内部ユーザーのみに Azure Virtual Desktop アクセス権を付与
   - Azure メーターを使用した従量課金制
   - ユーザーの行動に基づく毎月のユーザーあたりの動的コスト
   - Azure Virtual Desktop へのアクセス権のみを含む

## <a name="licensing-other-products-and-services-for-use-with-azure-virtual-desktop"></a>Azure Virtual Desktop で使用する他の製品とサービスのライセンス

Azure Virtual Desktop のユーザーごとのアクセスライセンスは、Windows または Microsoft 365 ライセンスに対して完全に置き換えることはできません。 ユーザーごとのライセンスは、Azure Virtual Desktop へのアクセス権のみを付与し、Microsoft Office、Microsoft Defender、Universal Print は含まれません。 つまり、ユーザーごとのライセンスを選択した場合は、他の製品とサービスに個別にライセンスを付与し、Azure Virtual Desktop 環境でユーザーにアクセス権を付与する必要があります。

## <a name="next-steps"></a>次のステップ

ライセンスの価格オプションについて理解した後は、Azure Virtual Desktop 環境の計画を始めることができます。 参考になるアーティクルを紹介します。

- [ユーザーごとのアクセス価格でサブスクリプションを登録する](per-user-access-pricing.md)
- [Azure Virtual Desktop のユーザーごとのアプリ ストリーミング料金の見積もり](streaming-costs.md)
- 最初のデプロイの設定を始める準備ができたら、[チュートリアル](../create-host-pools-azure-marketplace.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)を開始してください。
