---
title: TexConv - テクスチャ変換ツール
description: TexConv コマンドライン ツールのユーザー マニュアル
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 1d9b2ca163b70435a6c0e245e66492e8e2866639
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "80678841"
---
# <a name="texconv---texture-conversion-tool"></a>TexConv - テクスチャ変換ツール

TexConv は、PNG、TGA、JPEG、DDS などの一般的な入力形式のテクスチャを、ランタイムの使用に合わせて最適化された形式に処理するためのコマンドライン ツールです。
最も一般的なシナリオは、1 つの入力ファイル `A.xxx` を最適化された形式 `B.yyy` に変換するというものですが、このツールには高度な用途に対応する追加のオプションが多数用意されています。

## <a name="command-line-help"></a>コマンドライン ヘルプ

`--help` パラメーターを指定して TexConv.exe を実行すると、使用可能なすべてのオプションが一覧表示されます。 さらに、TexConv を実行すると、その実行内容を容易に把握できるように、使用されたオプションが出力されます。 詳細については、こちらの出力を参照してください。

## <a name="general-usage"></a>一般的な使用

TexConv では、常に**ちょうど 1 つの出力**ファイルが生成されます。 **複数の入力**ファイルを使用して、出力をアセンブルすることもできます。 アセンブリの場合は、どの入力ファイルからどのチャネル("*赤、緑、青*"、または "*アルファ*") を取得し、それを出力イメージのどのチャネルに移動するかを指定するための**チャネル マッピング**も必要です。

最も簡単なコマンド ラインは次のとおりです。

```cmd
TexConv.exe -out D:/result.dds -in0 D:/img.jpg -rgba in0
```

- `-out` では出力ファイルと形式が指定されます。
- `-in0` では最初の入力イメージが指定されます。
- `-rgba` では、出力イメージで 4 つすべてのチャネルを使用する必要があること、およびそれらを入力イメージから 1:1 で取得する必要があることが指定されます。

## <a name="multiple-input-files"></a>複数の入力ファイル

複数の入力ファイルから出力をアセンブルするには、数値が大きくなっていく `-in` オプションを使用して各入力ファイルを指定します。

```cmd
-in0 D:/img0.jpg -in1 D:/img1.jpg -in2 D:/img2.jpg ...
```

2D テクスチャからキューブマップをアセンブルする場合、`-right`、`-left`、`-top`、`-bottom`、`-front`、`-back` または `-px`、`-nx`、`-py`、`-ny`、`-pz`、`-nz` を使用することもできます。

これらの入力を出力ファイルにマップするには、適切なチャネル マッピングが必要です。

## <a name="channel-mappings"></a>チャネル マッピング

チャネルマッピング オプションでは、特定の出力チャネルを塗りつぶすための入力が指定されます。 次のように、各チャネルの入力を個別に指定できます。

```cmd
-r in0.b -g in0.g -b in0.r -a in1.r
```

ここで、出力の RGB チャネルについては、最初の入力イメージを使用して塗りつぶされますが、赤と青がスワップされます。 出力のアルファ チャネルについては、2 番目の入力イメージの赤チャネルからの値で塗りつぶされます。

各チャネルのマッピングを個別に指定すると、最大限の柔軟性が得られます。 便宜上、"スウィズリング" 演算子を使用して同じ内容を記述することができます。

```cmd
-rgb in0.bgr -a in1.r
```

### <a name="output-channels"></a>出力チャネル

利用可能なチャネルマッピング オプションは次のとおりです:

- `-r`、`-g`、`-b`、`-a`: これらによってシングル チャネルの割り当てが指定されます
- `-rg`: 赤および緑のチャネルの割り当てを指定します。
- `-rgb`: 赤、緑、および青のチャネルの割り当てを指定します。
- `-rgba`: 4 つのチャネルの割り当てをすべて指定します。

R、RG、または RGB チャネルのみをメンションすると、それぞれ 1 個、2 個、または 3 個のチャネルのみを使用して出力ファイルを作成するように TexConv に指示が出されます。

### <a name="input-swizzling"></a>入力スウィズリング

どの入力テクスチャでどの出力チャネルを塗りつぶすかを指定するときに、次のように入力をスウィズルすることができます。

- `-rgba in0` は `-rgba in0.rgba` と等価です
- `-rgba in0.bgra` を指定すると、入力チャネルがスウィズルされます
- `-rgb in0.rrr` を指定すると、赤のチャネルがすべてのチャネルに複製されます

また、黒または白のいずれかでチャネルを塗りつぶすこともできます。

- `-rgb in0 -a white` を指定すると、4 つのチャネルの出力テクスチャが作成されますが、アルファは完全に不透明に設定されます
- `-rg black -b white` を指定すると、完全に青のテクスチャが作成されます

