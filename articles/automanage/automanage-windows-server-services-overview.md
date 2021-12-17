---
title: Windows Server 用 Automanage
description: Windows Server 用 Azure Automanage の概要と Windows Server Aure Edition に関わる機能
author: nwashburn-ms
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 07/09/2021
ms.author: niwashbu
ms.openlocfilehash: febbd30bc342569f0a1e6dfb7b0de938cc461e65
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132717381"
---
# <a name="azure-automanage-for-windows-server"></a>Windows Server 用 Azure Automanage

Windows Server 用 Azure Automanage によって、_Windows Server Aure Edition_ に特化した新機能が提供されます。  次のような機能があります。
- ホットパッチ (プレビュー)
- SMB over QUIC
- Azure の拡張ネットワーク

> [!IMPORTANT]
> ホットパッチは現在、パブリック プレビュー段階にあります。 後述するホットパッチ機能を使用するためには、オプトイン手順が必要です。
> このプレビュー版はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Windows Server サービス用 Automanage の機能は、これらの _Windows Server Azure Edition_ のイメージの 1 つ以上で見つかります。 

- Windows Server 2022 Datacenter: Azure Edition (デスクトップ エクスペリエンス)
- Windows Server 2022 Datacenter: Azure Edition (コア)

機能はイメージによって異なります。詳細については、[概要](#getting-started-with-windows-server-azure-edition)に関するページを参照してください。

## <a name="automanage-for-windows-server-capabilities"></a>Windows Server サービス用 Automanage

### <a name="hotpatch-preview"></a>ホットパッチ (プレビュー)

パブリック プレビュー段階のホットパッチは、次のイメージで利用できます。

- Windows Server 2022 Datacenter: Azure Edition (コア)

ホットパッチにより、再起動しなくても、VM にセキュリティ更新プログラムを適用できます。  さらに、Windows Server 用 Automanage によって、ホットパッチのオンボード、構成、オーケストレーションが自動化されます。  詳細については、[ホットパッチ](automanage-hotpatch.md)に関するページを参照してください。  

### <a name="smb-over-quic"></a>SMB over QUIC

パブリック プレビュー段階の SMB over QUIC は、次のイメージで利用できます。

- Windows Server 2022 Datacenter: Azure Edition (デスクトップ エクスペリエンス)
- Windows Server 2022 Datacenter: Azure Edition (コア)

SMB over QUIC は、在宅勤務者、モバイル デバイス ユーザー、およびブランチ オフィス向けに "SMB VPN" を提供することで、インターネットなどの信頼されていないネットワーク経由で、エッジ ファイル サーバーに安全で信頼性の高い接続を実現します。 [QUIC](https://datatracker.ietf.org/doc/rfc9000/) は、HTTP/3 で使用される IETF 標準のプロトコルであり、TLS 1.3 によってデータを最大限保護するように設計されており、無効にできない暗号化が必要です。 SMB は通常、QUIC トンネル内で動作するため、ユーザー エクスペリエンスは変わりません。 マルチチャネル、署名、圧縮、継続的な可用性、ディレクトリ リースなどの SMB 機能は、通常どおり機能します。 

また、SMB over QUIC は、[Windows Server 向け Automanage マシンのベスト プラクティス](automanage-windows-server.md)と統合されているため、SMB over QUIC の管理がより簡単になります。 QUIC では証明書を使用して暗号化を実現するため、多くの組織では複雑な公開キー基盤の維持に苦労しています。 Automanage マシンのベスト プラクティスでは、証明書が警告なしに期限切れにならないようにして SMB over QUIC の有効性を維持し、サービスが最大限継続されるようにします。

詳細については、「[SMB over QUIC](/windows-server/storage/file-server/smb-over-quic)」および[Automanage マシンのベスト プラクティスによる SMB over QUIC の管理](automanage-smb-over-quic.md)に関するページを参照してください。
 

### <a name="extended-network-for-azure"></a>Azure の拡張ネットワーク

Azure の拡張ネットワークは、次のイメージで利用できます。

- Windows Server 2022 Datacenter: Azure Edition (デスクトップ エクスペリエンス)
- Windows Server 2022 Datacenter: Azure Edition (コア)

Azure 拡張ネットワークを使用すると、オンプレミスのサブネットを Azure に拡張して、オンプレミスの仮想マシンが Azure に移行されるときに、元のオンプレミスのプライベート IP アドレスが保持されるように設定できます。 詳細については、[Azure 拡張ネットワーク](/windows-server/manage/windows-admin-center/azure/azure-extended-network)に関するページを参照してください。  


## <a name="getting-started-with-windows-server-azure-edition"></a>Windows Server Azure Edition の概要

Windows Server 用 Automanage のどの機能を使用するかを事前に検討し、その後それらのすべての機能がサポートされる、対応する VM イメージを選択することが重要です。  _Windows Server Azure Edition_ のイメージによっては、一部の機能にしか対応していません。詳細については、以下の表を参照してください。

### <a name="deciding-which-image-to-use"></a>使用するイメージを決定する 

|イメージ|機能|
|--|--|
|Windows Server 2022  Datacenter: Azure Edition (デスクトップ エクスペリエンス) | SMB over QUIC、Azure の拡張ネットワーク | 
| Windows Server 2022 Datacenter: Azure Edition (コア) | ホットパッチ、SMB over QUIC、Azure の拡張ネットワーク | 

### <a name="creating-a-vm"></a>VM の作成

新しい VM で Windows Server 用 Automanage の使用を開始するには、お好みの方法を使用して Azure VM を作成し、使用する [機能セット](#getting-started-with-windows-server-azure-edition)に対応する _Windows Server Azure Edition_ のイメージを選択します。  

> [!IMPORTANT]
> 一部の機能には、VM の作成時に実行する特定の構成手順があります。また、プレビュー段階にある一部の機能には、特定のオプトインとポータルの表示要件があります。  VM で上記の個々の機能を使用する方法の詳細については、その機能に関するトピックを参照してください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Automanage の詳細情報](automanage-virtual-machines.md)