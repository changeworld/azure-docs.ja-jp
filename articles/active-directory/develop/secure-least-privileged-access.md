---
title: Azure AD での最小特権アクセスに関するベスト プラクティス - Microsoft ID プラットフォーム
description: 最小特権に関する一連のベスト プラクティスと一般的なガイダンスについて学習します。
services: active-directory
author: Chrispine-Chiedo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/26/2021
ms.custom: template-concept
ms.author: cchiedo
ms.reviewer: yuhko, saumadan, marsma
ms.openlocfilehash: db2eb2dcda894b997f485795ab62d983966a7f2f
ms.sourcegitcommit: eb20dcc97827ef255cb4ab2131a39b8cebe21258
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/03/2021
ms.locfileid: "111372781"
---
# <a name="best-practices-for-least-privileged-access-for-applications"></a>アプリケーションの最小特権アクセスに関するベスト プラクティス

最小特権の原則は情報セキュリティの概念であり、必要なタスクを実行するために必要な最小レベルのアクセス権をユーザーとアプリケーションに付与する必要があるという考えを強制するものです。 最小特権の原則を理解すると、顧客向けに信頼できるアプリケーションをビルドするのに役立ちます。

最小特権の導入は、単なる適切なセキュリティ プラクティスではありません。 この概念は、データの整合性とセキュリティを維持するのに役立ちます。 また、データのプライバシーを保護し、アプリケーションが必要以上にデータにアクセスするのを防ぐことでリスクを軽減します。 より広いレベルで見れば、最小特権原則の導入は、組織が[ゼロ トラスト](https://www.microsoft.com/security/business/zero-trust)によるプロアクティブなセキュリティを採用できる方法の 1 つです。

この記事では、エンド ユーザーのアプリケーションのセキュリティを強化するために最小特権原則を導入する際に使用できる一連のベスト プラクティスについて説明します。 最小特権の次の側面を理解できます。
- アクセス許可での同意のしくみ
- アプリに過剰な特権または最小特権が与えられる場合に、それが何を意味するか
- 開発者として最小特権にアプローチする方法
- 組織として最小特権にアプローチする方法

## <a name="using-consent-to-control-access-permissions-to-data"></a>同意を使用してデータへのアクセス許可を制御する

保護されたデータにアクセスするには、エンド ユーザーからの[同意](../develop/application-consent-experience.md#consent-and-permissions)が必要です。 ユーザーのデバイスで実行されるアプリケーションでは、保護されたデータへのアクセスを要求するたびに、その保護されたデータへのアクセスを許可する前にユーザーの同意を求める必要があります。 エンド ユーザーは、アプリケーションが先に進む前に、要求されたアクセス許可に対する同意を与える (または拒否する) 必要があります。 アプリケーション開発者は、最小特権のアクセス許可を要求することをお勧めします。

:::image type="content" source="./media/least-privilege-best-practice/api-permissions.png" alt-text="アプリ登録の [API のアクセス許可] ペインを示す Azure portal のスクリーンショット。":::

## <a name="overprivileged-and-least-privileged-applications"></a>過剰な特権および最小特権が与えられているアプリケーション

過剰な特権が与えられているアプリケーションには、次のいずれかの特性がある場合があります。
- **未使用のアクセス許可**: アプリケーションで付与されているすべてのアクセス許可を利用する API 呼び出しを実行できない場合、アクセスが未使用となる可能性があります。 たとえば、[MS Graph](/graph/overview) では、アプリで (*Files.Read.All* アクセス許可を使用して) OneDrive ファイルを読み取るだけでも、''Calendar API と統合されていないにもかかわらず、*Calendars.Read* アクセス許可も付与されている場合があります。
- **縮小可能なアクセス許可**: 付与されたアクセス許可に、必要な API 呼び出しを完了できるより低い特権を持つ代わりのものがある場合、アプリには縮小可能なアクセス許可があります。 たとえば、ユーザー プロファイルを読み取るだけのアプリにも、*User.ReadWrite.All* が付与されている場合は、過剰な特権が与えられていると見なされることがあります。 この場合、アプリには代わりに *User.Read.All* が付与される必要があります。これは、要求を満たすために必要な最小特権アクセス許可です。

アプリケーションを最小特権があるものと見なすには、以下が必要です。
- **十分なアクセス許可のみ**: 必要なタスクを実行するためにアプリケーション、サービス、またはシステムのエンド ユーザーに必要な最小限のアクセス許可セットのみを付与します。

## <a name="approaching-least-privilege-as-an-application-developer"></a>アプリケーション開発者としての最小特権へのアプローチ

開発者として、顧客のデータのセキュリティに貢献する責任があります。 アプリケーションを開発する場合は、最小特権の原則を導入する必要があります。 これらの手順に従って、アプリケーションに過剰な特権が与えられないようにすることをお勧めします。
- アプリケーションで行う必要がある API 呼び出しに必要なアクセス許可について完全に理解する
- [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) を使用して、アプリで行う必要がある各 API 呼び出しの最小特権のアクセス許可について理解する
- 最小から最大特権までの対応する[アクセス許可](/graph/permissions-reference)を見つける
- アプリが重複するアクセス許可を持つ API 呼び出しを行う場合に、重複するアクセス許可のセットを削除する
- アクセス許可の一覧で最小特権のアクセス許可を選択することで、最小特権のアクセス許可セットのみをアプリケーションに適用する

## <a name="approaching-least-privilege-as-an-organization"></a>組織としての最小特権へのアプローチ

組織は多くの場合、ビジネス運用に影響を与える可能性があるため、既存のアプリケーションを変更することをためらいますが、既に付与されているアクセス許可に過剰な特権があり、取り消す必要がある場合は課題が生じます。 組織として、アクセス許可を定期的に確認およびレビューすることをお勧めします。 これらの手順に従って、アプリケーションを正常な状態に保つことをお勧めします。
- アプリケーションから行われた API 呼び出しを評価する
- [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) および [Microsoft Graph](/graph/overview) ドキュメントを使用して、必須および最小特権アクセス許可について確認する
- ユーザーまたはアプリケーションに付与される特権を監査する
- 最小特権のアクセス許可セットでアプリケーションを更新する
- アクセス許可レビューを定期的に実施して、すべての承認されたアクセス許可が引き続き関連していることを確認する

## <a name="next-steps"></a>次のステップ

- Azure Active Directory での同意とアクセス許可の詳細については、「[Azure AD アプリケーションの同意エクスペリエンスについて](../develop/application-consent-experience.md)」を参照してください。
- Microsoft ID でのアクセス許可と同意の詳細については、「[Microsoft ID プラットフォームでのアクセス許可と同意](../develop/v2-permissions-and-consent.md)」を参照してください。
- ゼロ トラストの詳細については、[ゼロ トラスト展開センター](/security/zero-trust/)に関するページを参照してください。
