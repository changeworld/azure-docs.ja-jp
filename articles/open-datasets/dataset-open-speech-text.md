---
title: Russian Open Speech To Text (ロシア語の公開音声テキスト変換)
titleSuffix: Azure Open Datasets
description: Azure Open Datasets で Russian Open Speech To Text (ロシア語の公開音声テキスト変換) データセットを使用する方法について説明します。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 4c0ec200fa3506dd0b251903954240eff5132dd4
ms.sourcegitcommit: 7c44970b9caf9d26ab8174c75480f5b09ae7c3d7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2021
ms.locfileid: "112982512"
---
# <a name="russian-open-speech-to-text"></a>Russian Open Speech To Text (ロシア語の公開音声テキスト変換)

さまざまな音声ソースから派生した音声のサンプルのコレクションです。 データセットにはロシア語の短い音声クリップが含まれます。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

このロシア語の音声テキスト変換 (STT) データセットの内容は次のとおりです。
- 約 1,600 万発話
- 約 20,000 時間
- 2.3 TB (int16、.wav 形式、非圧縮)、356 G (OPUS)
- 検証データセットを除くすべてのファイルを OPUS に変換済み

データセットの主な目的は、音声テキスト変換モデルのトレーニングを行うことです。

## <a name="dataset-composition"></a>データセットの構成

データセットのサイズは .wav ファイルのものです。

| データセット | 発話数 | 時間数 | GB | 秒数/文字数 | COMMENT | アノテーション | 品質/ノイズ |
|-|-|-|-|-|-|-|-|
| radio_v4 (*) | 7,603,192 | 10,430 | 1,195 | 5/68 | ラジオ | 整列 | 95%/明瞭 |
| public_speech (*) | 1,700,060 | 2,709 | 301 | 6/79 | 演説 | 整列 | 95%/明瞭 |
| audiobook_2 | 1,149,404 | 1,511 | 162 | 5/56 | ブック | 整列 | 95%/明瞭 |
| radio_2 | 651,645 | 1,439 | 154 | 8/110 | ラジオ | 整列 | 95%/明瞭 |
| public_youtube1120 | 1,410,979 | 1,104 | 237 | 3/34 | YouTube | 字幕 | 95%/ほぼ明瞭 |
| public_youtube700 | 759,483 | 701 | 75 | 3/43 | YouTube | 字幕 | 95%/ほぼ明瞭 |
| tts_russian_addresses | 1,741,838 | 754 | 81 | 2/20 | アドレス | TTS の 4 つの音声 | 100%/明瞭 |
| asr_public_phone_calls_2 | 603,797 | 601 | 66 | 4/37 | 電話 | ASR | 70%/ノイズが多い |
| public_youtube1120_hq | 369,245 | 291 | 31 | 3/37 | YouTube HQ | 字幕 | 95%/ほぼ明瞭 |
| asr_public_phone_calls_1 | 233,868 | 211 | 23 | 3/29 | 電話 | ASR | 70%/ノイズが多い |
| radio_v4_add (*) | 92,679 | 157 | 18 | 6/80 | ラジオ | 整列 | 95%/明瞭 |
| asr_public_stories_2 | 78,186 | 78 | 9 | 4/43 | ブック | ASR | 80%/明瞭 |
| asr_public_stories_1 | 46,142 | 38 | 4 | 3/30 | ブック | ASR | 80%/明瞭 |
| public_series_1 | 20,243 | 17 | 2 | 3/38 | YouTube | 字幕 | 95%/ほぼ明瞭 |
| asr_calls_2_val | 12,950 | 7,7 | 2 | 2/34 | 電話 | 手動でのアノテーション | 99%/明瞭 |
| public_lecture_1 | 6,803 | 6 | 1 | 3/47 | 講演 | 字幕 | 95%/明瞭 |
| buriy_audiobooks_2_val | 7,850 | 4,9 | 1 | 2/31 | ブック | 手動でのアノテーション | 99%/明瞭 |
| public_youtube700_val | 7,311 | 4,5 | 1 | 2/35 | YouTube | 手動でのアノテーション | 99%/明瞭 |

(*) データのサンプルのみが txt ファイルと共に提供されています。

## <a name="annotation-methodology"></a>アノテーションの手法

データセットはオープンソースを使用してコンパイルされています。 長いシーケンスは音声アクティビティ検出とアラインメントと使用してオーディオ チャンクに分割されています。 一部のオーディオ タイプは、アノテーションが自動的に行われ、ヒューリスティックを使って統計的に検証されます。

## <a name="data-volumes-and-update-frequency"></a>データ量と更新頻度

