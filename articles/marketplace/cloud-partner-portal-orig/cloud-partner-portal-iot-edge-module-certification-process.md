---
title: IoT Edge モジュールの認定 | Microsoft Docs
description: Marketplace 向けに IoT Edge モジュールを認定します。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/19/2018
ms.author: pbutlerm
ms.openlocfilehash: c37ed908b61ca54957affed3f81526353bc3f53b
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2018
ms.locfileid: "49389566"
---
# <a name="the-iot-edge-module-certification-process"></a>IoT Edge モジュール認定プロセス

この記事では、IoT Edge モジュールを Azure Marketplace に発行できるように認定を受けるための手順と要件について説明します。 

>[!Note]
>これは一時的なドキュメントです。 IoT Edge Module Marketplace の構築が並行して進行しており、この記事はパブリック ドキュメントに差し替えられる予定です。

## <a name="understanding-an-iot-edge-module"></a>IoT Edge モジュールを理解する

モジュールの用語:

-   **モジュール イメージ**。モジュールを定義するソフトウェアを含むパッケージです。

-   **モジュール インスタンス**。IoT Edge デバイスでモジュール イメージを実行している計算の特定の単位です。 モジュール インスタンスは、IoT Edge ランタイムによって開始されます。

モジュールには IoT module SDK を含めることもでき、次の用語が使用されます。

-   **モジュール ID**。IoT Hub に格納されている情報 (セキュリティ資格情報を含む) の一部で、各モジュール インスタンスに関連付けられています。

-   **モジュール ツイン**。IoT Hub に格納されている JSON ドキュメントで、メタデータ、構成、条件などのモジュール インスタンスの状態情報が含まれています。

