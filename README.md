# 青空文庫振り仮名注釈付き音声コーパス
青空文庫及びサピエの音声デイジーデータから作成した振り仮名注釈付き音声コーパスのデータセット

***2025年3月7日 ver.2を公開しました。***

2024年1月31日に公開したver.1では重複タイトル有りの作品数が3,334点（データセットに含まれる総録音時間3,771時間）であったところ、ver.2では重複タイトル有りの作品数が3,632点（データセットに含まれる総録音時間は4,892時間）と拡大しています。

ver.1の説明については[ver.1ブランチ](https://github.com/ndl-lab/hurigana-speech-corpus-aozora/tree/ver.1)を確認してください。

## 1.概要

青空文庫振り仮名注釈付き音声コーパス（以下、「本コーパス」といいます。）は、著作権保護期間が満了している作品の次のデータを利用して作成しました。 
* 青空文庫の公開作品のテキストデータ
* 社会福祉法人日本点字図書館がシステムを管理し、全国視覚障害者情報提供施設協会が運営している、視覚障害者情報総合ネットワーク「サピエ」が視覚障害者に提供している音声デイジーデータ

音声デイジーデータを音声認識でテキストに変換することで両者のマッチングを行い、音声データとテキストからなる音声コーパスを構築したうえで、更にテキストの漢字の部分に振り仮名を付与しています。

大量のデータを必要とする機械学習等への利用を想定しています。

## 2.データについて

作家ごとにzipで圧縮されており、次のURLから取得可能です。

https://lab.ndl.go.jp/dataset/hurigana-speech-corpus-aozora/ver2/aozora_work_part1.zip

https://lab.ndl.go.jp/dataset/hurigana-speech-corpus-aozora/ver2/aozora_work_part2.zip

収集した全作品の情報と作家ごとの情報については、本リポジトリの[all_works.csv](./all_works.csv)及び[作家毎の統計情報.csv](./作家毎の統計情報.csv)を参照してください。


### 2.1. データの詳細

青空文庫の文字数に対して何らかの注釈（アノテーション）を付ける事ができた文字数の割合を収集率と定義したとき、 
収集率50%以上を満たす作品の注釈コーパスデータ（タブ区切りtxt形式、UTF-8）、収集できた音声ファイル（mp3）及び作品情報等を記載したメタデータ（csv形式、UTF-8）を公開しています。

収集率が100%とならない理由は、
* 音声デイジーのみ存在する注記の存在
* 音声デイジーと青空文庫との表現の違い
* 音声認識の認識結果の影響

等により、対応付けがうまくいかなかったためと考えられます。

作品数（重複タイトル有り）は3,632点、収集できた録音時間は4,892時間です。

音声認識は、OpenAIが2022年に公開した高性能な音声認識モデルWhisperを使用しています。

オープンソースとして公開されている[whisper-large（外部サイト）](https://huggingface.co/openai/whisper-large)及び[whisper-large-v3-turbo（外部サイト）](https://huggingface.co/openai/whisper-large-v3-turbo)をローカルサーバにダウンロードして処理を行っています。

### 2.2. フォルダ構成

作家毎のzipファイルを展開すると、例示する通り、著者ごと作品ごとに階層構造を有しています。

「作品名」の下に「daisyフォルダー名」の階層があります。

```
0 夏目漱石
1 こころ
2 こころB3528R01454395
3 mp3
4 0000025.mp3
4 0000026.mp3
4 ...
3 こころ.txt
1 収集率_夏目漱石.csv
```

### 2.3. 注釈コーパスデータの形式


下記は「こころ」の一文「名前はまだ無い。」の箇所の注釈コーパスデータの抜粋です。

```
行番号 15 0000037.mp3
私は多少の金を区面して出かけることにした。 [音声認識結果]
私は多少の金を工面して、出掛ける事にした。 [青空文庫テキスト]
「解析結果:」
工面 区面 くめん 同音_rubi
出掛 出か でか 同音_rubi
事 こと こと 同音_mo
「読み推定結果:」
私 わたし わたし 私
多少 たしょう たしょう 多少
金 かね きん 金
工面 くめん くめん 区面
出掛 でか でか 出か
事 こと こと こと
```

1-3行目はそれぞれ、分割された音声データのファイル名、音声認識の結果のテキスト、対応する青空文庫のテキストです。
4行目から始まる「解析結果」は、青空文庫の文字列、音声認識の文字列、読み、備考です。

8行目から始まる「読み推定結果」は、青空文庫の文字列、推定の読み、形態素エンジンの読み、音声認識の文字列です。

 解析結果には、Whisperによる音声認識結果と元テキストに不一致の個所があるときに、不一致となる文字列と両者に共通する読みを抽出した結果が含まれています。
青空文庫のテキストに漢字が含まれているときは、「読み推定結果」以降で、漢字とその推定した読みを示しています。


### 2.4. メタデータの形式の説明

「著者名」「作品名」「daisyフォルダー名」「total青空文字数」「文字収集率」「total青空行数」「行収集率」「total青空録音時間（時:分:秒）」「録音収集率」
「accept文字数」「accept行数」「accept録音時間（時:分:秒）」「reject文字数」「reject行数」「reject録音時間（時:分:秒）」
がカンマ区切りで記述されています。


### 2.5. 作成手順の概要
OpenAIの音声認識モデルWhisperを使用して構築を行いました。

音声データとして、「サピエ」が視覚障害者に提供している音声デイジーを使用しています。
まず、音声デイジーのxmlデータの情報をもとに文単位に音声データを分割し音声認識を行った文単位のテキストデータと青空文庫のテキストの対応を取り、
次に両者の文と、音声認識結果の複数の候補と、単語の読み辞書を用いて、読みを推定しています。

詳細は、2025年3月に開催される「言語処理学会第31回年次大会（NLP2025）」において、「プロンプトと複数の音声認識候補による青空文庫振り仮名注釈付き音声コーパスの再構築」のタイトルで発表予定です。


## 3.注意点等

* 音声デイジーの朗読データには、句読点等の記号は発音されないため含まれませんが、音声認識結果にはWhisperが推測した句読点が挿入されています。
* 青空文庫の送り仮名は現代仮名遣いと異なっているため、Whisperの音声認識の結果と差異があります。
* 青空文庫の異体字は変換しています。
* 本コーパスにおける振り仮名が正確でない場合があります。
* 現時点では音素を取得できていません

このため、音声データの発音と、付与した振り仮名が一致しない場合があります。たとえば、「私」が「わたくし」と発音していても、「わたし」になる場合があります。

* 日本語では「川」を「かわ」「がわ」と読んだり、「匹」を「ひき」「ぴき」と読んだりするため、あいまいな文字一致を行っています。

このため「一匹」の読みを、「いっひき」と推定する場合があります。


## 4. 背景
2019年に「視覚障害者等の読書環境の整備の推進に関する法律（読書バリアフリー法）が制定され、読みの困難な人に対するアクセシビリティに係る要件が整理されてきています。

 重度視覚障害者は、パソコンで画面読み上げソフトを使用して、漢字交じりの文書を音声で聞いていますが、 しばしば発生する読み上げの誤りに悩まされています。 
例えば、「表に出る」を「ひょうにでる」と読み上げられると理解が困難になります。

このため、振り仮名が付与された国立国会図書館の書誌データのタイトルや青空文庫の公開作品のテキストデータと「サピエ」の点字データを用いて、大規模日本語振り仮名注釈付きコーパスを構築し、
機械学習による同形異音語の読みの分類を行ってきました。[（参考）振り仮名注釈コーパス（青空文庫コーパス）](https://github.com/ndl-lab/huriganacorpus-aozora)

しかし、読みの推定は、同形異音語以外にも、「音読み訓読み」「人名と普通名詞」等と多数存在し、機械学習による読みの推定精度向上のためには、更なる振り仮名注釈付きコーパスの大規模化が必要となります。

本コーパスを構築・公開することで、各種アプリ・サービスにおける機械学習を用いた技術開発を促し、視覚障害者の情報障害の改善を目指しています。

## 5. 参考文献

佐藤文一, 吉永直樹, and 喜連川優. "音声認識を用いた青空文庫振り仮名注釈付き音声コーパスの構築の試み." 言語処理学会第30回年次大会講演論文集, 2024.

SATO, Fumikazu, et al. Building Large-Scale Japanese Pronunciation-Annotated Corpora for Reading Heteronymous Logograms. In: Proceedings of the Thirteenth Language Resources and Evaluation Conference. 2022. p.7113-7121.

佐藤文一 , 吉永直樹 , 喜連川優. "大規模振り仮名注釈付きコーパスを用いた同形異音語の読み分類. " 言語処理学会第28回年次大会講演論文集, 2022.

佐藤文一, 吉永直樹, and 喜連川優. "書誌データ・青空文庫・点字データを用いた振り仮名注釈付き日本語コーパスの構築." 情報処理学会第15回アクセシビリティ研究会 研究報告 2021年3月

佐藤文一, 喜連川優. "事前学習済み BERT の単語埋め込みベクトルによる同形異音語の読み誤りの改善 情報処理学会第12回アクセシビリティ研究会 研究報告2020年3月


これらのコーパスを利用して研究等を実施する場合には、上記の参考文献の論文を引用頂けますと幸いです。


## 6. 本件に関する問い合わせ先
lab@ndl.go.jp 

何かお気づきの点がありましたら、お気軽にお問い合わせください。