データセットの合計サイズは 350 GB です。 公開共有ラベルを含むデータセットの合計サイズは 130 GB です。

データセット自体が下位互換性のために更新される可能性はほとんどありません。 ベンチマークと除外するファイルについては、元のリポジトリに従ってください。

将来的には新しいドメインと言語が追加される可能性があります。

## <a name="audio-normalization"></a>オーディオの正規化

ランタイム拡張をより簡単かつ高速にするために、すべてのファイルが正規化されています。 処理は次のとおりです。

- 必要な場合はモノラルに変換
- 必要に応じて、16 kHz のサンプリング レートに変換
- 16 ビット整数として保存
- OPUS に変換

## <a name="on-disk-db-methodology"></a>オン ディスク DB の手法

各オーディオ ファイル (wav、バイナリ) はハッシュ化されています。 ハッシュは、より最適な FS 操作のためのフォルダー階層を作成するために使用されます。

```python
target_format = 'wav'
wavb = wav.tobytes()

f_hash = hashlib.sha1(wavb).hexdigest()

store_path = Path(root_folder,
                  f_hash[0],
                  f_hash[1:3],
                  f_hash[3:15] + '.' + target_format)
```

## <a name="downloads"></a>ダウンロード

データセットは、次の 2 つの形式で提供されます。

- Azure Blob Storage や直接リンクを介して利用できるアーカイブ。
- Azure Blob Storage を介して利用できる元のファイル。すべてのものが 'https://azureopendatastorage.blob.core.windows.net/openstt/ ' に保存されています

フォルダー構造:

```
└── ru_open_stt_opus                                            <= archived folders
│   │
│   ├── archives
│   │    ├── asr_calls_2_val.tar.gz                             <= tar.gz archives with opus and wav files
│   │    │   ...                                                <= see the below table for enumeration
│   │    └── tts_russian_addresses_rhvoice_4voices.tar.gz
│   │
│   └── manifests
│        ├── asr_calls_2_val.csv                                <= csv files with wav_path, text_path, duration (see notebooks)
│        │   ...
│        └── tts_russian_addresses_rhvoice_4voices.csv
│
└── ru_open_stt_opus_unpacked                                   <= a separate folder for each uploaded domain
    ├── public_youtube1120
    │    ├── 0                                                  <= see "On disk DB methodology" for details
    │    ├── 1
    │    │   ├── 00
    │    │   │  ...
    │    │   └── ff
    │    │        ├── *.opus                                   <= actual files
    │    │        └── *.txt
    │    │   ...
    │    └── f
    │
    ├── public_youtube1120_hq
    ├── public_youtube700_val
    ├── asr_calls_2_val
    ├── radio_2
    ├── private_buriy_audiobooks_2
    ├── asr_public_phone_calls_2
    ├── asr_public_stories_2
    ├── asr_public_stories_1
    ├── public_lecture_1
    ├── asr_public_phone_calls_1
    ├── public_series_1
    └── public_youtube700
```

| データセット | GB、wav | GB、アーカイブ | アーカイブ | 接続元 | マニフェスト |
|-|-|-|-|-|-|
| トレーニング |  |  |  |  |  |
| ラジオと演説のサンプル | - | 11.4 | OPUS + txt | - | manifest |
| audiobook_2 | 162 | 25.8 | OPUS + txt | インターネット + アライメント | manifest |
| radio_2 | 154 | 24.6 | OPUS + txt | ラジオ | manifest |
| public_youtube1120 | 237 | 19.0 | OPUS + txt | YouTube 動画 | manifest |
| asr_public_phone_calls_2 | 66 | 9.4 | OPUS + txt | インターネット + ASR | manifest |
| public_youtube1120_hq | 31 | 4.9 | OPUS + txt | YouTube 動画 | manifest |
| asr_public_stories_2 | 9 | 1.4 | OPUS + txt | インターネット + アライメント | manifest |
| tts_russian_addresses_rhvoice_4voices | 80.9 | 12.9 | OPUS + txt | TTS | manifest |
| public_youtube700 | 75.0 | 12.2 | OPUS + txt | YouTube 動画 | manifest |
| asr_public_phone_calls_1 | 22.7 | 3.2 | OPUS + txt | インターネット + ASR | manifest |
| asr_public_stories_1 | 4.1 | 0.7 | OPUS + txt | 公開されている物語 | manifest |
| public_series_1 | 1.9 | 0.3 | OPUS + txt | 公開されているシリーズ | manifest |
| public_lecture_1 | 0.7 | 0.1 | OPUS + txt | インターネット + 手動 | manifest |
| Val |  |  |  |  |  |
| asr_calls_2_val | 2 | 0.8 | wav + txt | インターネット | manifest |
| buriy_audiobooks_2_val | 1 | 0.5 | wav + txt | 書籍 + 手動 | manifest |
| public_youtube700_val | 2 | 0.13 | wav + txt | YouTube のビデオ + 手動 | manifest |

