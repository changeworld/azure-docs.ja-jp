---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 05/19/2021
ms.topic: include
ms.openlocfilehash: 103a23a478a3629eb913c3d1672b3665bc9cba9c
ms.sourcegitcommit: cc099517b76bf4b5421944bd1bfdaa54153458a0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2021
ms.locfileid: "113559735"
---
## <a name="availability"></a>可用性

|側面|詳細|
|----|:----|
|リリース状態:|一般提供 (GA)|
|価格:|**コンテナー レジストリ用の Azure Defender** の課金については、[価格に関するページ](../articles/security-center/security-center-pricing.md)をご覧ください。|
|サポートされているレジストリとイメージ:|シェル アクセスによってパブリック インターネットからアクセス可能な ACR レジストリ内の Linux イメージ|
|サポートされていないレジストリとイメージ:|Windows イメージ<br>"プライベート" レジストリ<br>[Docker スクラッチ](https://hub.docker.com/_/scratch/)のようなスーパー ミニマリスト イメージ、またはアプリケーションとそのランタイム依存関係のみが含まれ、パッケージ マネージャー、シェル、または OS は含まれない "ディストリビューションレス" イメージ。<br>[Open Container Initiative (OCI) のイメージ形式の仕様](https://github.com/opencontainers/image-spec/blob/master/spec.md)のイメージ|
|必要なロールとアクセス許可:|**セキュリティ閲覧者** および [Azure Container Registry のロールとアクセス許可](../articles/container-registry/container-registry-roles.md)|
|クラウド:|:::image type="icon" source="../articles/security-center/media/icons/yes-icon.png" border="false"::: 商用クラウド<br>:::image type="icon" source="../articles/security-center/media/icons/yes-icon.png" border="false"::: US Gov および China Gov - 現在、プッシュ時のスキャン機能のみがサポートされています。 詳細については、「[イメージはどのような場合にスキャンされますか](../articles/security-center/defender-for-container-registries-introduction.md#when-are-images-scanned)」を参照|
|||
