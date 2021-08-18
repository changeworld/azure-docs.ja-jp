---
title: Microsoft 365 Defender と Azure Sentinel の統合 |Microsoft Docs
description: Microsoft 365 Defender を Azure Sentinel と共に使用することで、Azure Sentinel をユニバーサル インシデント キューとして使用しながら、Microsoft 365 セキュリティ インシデントの調査に役立つ Microsoft 365 Defender の強みをシームレスに適用する方法について説明します。 また、Defender コンポーネントの高度な検索データを Azure Sentinel に取り込む方法についても説明します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/21/2021
ms.author: yelevin
ms.openlocfilehash: 86a998e304755085a9ae1e15e011df3b242c4df8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121725152"
---
# <a name="microsoft-365-defender-integration-with-azure-sentinel"></a>Microsoft 365 Defender と Azure Sentinel の統合

> [!IMPORTANT]
> 現在、Microsoft 365 Defender コネクタは **プレビュー** の段階にあります。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

> [!IMPORTANT]
>
> **Microsoft 365 Defender** は、以前は **Microsoft Threat Protection** または **MTP** と呼ばれていました。
>
> **Microsoft Defender for Endpoint** は、以前は **Microsoft Defender Advanced Threat Protection** または **MDATP** と呼ばれていました。
>
> 以前の名前がしばらくの間使用されることがあります。

## <a name="incident-integration"></a>インシデント統合

Azure Sentinel の [Microsoft 365 Defender ](/microsoft-365/security/mtp/microsoft-threat-protection) インシデント統合により、すべての Microsoft 365 Defender インシデントを Azure Sentinel にストリーミングし、両方のポータル間で同期を保つことができます。 Microsoft 365 Defender (旧称 Microsoft Threat Protection または MTP) のインシデントには、関連するすべてのアラート、エンティティ、関連情報が含まれており、Azure Sentinel でトリアージと事前調査を実行するのに十分なコンテキストが提供されます。 Sentinel に入ると、インシデントは Microsoft 365 Defender と双方向で同期されたままになるため、インシデント調査で両方のポータルの利点を活用できます。

この統合により、Microsoft 365 セキュリティ インシデントは、組織全体の主要なインシデント キューの一部として、Azure Sentinel 内から管理できるようになります。これにより、Microsoft 365 インシデントを、他のすべてのクラウドおよびオンプレミス システムのものと合わせて確認し、関連付けることができます。 同時に、Microsoft 365 Defender の独自の強みと機能を活用して、詳細な調査や、Microsoft 365 エコシステム全体での Microsoft 365 固有のエクスペリエンスを実現できます。 Microsoft 365 Defender は、複数の Microsoft 365 製品からのアラートを強化してグループ化します。これにより、SOC のインシデント キューのサイズが縮小され、解決にかかる時間が短縮されます。 Microsoft 365 Defender スタックの一部であるコンポーネント サービスは次のとおりです。

- **Microsoft Defender for Endpoint** (以前の Microsoft Defender ATP)
- **Microsoft Defender for Identity** (旧称 Azure ATP)
- **Microsoft Defender for Office 365** (旧称 Office 365 ATP)
- **Microsoft Cloud App Security**

これらのコンポーネントからアラートを収集するだけでなく、Microsoft 365 Defender によって独自のアラートが生成されます。 これらのすべてのアラートからインシデントを作成し、Azure Sentinel に送信します。

### <a name="common-use-cases-and-scenarios"></a>一般的なユース ケースとシナリオ

- 1 回のクリックで、Microsoft 365 Defender インシデント (Microsoft 365 Defender コンポーネントのすべてのアラートとエンティティを含む) を Azure Sentinel に接続します。

- 状態、所有者、終了の理由について、Sentinel と Microsoft 365 Defender インシデント間の双方向同期を行います。

- Azure Sentinel で Microsoft 365 Defender アラートのグループ化と強化の機能を適用して、解決までの時間を短縮します。

- Azure Sentinel インシデントとその並行 Microsoft 365 Defender インシデント間のコンテキスト内ディープ リンクにより、両方のポータルでの調査を容易にします。

### <a name="connecting-to-microsoft-365-defender"></a>Microsoft 365 Defender に接続する

Microsoft 365 Defender データ コネクタを有効にして [インシデントとアラートを収集](connect-microsoft-365-defender.md)すると、Microsoft 365 Defender インシデントは、Microsoft 365 Defender で生成されるとすぐに、 **[Product name]\(製品名\)** フィールドに **Microsoft 365 Defender** と共に Azure Sentinel インシデント キューに表示されます。
- Microsoft 365 Defender でインシデントが生成されてから Azure Sentinel に表示されるまで、最大で10 分かかることがあります。

- インシデントは、追加コストなしで取り込まれて同期されます。

Microsoft 365 Defender 統合が接続されると、すべてのコンポーネント アラート コネクタ (Defender for Endpoint、Defender for Identity、Defender for Office 365、Cloud App Security) はバックグラウンドで自動的に接続されます (まだ存在していない場合)。 Microsoft 365 Defender の接続後にいずれかのコンポーネント ライセンスを購入した場合でも、新しい製品のアラートとインシデントは、追加の構成や料金なしで Azure Sentinel に送信されます。