## <a name="download-instructions"></a>ダウンロードの手順

### <a name="direct-download"></a>直接ダウンロード

データセットを直接ダウンロードする方法については、[GitHub のダウンロード手順に関するページ](https://github.com/snakers4/open_stt#download-instructions)を参照してください。

## <a name="additional-information"></a>追加情報

データに関するヘルプや質問については、データ作成者にお問い合わせください (aveysov@gmail.com)。

このライセンスでは、ユーザーは、作成者への帰属を示している限り、非営利目的でのみ、あらゆるメディアや形式で素材を配布、リミックス、改変、作成することができます。 次の要素が含まれます。
* BY - 作成者のクレジットを表示する必要があります
* NC - 非営利目的でのみ作品の使用が許可されます

CC-BY-NC および営利目的での使用にはデータセット作成者との合意が必要です。

## <a name="data-access"></a>データ アクセス

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=open-speech-to-text -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=open-speech-to-text)** 。

## <a name="helper-functions--dependencies"></a>ヘルパー関数および依存関係

### <a name="building-libsndfile"></a>libsndfile のビルド

Python で、大きなオーバーヘッドを発生させずに OPUS ファイルを効率的に読み取るには、pysoundfile (libsoundfile の Python CFFI ラッパー) を使用します。

OPUS のサポートはアップストリームで実装されていますが、適切にリリースされていません。 そのため、カスタム ビルドとモンキー パッチの組み合わせを選択しました。

通常、sudo アクセスを使用してシェルでこれを実行する必要があります。

```bash
apt-get update
apt-get install cmake autoconf autogen automake build-essential libasound2-dev \
libflac-dev libogg-dev libtool libvorbis-dev libopus-dev pkg-config -y

cd /usr/local/lib
git clone https://github.com/erikd/libsndfile.git
cd libsndfile
git reset --hard 49b7d61
mkdir -p build && cd build

cmake .. -DBUILD_SHARED_LIBS=ON
make && make install
cmake --build .
```
### <a name="helper-functions--dependencies"></a>ヘルパー関数および依存関係

次のライブラリをインストールします。

```
pandas
numpy
scipy
tqdm
soundfile
librosa
```

マニフェストは、次の列が含まれた csv ファイルです。

- オーディオへのパス
- テキスト ファイルへのパス
- Duration

これらは、データにアクセスする最もシンプルな形式であることが実証されています。

使いやすくするために、すべてのマニフェストが既に再ルート化されています。 マニフェスト内のパスはすべて相対パスであるため、ルート フォルダーを指定する必要があります。