## <a name="common-options"></a>共通オプション

最も興味深いオプションを以下に示します。 その他のオプションを一覧表示するには、`TexConv --help` を使用します。

### <a name="output-type"></a>出力の種類

- `-type 2D`: 出力は、通常の 2D イメージになります。
- `-type Cubemap`: 出力はキューブマップ イメージになります。 DDS 出力ファイルでのみサポートされます。 この値を指定すると、6 個の標準 2D 入力イメージからキューブマップをアセンブルすることができます。

### <a name="image-compression"></a>イメージの圧縮

- `-compression none`: 出力イメージは圧縮されません。
- `-compression medium`: サポートされている場合、出力イメージでは品質をあまり犠牲にすることなく圧縮が使用されます。
- `-compression high`: サポートされている場合、出力イメージでは圧縮が使用されます。ファイルをより小さくすることが優先され品質は低下します。

### <a name="mipmaps"></a>mipmap

既定では、出力形式でサポートされている場合に mipmap が TexConv によって生成されます。

- `-mipmaps none`: mipmap は生成されません。
- `-mipmaps Linear`: サポートされている場合に mipmap がボックス フィルターを使用して生成されます。

### <a name="usage-srgb--gamma-correction"></a>使用法 (sRGB とガンマ補正)

`-usage` オプションでは、出力の目的が指定されます。これにより、入力ファイルと出力ファイルに対してガンマ補正を適用するかどうかが TexConv に指示されます。 使用法の影響を受けるのは、RGB チャネルのみです。 アルファ チャネルは常に "線形" 値を含んでいると見なされます。 使用法が指定されていない場合、"自動" モードでは、最初の入力イメージの形式とファイル名を基に使用法が検出されます。 たとえば、シングル チャネルおよびデュアル チャネルの出力形式は常に線形になります。 出力を調べて、TexConv によって行われた決定の内容を確認します。

- `-usage Linear`: 出力イメージには、色を表さない値が含まれています。 これは、通常、メタリックおよび粗さのテクスチャと、すべての種類のマスクに当てはまります。

- `-usage Color`: 出力イメージでは、拡散または Albedo マップなどの色が表現されます。 出力 DDS ヘッダーには、sRGB フラグが設定されます。

- `-usage HDR`: 出力ファイルでは、エンコードのために、ピクセルあたりのビット数を 8 ビットより大きくする必要があります。 その結果、すべての値が線形空間に格納されます。 HDR テクスチャの場合、データが色を表しているか、それとも他のデータを表しているかは重要ではありません。

- `-usage NormalMap`: 出力イメージは、接空間線マップを表します。 値は正規化され、mipmap 計算は若干最適化されます。

- `-usage NormalMap_Inverted`: 出力は、Y が入力とは逆方向を指す接線空間法線マップです。

### <a name="image-rescaling"></a>イメージの再スケーリング

- `-minRes 64`: 出力の最小解像度が指定されます。 入力イメージがそれより小さい場合は、アップスケールされます。
- `-maxRes 1024`: 出力ファイルの最大サイズが指定されます。 入力イメージがそれより大きい場合は、ダウンスケールされます。
- `-downscale 1`: この値が 0 より大きい場合、入力イメージは N 倍の解像度で半分になります。 これを使用すると、全体的な品質低下が適用されます。

## <a name="examples"></a>例

### <a name="convert-a-color-texture"></a>色テクスチャを変換する

```cmd
TexConv.exe -out D:/diffuse.dds -in0 D:/diffuse.jpg -rgba in0 -usage color
```

### <a name="convert-a-normal-map"></a>法線マップを変換する

```cmd
TexConv.exe -out D:/normalmap.dds -in0 D:/normalmap.png -rgb in0 -usage normalmap
```

### <a name="create-an-hdr-cubemap"></a>HDR キューブマップを作成する

```cmd
TexConv.exe -out "D:/skybox.dds" -in0 "D:/skymap.hdr" -rgba in0 -type cubemap -usage hdr
```

HDR キューブマップのソースとして優れているのは、[hdrihaven.com](https://hdrihaven.com/hdris/) です。

### <a name="bake-multiple-images-into-one"></a>複数のイメージを 1 つに焼き付ける

```cmd
TexConv.exe -out "D:/Baked.dds" -in0 "D:/metal.tga" -in1 "D:/roughness.png" -in2 "D:/DiffuseAlpha.dds" -r in1.r -g in0.r -b black -a in2.a -usage linear
```

### <a name="extract-a-single-channel"></a>シングル チャネルを抽出する

```cmd
TexConv.exe -out D:/alpha-mask-only.dds -in0 D:/DiffuseAlpha.dds -r in0.a
```