### <a name="microsoft-365-defender-incidents-and-microsoft-incident-creation-rules"></a>Microsoft 365 Defender インシデントと Microsoft インシデントの作成ルール

- Microsoft 365 セキュリティ製品からのアラートに基づいて Microsoft 365 Defender によって生成されたインシデントは、カスタム Microsoft 365 Defender ロジックを使用して作成されます。

- Azure Sentinel の Microsoft インシデント作成ルールも、(別の) カスタム Azure Sentinel ロジックを使用して、同じアラートからインシデントを作成します。

- 両方のメカニズムを一緒に使用することは完全にサポートされており、新しい Microsoft 365 Defenderインシデント作成ロジックへの移行を容易にすることができます。 しかし、これにより、同じアラートに対して **重複したインシデント** が作成されます。

- 同じインシデントに対し 2 つの警告を出さないため、Microsoft 365 Defender に接続するときは、Microsoft 365 製品 (Defender for Endpoint、Defender for Identity、Defender for Office 365、Cloud App Security) に対しては **Microsoft インシデント作成ルール** をすべて無効にすることをお勧めします。 これを行うには、コネクタのページでインシデントの作成を無効にします。 これを行うと、インシデント作成ルールによって適用されたフィルターが Microsoft 365 Defender インシデント統合に適用されないことに注意してください。

    > [!NOTE]
    > これで、Microsoft Cloud App Security のすべての種類の警告が、Microsoft 365 Defender にオンボードされます。

### <a name="working-with-microsoft-365-defender-incidents-in-azure-sentinel-and-bi-directional-sync"></a>Azure Sentinel と双方向同期での Microsoft 365 Defender インシデントの処理

Microsoft 365 Defender インシデントは、Azure Sentinel インシデント キューに、製品名 **Microsoft 365 Defender** と、他の Sentinel インシデントと同様の詳細と機能を含めて表示されます。 各インシデントには、Microsoft 365 Defender ポータルの並列インシデントへのリンクが含まれます。

インシデントが Microsoft 365 Defender で進化し、より多くのアラートまたはエンティティが追加されると、それに応じて Azure Sentinel インシデントが更新されます。

Microsoft 365 Defender または Azure Sentinel で、Microsoft 365 インシデントの状態、終了理由、または割り当てに加えられた変更は、他のインシデント キューでも同様に更新されます。 同期は、インシデントに対する変更が適用されるとすぐに、両方のポータルで遅延なく行なわれます。 最新の変更内容を確認するには、最新の情報に更新する必要がある場合があります。

Microsoft 365 Defender では、1 つのインシデントからのすべてのアラートが別のものに転送され、インシデントがマージされることがあります。 このマージが起きると、Azure Sentinel のインシデントに変更が反映されます。 一方のインシデントには両方の元のインシデントからの全アラートが含まれ、他方のインシデントは自動的に閉じられ、"リダイレクト済み" のタグが追加されます。

> [!NOTE]
> Azure Sentinel のインシデントには、最大で 150 のアラートが含まれることがあります。 Microsoft 365 Defender インシデントには、これを超えることができます。 アラートが 150 を超える Microsoft 365 Defender インシデントが Azure Sentinel に同期されている場合、Sentinel インシデントは "150 +" のアラートを持つものとして表示され、アラートの完全なセットを確認できる Microsoft 365 Defender の並列インシデントへのリンクが提供されます。

## <a name="advanced-hunting-event-collection"></a>高度なハンティング イベント収集

Microsoft 365 Defender コネクタを使用すると、**高度なハンティング** イベント (生のイベント データの種類) を、Microsoft 365 Defender およびそのコンポーネント サービスから Azure Sentinel にストリーミングすることもできます。 現在、[Microsoft Defender For Endpoint (MDATP)](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) の[高度なハンティング](/windows/security/threat-protection/microsoft-defender-atp/advanced-hunting-overview) イベントを収集して、それらを Azure Sentinel ワークスペースの専用のテーブルに直接ストリーミングすることができます。 これらのテーブルは Microsoft 365 Defender ポータルで使用されているのと同じスキーマに基づいて構築されており、高度なハンティング イベントの完全なセットに完全にアクセスして、次のことを行うことができます。

- 既存の Microsoft Defender for Endpoint の高度なハンティング クエリを Azure Sentinel に簡単にコピーする。

- 生のイベント ログを使用して、アラート、ハンティング、調査に関する追加の分析情報を取得し、Azure Sentinel のその他のデータ ソースのイベントとこれらのイベントを関連付ける。

- Microsoft Defender for Endpoint または Microsoft 365 Defender の 30 日間の既定の保有期間を超えて、延長された保有期間でログを保存する。 これを行うには、ワークスペースの保有期間を構成するか、Log Analytics でテーブルごとの保有期間を構成します。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Microsoft 365 Defender コネクタを使用して Microsoft 365 Defender を Azure Sentinel と一緒に使用することの利点について学習しました。

- [Microsoft 365 Defender コネクタを有効にする](connect-microsoft-365-defender.md)手順を確認します。
- [カスタム アラート](detect-threats-custom.md)を作成して[インシデントを調査](investigate-cases.md)します。