-   **SDK**。複数の言語 (C\#、C、Python、Java、Node.JS など) でカスタム モジュールを作成するために使用されます。

## <a name="the-onboarding-process-for-an-iot-edge-module"></a>IoT Edge モジュールのオンボード プロセス

次の画面キャプチャは、Azure Marketplace に IoT Edge モジュールが公開されるプロセスを示しています。

![認定プロセス](./media/cloud-partner-portal-iot-edge-module-certification-process/onboarding-process.png)

### <a name="onboarding-step-details"></a>オンボード手順の詳細

-   **手順 1** - パートナーは、Azure Marketplace チームによる Cloud パートナー ポータル ツールで、各自のプランを、プランの種類として **[IoT Edge モジュール]** と指定して送信します。 Cloud パートナー ポータル ツールにアクセスするには、正規の MS パートナーである必要があります。 詳細については、[発行者向けガイド](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide)を参照してください。

-   **手順 2** - Marketplace で、ウイルスとマルウェアのチェックが自動的に実行されます。 IoT チームは、このステージ中に、カスタムの自動取り込みテストを実行できます。

-   **手順 3** - モジュールが公開されます。 Marketplace に表示され、コンテナーを URL から匿名で取得できるようになります。 例: *marketplace.azurecr.io/module-identifier*。

## <a name="iot-edge-module-certification-criteria"></a>IoT Edge モジュールの認定条件

認定を受けたうえで IoT Edge モジュールを Azure Marketplace に発行するには、次の主な要件を満たす必要があります。

>[!Note]
>これらの要件は変更される可能性があります。 その場合は、その旨が事前に通知され、更新された要件を満たすことができるようにコンテナーを更新するための時間が与えられます。

### <a name="technical-requirements"></a>技術的な要件

**IoT Edge モジュールになる**

-   現時点で IoT Edge モジュールとしてサポートされるのは、Docker と互換性のあるコンテナーのみです。 モジュールは [Moby](https://mobyproject.org/) で実行される必要があります。 

    >[!Note]
    >Docker の基になるオープン ソース プロジェクトは Moby であるため、Docker コンテナーは、Moby と連携する可能性が高くなります。

-   パートナーは、次の既定の設定を指定する必要があります。 

    -   既定の**タグ** (空にすることはできません)。

    -   既定の**createOptions** (空にできます)。

    -   IoT Module SDK を使用する場合は、既定の**ツイン** (空にできます)。

    -   IoT Module SDK を使用する場合は、既定の**ルート** (空にできます)。

**プラットフォームのサポート**

-   IoT Edge レベル 1 で**一般公開**されているプラットフォーム (「[Azure IoT Edge のサポート](https://docs.microsoft.com/azure/iot-edge/support)」に記載されています) のみをサポートする必要があります。 たとえば、現時点では、これは、次の OS とアーキテクチャの組み合わせをサポートすることを意味します。

    -   Ubuntu Server 18.04 for x64

    -   Ubuntu Server 16.04 for x64

    -   Raspbian-stretch for arm32 (armhf)

**デバイスのディメンション**

-   IoT Edge デバイスになることができるのは、ディメンションが Raspberry Pi のディメンション (CPU/RAM/Storage/GPU/ など) と同等またはそれ以上であるデバイスです。 モジュールが特定のディメンションの制約内でのみ動作する場合は、それらの制約をモジュールの説明に明記する必要があります。

**既定の設定**

-   モジュールは、サポートされるプラットフォーム (OS + アーキテクチャ) ごとに、取得後すぐに既定のパラメーターで起動できる必要があります。

-   構成の設定は、明確に文書化する必要があります (タグ、環境変数、ツイン、ルート)。一覧表示の一部として、パートナーは、各自のモジュールで基本的な HTML マークアップがサポートされていることを説明したり、外部 Ｗeb ページを示したりできます。

**バージョン管理**

-   Marketplace から取得したモジュールを自動的に更新するか、自分でテスト済みのバージョンを使用するかどうかを顧客が選択できるようにする必要があります。 Marketplace のモジュールは、IoT Edge ランタイムと同じバージョン管理パターンに従う必要があります。 次に例を示します。

    -   "1.0" などのローリング タグは更新できます。

    -   "1.3.24" などのマイナー バージョン タグは更新できません。

**テレメトリ**

-   IoT Module SDK を使用するモジュールには、テレメトリ目的で Marketplace によって割り当てられた一意のモジュール識別子を設定する必要があります。 これにより、Azure Marketplace では、実行されているモジュール インスタンスの数を識別できます。 この一意識別子は、取り込み時に Marketplace によって割り当てられるコンテナーの名前です。 この識別子を設定するには、次の SDK のメソッドを使用します。
    - [C\#](https://hub.docker.com/_/mysql/)
    - [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
    - [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
    - [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device._product_info?view=azure-java-stable)

-   IoT Module SDK を使用していないモジュールでは、Cloud パートナー ポータルを通して入手できる分析情報の精度が低くなります。 たとえば、ダウンロードの数です。

**セキュリティ**

-   ホストに対するコンテナーの特権アクセスは最小限にする必要があります。 コンテナーが "特権を持つ" ことはめったにありません。

-   パートナーは、提供するサポートの一環として、各自のモジュールをセキュリティで保護する必要があります。

**更新**

-   パートナーは、モジュールが最新の状態で機能できるようにする必要があります。 IoT Edge ランタイムに対する重大な変更計画がある場合は、事前に通知されます。

**Module IoT SDK**

-   Module IoT SDK の同梱は、認定の前提条件ではありません。
    ただし、IoT モジュールの SDK を含めると、優れたユーザー エクスペリエンスを提供できる場合があります。 たとえば、ルーティング、クラウドへのメッセージ送信などをサポートできます。 実行中のモジュール インスタンスの数に関するテレメトリを取得するために、IoT Module SDK が必要です。

**主観的な要件**

-   少なくとも 1 つの実社会での IoT Edge のユース ケースを満たす必要があります。 たとえば、顧客が IoT Edge から進んで使用しない限り、WordPress コンテナーをオンボードすべきではありません。

**法的要件 (AMP ポリシーから)**

-   モジュールは、Microsoft Azure Marketplace の契約とポリシーに準拠する必要があります。 詳細については、添付される発行者契約と[参加ポリシー](https://azure.microsoft.com/support/legal/marketplace/participation-policies/)を参照してください。

-   Azure Marketplace 契約とポリシーの他に、IoT Edge モジュールというプランの種類に固有の法的要件が追加される可能性があります。 これは、現在レビュー段階です。

-   モジュールに*利用規約*と*プライバシー ポリシー*が含まれている必要があります。

-   モジュールでサード パーティを利用している場合は、その通知もモジュールに含める必要があります。

**サポート要件 (AMP ポリシーから)**

-   パートナーは、各自の IoT Edge モジュールに対してサポートを提供する責任があります。 IoT Edge モジュールの説明に記載したサポート オプションを利用できる状態を保ち、少なくとも 1 つのオプションを常に利用できるようにします  (詳細については、AMP 発行者契約のセクション 4 を参照してください)。

**カテゴリ化**

-   すべての IoT Edge モジュールは、**[モノのインターネット]\>[IoT Edge モジュール]** カテゴリの下に表示されます。 パートナーは、各自の製品に最も適したサブカテゴリを選択できます。