```python
# manifest utils
import os
import numpy as np
import pandas as pd
from tqdm import tqdm
from urllib.request import urlopen


def reroot_manifest(manifest_df,
                    source_path,
                    target_path):
    if source_path != '':
        manifest_df.wav_path = manifest_df.wav_path.apply(lambda x: x.replace(source_path,
                                                                              target_path))
        manifest_df.text_path = manifest_df.text_path.apply(lambda x: x.replace(source_path,
                                                                                target_path))
    else:
        manifest_df.wav_path = manifest_df.wav_path.apply(lambda x: os.path.join(target_path, x))
        manifest_df.text_path = manifest_df.text_path.apply(lambda x: os.path.join(target_path, x))    
    return manifest_df


def save_manifest(manifest_df,
                  path,
                  domain=False):
    if domain:
        assert list(manifest_df.columns) == ['wav_path', 'text_path', 'duration', 'domain']
    else:
        assert list(manifest_df.columns) == ['wav_path', 'text_path', 'duration']

    manifest_df.reset_index(drop=True).sort_values(by='duration',
                                                   ascending=True).to_csv(path,
                                                                          sep=',',
                                                                          header=False,
                                                                          index=False)
    return True


def read_manifest(manifest_path,
                  domain=False):
    if domain:
        return pd.read_csv(manifest_path,
                        names=['wav_path',
                               'text_path',
                               'duration',
                               'domain'])
    else:
        return pd.read_csv(manifest_path,
                        names=['wav_path',
                               'text_path',
                               'duration'])


def check_files(manifest_df,
                domain=False):
    orig_len = len(manifest_df)
    if domain:
        assert list(manifest_df.columns) == ['wav_path', 'text_path', 'duration']
    else:
        assert list(manifest_df.columns) == ['wav_path', 'text_path', 'duration', 'domain']
    wav_paths = list(manifest_df.wav_path.values)
    text_path = list(manifest_df.text_path.values)

    omitted_wavs = []
    omitted_txts = []

    for wav_path, text_path in zip(wav_paths, text_path):
        if not os.path.exists(wav_path):
            print('Dropping {}'.format(wav_path))
            omitted_wavs.append(wav_path)
        if not os.path.exists(text_path):
            print('Dropping {}'.format(text_path))
            omitted_txts.append(text_path)

    manifest_df = manifest_df[~manifest_df.wav_path.isin(omitted_wavs)]
    manifest_df = manifest_df[~manifest_df.text_path.isin(omitted_txts)]
    final_len = len(manifest_df)

    if final_len != orig_len:
        print('Removed {} lines'.format(orig_len-final_len))
    return manifest_df


def plain_merge_manifests(manifest_paths,
                          MIN_DURATION=0.1,
                          MAX_DURATION=100):

    manifest_df = pd.concat([read_manifest(_)
                             for _ in manifest_paths])
    manifest_df = check_files(manifest_df)

    manifest_df_fit = manifest_df[(manifest_df.duration>=MIN_DURATION) &
                                  (manifest_df.duration<=MAX_DURATION)]

    manifest_df_non_fit = manifest_df[(manifest_df.duration<MIN_DURATION) |
                                      (manifest_df.duration>MAX_DURATION)]

    print(f'Good hours: {manifest_df_fit.duration.sum() / 3600:.2f}')
    print(f'Bad hours: {manifest_df_non_fit.duration.sum() / 3600:.2f}')

    return manifest_df_fit


def save_txt_file(wav_path, text):
    txt_path = wav_path.replace('.wav','.txt')
    with open(txt_path, "w") as text_file:
        print(text, file=text_file)
    return txt_path


def read_txt_file(text_path):
    #with open(text_path, 'r') as file:
    response = urlopen(text_path)
    file = response.readlines()
    for i in range(len(file)):
        file[i] = file[i].decode('utf8')
    return file 

def create_manifest_from_df(df, domain=False):
    if domain:
        columns = ['wav_path', 'text_path', 'duration', 'domain']
    else:
        columns = ['wav_path', 'text_path', 'duration']
    manifest = df[columns]
    return manifest


def create_txt_files(manifest_df):
    assert 'text' in manifest_df.columns
    assert 'wav_path' in manifest_df.columns
    wav_paths, texts = list(manifest_df['wav_path'].values), list(manifest_df['text'].values)
    # not using multiprocessing for simplicity
    txt_paths = [save_txt_file(*_) for _ in tqdm(zip(wav_paths, texts), total=len(wav_paths))]
    manifest_df['text_path'] = txt_paths
    return manifest_df


def replace_encoded(text):
    text = text.lower()
    if '2' in text:
        text = list(text)
        _text = []
        for i,char in enumerate(text):
            if char=='2':
                try:
                    _text.extend([_text[-1]])
                except:
                    print(''.join(text))
            else:
                _text.extend([char])
        text = ''.join(_text)
    return text
```

```python
# reading opus files
import os
import soundfile as sf



# Fx for soundfile read/write functions
def fx_seek(self, frames, whence=os.SEEK_SET):
    self._check_if_closed()
    position = sf._snd.sf_seek(self._file, frames, whence)
    return position


def fx_get_format_from_filename(file, mode):
    format = ''
    file = getattr(file, 'name', file)
    try:
        format = os.path.splitext(file)[-1][1:]
        format = format.decode('utf-8', 'replace')
    except Exception:
        pass
    if format == 'opus':
        return 'OGG'
    if format.upper() not in sf._formats and 'r' not in mode:
        raise TypeError("No format specified and unable to get format from "
                        "file extension: {0!r}".format(file))
    return format


#sf._snd = sf._ffi.dlopen('/usr/local/lib/libsndfile/build/libsndfile.so.1.0.29')
sf._subtypes['OPUS'] = 0x0064
sf.SoundFile.seek = fx_seek
sf._get_format_from_filename = fx_get_format_from_filename


def read(file, **kwargs):
    return sf.read(file, **kwargs)


def write(file, data, samplerate, **kwargs):
    return sf.write(file, data, samplerate, **kwargs)
```

