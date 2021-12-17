---
title: Microsoft Defender for App Service - 利点と機能
description: Microsoft Defender for App Service の機能と、それをサブスクリプションで有効にする方法について説明します
author: memildin
ms.author: memildin
ms.date: 11/09/2021
ms.topic: overview
ms.service: defender-for-cloud
manager: rkarlin
ms.openlocfilehash: 898a6dbae646d038b97929787498c7d7e2bf92db
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132525587"
---
# <a name="protect-your-web-apps-and-apis"></a>Web アプリと API の保護

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

## <a name="prerequisites"></a>前提条件

Defender for Cloud は App Service とネイティブに統合されており、デプロイやオンボーディングの必要はありません。統合は透過的です。

Microsoft Defender for App Service を使用して Azure App Service プランを保護するには、次のものが必要です。

- 専用のマシンに関連付けられた、サポートされている App Service プラン。 サポートされているプランは、「[可用性](#availability)」に記載されています。

- [クイックスタート: 強化されたセキュリティ機能の有効化](enable-enhanced-security.md)に関するページで説明されているようにサブスクリプションで有効化された Defender for Cloud の強化された保護。

    > [!TIP]
    > 必要に応じて、Microsoft Defender for App Service などの個々の Microsoft Defender プランを有効にできます。

## <a name="availability"></a>可用性

| 側面                       | 詳細                                                                                                                                                                                        |
|------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| リリース状態:               | 一般公開 (GA)                                                                                                                                                                      |
| 価格:                     | Microsoft Defender for App Service は、[価格ページ](https://azure.microsoft.com/pricing/details/security-center/)に記載されているように課金されます。<br>課金は、すべてのプランのコンピューティング インスタンスの合計に基づいて行われます。       |
| サポートされている App Service プラン: | [従量課金プランの Azure Functions](../azure-functions/functions-scale.md) を除き、[すべての App Service プラン](https://azure.microsoft.com/pricing/details/app-service/plans/)がサポートされています。 |
| クラウド:                      | :::image type="icon" source="./media/icons/yes-icon.png"::: 商用クラウド<br>:::image type="icon" source="./media/icons/no-icon.png"::: 各国 (Azure Government、Azure China 21Vianet)                                                     |
|                              |                                                                                                                                                                                                |

## <a name="what-are-the-benefits-of-microsoft-defender-for-app-service"></a>Microsoft Defender for App Service の利点

Azure App Service は、Web アプリや API の作成とホスティングに使用するフル マネージドのプラットフォームです。 フル マネージドのプラットフォームであるため、そのインフラストラクチャについて心配する必要はありません。 エンタープライズ クラスのパフォーマンス、セキュリティ、コンプライアンス要件を満たすための管理機能、監視機能、運用に関する分析情報を提供します。 詳細については、[Azure App Service](https://azure.microsoft.com/services/app-service/)に関するページを参照してください。

**Microsoft Defender for App Service** では、クラウドのスケールを使用して、App Service で実行されるアプリケーションをターゲットとした攻撃が識別されます。 攻撃者は、Web アプリケーションをプローブして弱点を発見し、悪用します。 Azure で実行されているアプリケーションに対する要求は、特定の環境にルーティングされる前に、複数のゲートウェイを通過し、そこで検査され、ログに記録されます。 その後、このデータは、悪用や攻撃者を特定し、後で使用される新しいパターンを学習するために使用されます。

Microsoft Defender for App Service を有効にすると、この Defender プランで提供される次のサービスの利点をすぐに活用できます。

- **セキュア** - App Service プランの対象となるリソースが Defender for App Service によって評価され、その結果に基づき、セキュリティ上の推奨事項が生成されます。 それらの推奨事項に記載された詳しい手順に従って、App Service リソースのセキュリティを強化することができます。

- **検出** - Defender for App Service では次を監視することで、App Service リソースに対するさまざまな脅威を検出します。
    - App Service が実行されている VM インスタンスとその管理インターフェイス
    - App Service アプリとの間で送受信される要求と応答
    - 基になるサンドボックスと VM
    - App Service の内部ログ (Azure がクラウド プロバイダーとして備える可視性を利用)

クラウドネイティブのソリューションである Defender for App Service では、複数のターゲットに適用される攻撃手法を特定できます。 たとえば、単一のホストからでは、IP の小さなサブセットを起点として複数のホスト上の類似のエンドポイントをクロールする分散型の攻撃を特定することは難しいでしょう。

ログ データとインフラストラクチャを併用することで、流行している新しい攻撃からお客様のマシンのセキュリティ侵害に至るまで、そのストーリーを把握することができるのです。 そのため、Web アプリが悪用された後に Microsoft Defender for App Service がデプロイされた場合でも、進行中の攻撃を検出できる可能性があります。


## <a name="what-threats-can-defender-for-app-service-detect"></a>Defender for App Service で検出できる脅威

### <a name="threats-by-mitre-attck-tactics"></a>MITRE ATT&CK 戦術による脅威

Defender for Cloud では、App Service リソースに対するさまざまな脅威が監視されます。 このアラートは、攻撃準備からコマンド アンド コントロールまで、ほぼすべての MITRE ATT&CK 戦術をカバーしています。

- **攻撃準備の脅威** - Defender for Cloud では、攻撃者がアプリケーションの弱点をプローブするためによく使用する、複数のタイプの脆弱性スキャナーの実行を検出できます。

- **初期アクセスの脅威** - これらのアラートには、[Microsoft 脅威インテリジェンス](https://go.microsoft.com/fwlink/?linkid=2128684)が利用されています。たとえば、悪意のある既知の IP アドレスが Azure App Service の FTP インターフェイスに接続するとアラートがトリガーされます。

- **実行の脅威** - Defender for Cloud では、高い特権のコマンド、Windows App Service での Linux コマンド、ファイルレス攻撃の動作、デジタル通貨マイニング ツール、およびその他多くの疑わしく悪意のあるコード実行アクティビティの実行試行を検出できます。

### <a name="dangling-dns-detection"></a>未解決の DNS の検出

App Service Web サイトが使用停止されたときに DNS レジストラーに残っている DNS エントリ (未解決 DNS エントリと呼ばれる) も Defender for App Service によって特定されます。 Web サイトを削除したにもかかわらず、そのカスタム ドメインを DNS レジストラーから削除しなかった場合、その DNS エントリは、存在しないリソースを参照することになり、サブドメインが乗っ取りに対して無防備になります。 Defender for Cloud が DNS レジストラーをスキャンして "*既存*" の未解決 DNS エントリを検出することはありません。アラートが生成されるのは、App Service Web サイトの使用を停止したにもかかわらず、そのカスタム ドメイン (DNS エントリ) が削除されていない場合です。

サブドメインの乗っ取りは、組織にとって重大度の高い一般的な脅威です。 脅威アクターは未解決の DNS エントリを検出すると、宛先のアドレスに独自のサイトを作成します。 そうして組織のドメインに宛てられたトラフィックは脅威アクターのサイトに誘導され、悪意のある多様なアクティビティにそのトラフィックが利用されることとなります。

未解決の DNS の保護は、ドメイン管理に Azure DNS を使用しているか、外部のドメイン レジストラーを使用しているかに関係なく利用でき、Windows 上の App Service と Linux 上の App Service の両方に適用されます。

:::image type="content" source="media/defender-for-app-service-introduction/dangling-dns-alert.png" alt-text="検出された未解決の DNS エントリに関するアラートの例。Microsoft Defender for App Service を有効にして、これを含む環境に対するアラートを受け取ります。" lightbox="media/defender-for-app-service-introduction/dangling-dns-alert.png":::

未解決の DNS とサブドメイン乗っ取りの脅威について詳しくは、「[未解決の DNS エントリを防ぎ、サブドメインの乗っ取りを回避する](../security/fundamentals/subdomain-takeover.md)」を参照してください。

App Service アラートの一覧については、[アラートのリファレンス表](alerts-reference.md#alerts-azureappserv)を参照してください。

> [!NOTE]
> カスタム ドメインが App Service リソースを直接参照していない場合や、未解決の DNS の保護が有効にされてから Web サイトへのトラフィックが Defender for Cloud によって監視されていない場合、未解決の DNS のアラートが Defender for Cloud からトリガーされない場合があります (カスタム ドメインの特定につながるログが存在しないため)。

## <a name="next-steps"></a>次の手順

この記事では、Microsoft Defender for App Service について説明しました。 

> [!div class="nextstepaction"]
> [強化された保護を有効にする](enable-enhanced-security.md)

関連資料については、次の記事をご覧ください。 

- アラートを Microsoft Sentinel、サードパーティの SIEM、またはその他の外部ツールにエクスポートする場合は、「[SIEM、SOAR、または IT サービス管理ソリューションにアラートをストリーミングする](export-to-siem.md)」の手順に従ってください。
- Microsoft Defender for App Service アラートの一覧については、[アラートのリファレンス表](alerts-reference.md#alerts-azureappserv)を参照してください。
- App Service プランの詳細については、「[App Service プラン](https://azure.microsoft.com/pricing/details/app-service/plans/)」を参照してください。