```python
# display utils
import gc
from IPython.display import HTML, Audio, display_html
pd.set_option('display.max_colwidth', 3000)
#Prepend_path is set to read directly from Azure. To read from local replace below string with path to the downloaded dataset files
prepend_path = 'https://azureopendatastorage.blob.core.windows.net/openstt/ru_open_stt_opus_unpacked/'


def audio_player(audio_path):
    return '<audio preload="none" controls="controls"><source src="{}" type="audio/wav"></audio>'.format(audio_path)

def display_manifest(manifest_df):
    display_df = manifest_df
    display_df['wav'] = [audio_player(prepend_path+path) for path in display_df.wav_path]
    display_df['txt'] = [read_txt_file(prepend_path+path) for path in tqdm(display_df.text_path)]
    audio_style = '<style>audio {height:44px;border:0;padding:0 20px 0px;margin:-10px -20px -20px;}</style>'
    display_df = display_df[['wav','txt', 'duration']]
    display(HTML(audio_style + display_df.to_html(escape=False)))
    del display_df
    gc.collect()
```

## <a name="play-with-a-dataset"></a>データセットを使用して再生する

### <a name="play-a-sample-of-files"></a>ファイルのサンプルを再生する

ほとんどのプラットフォーム ブラウザーでは、ネイティブ オーディオ再生がサポートされています。 そのため、HTML5 オーディオ プレーヤーを使用してデータを表示できます。

```python
manifest_df = read_manifest(prepend_path +'/manifests/public_series_1.csv')
#manifest_df = reroot_manifest(manifest_df,
                              #source_path='',
                              #target_path='../../../../../nvme/stt/data/ru_open_stt/')

```

```python
sample = manifest_df.sample(n=20)
display_manifest(sample)
```

### <a name="read-a-file"></a>ファイルを読み取る

```python
!ls ru_open_stt_opus/manifests/*.csv
```

wav ファイルおよび OPUS ファイルを最適に読み取る方法を示す例をいくつか紹介します。

wav の場合、scipy が最も高速です。 OPUS には、pysoundfile が全体的に最も適しています。

```python
%matplotlib inline

import librosa
from scipy.io import wavfile
from librosa import display as ldisplay
from matplotlib import pyplot as plt
```

#### <a name="read-a-wav"></a>wav を読み取る

```python
manifest_df = read_manifest(prepend_path +'manifests/asr_calls_2_val.csv')
#manifest_df = reroot_manifest(manifest_df,
                              #source_path='',
                              #target_path='../../../../../nvme/stt/data/ru_open_stt/')
```

```python
sample = manifest_df.sample(n=5)
display_manifest(sample)
```

```python
from io import BytesIO

wav_path = sample.iloc[0].wav_path
response = urlopen(prepend_path+wav_path)
data = response.read()
sr, wav = wavfile.read(BytesIO(data))
wav.astype('float32')
absmax = np.max(np.abs(wav))
wav =  wav / absmax
```

```python
# shortest way to plot a spectrogram
D = librosa.amplitude_to_db(np.abs(librosa.stft(wav)), ref=np.max)
plt.figure(figsize=(12, 6))
ldisplay.specshow(D, y_axis='log')
plt.colorbar(format='%+2.0f dB')
plt.title('Log-frequency power spectrogram')
# shortest way to plot an envelope
plt.figure(figsize=(12, 6))
ldisplay.waveplot(wav, sr=sr, max_points=50000.0, x_axis='time', offset=0.0, max_sr=1000, ax=None)
```

#### <a name="read-opus"></a>OPUS を読み取る

```python
manifest_df = read_manifest(prepend_path +'manifests/asr_public_phone_calls_2.csv')
#manifest_df = reroot_manifest(manifest_df,
                              #source_path='',
                              #target_path='../../../../../nvme/stt/data/ru_open_stt/')
```

```python
sample = manifest_df.sample(n=5)
display_manifest(sample)
```

```python
opus_path = sample.iloc[0].wav_path
response = urlopen(prepend_path+opus_path)
data = response.read()
wav, sr = sf.read(BytesIO(data))
wav.astype('float32')
absmax = np.max(np.abs(wav))
wav =  wav / absmax
```

```python
# shortest way to plot a spectrogram
D = librosa.amplitude_to_db(np.abs(librosa.stft(wav)), ref=np.max)
plt.figure(figsize=(12, 6))
ldisplay.specshow(D, y_axis='log')
plt.colorbar(format='%+2.0f dB')
plt.title('Log-frequency power spectrogram')
# shortest way to plot an envelope
plt.figure(figsize=(12, 6))
ldisplay.waveplot(wav, sr=sr, max_points=50000.0, x_axis='time', offset=0.0, max_sr=1000, ax=None)
```

<!-- nbend -->

---

## <a name="next-steps"></a>次のステップ

[Open Datasets カタログ](dataset-catalog.md)の残りのデータセットを表示します。
