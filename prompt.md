# このチャットのプロンプトと出力の記録

## 2026-11-12 PDFのOCR実装

### プロンプト原文

````text
今後このチャットにおけるプロンプトと出力内容の簡便な説明をC:\東工大\3年3Q\デザイン\code\prompt.mdに追記していってください。
またこのチャットにおいてコードを作る場合は可読性を最優先事項としたうえでファイルパスがあるかどうかの確認などの動作上必要のないコードは書かないでください。ユーザーが理解できることが一番のロバスト性であるため、ロバスト性を考慮したコードより可読性を優先したコードを書いてください。

まず最初に、入力fileパスと出力ファイルパスを受け取ったうえで、pdfをocrに掛けてtxtデータ化する方法を教えてください。C:\東工大\3年3Q\デザイン\code\ocr.ipynbに簡単な実装例を書いてください。
````

### 出力の概要

- `ocr.ipynb` にPyMuPDFのインストール、日本語・英語のTesseract学習データのダウンロード、変換関数、パス指定による実行例を作成した。
- 各ページ全体を300 dpiでOCRし、ページ間を空行で区切ったUTF-8テキストを保存する。
- 出力先フォルダの事前用意と同名ファイルの上書きを説明した。
- ノートブックの形式とPythonコードの構文を検証した。実際のPDFを使うOCR実行は未検証。

## 2026-11-12 OCRの言語初期化エラー

### プロンプト原文

````text
このエラーコードの意味を教えて
コードの修正は不要
\---------------------------------------------------------------------------\
FzErrorLibrary                            Traceback (most recent call last)\
Cell In[6], line 8\
&#x20;     4 output_txt_path = r"C:\東工大\3年3Q\デザイン\data\output.txt"\
&#x20;     5 \
&#x20;     6 for university_name in univ_name:\
&#x20;     7     for year in ["2024","2025","2026"]:\
\----> 8         pdf_to_txt(f"../data2/{university_name}\_kakomon/english/{year}.pdf",f"../ocred_txt/{university_name}/{year}.txt")\
\
Cell In[4], line 10\
&#x20;     6     page_texts = []\
&#x20;     7 \
&#x20;     8     with pymupdf.open(input_pdf_path) as pdf:\
&#x20;     9         for page in pdf:\
\---> 10             ocr_textpage = page.get_textpage_ocr(\
&#x20;    11                 language="jpn+eng",\
&#x20;    12                 dpi=300,\
&#x20;    13                 full=True,\
\
File c:\東工大\3年3Q\\.venv\Lib\site-packages\pymupdf\\\_\_init\_\_.py:12896, in Page.get_textpage_ocr(self, \*args, \*\*kwargs)\
&#x20; 12895 def get_textpage_ocr(self, \*args, \*\*kwargs):\
\> 12896     return utils.get_textpage_ocr(self, \*args, \*\*kwargs)\
\
File c:\東工大\3年3Q\\.venv\Lib\site-packages\pymupdf\utils.py:446, in get_textpage_ocr(page, flags, language, dpi, full, tessdata)\
&#x20;   444 # if OCR for the full page, OCR its pixmap @ desired dpi\
&#x20;   445 if full:\
\--> 446     return full_ocr(page, dpi, language, flags)\
&#x20;   448 # For partial OCR, make a normal textpage, then extend it with text that\
&#x20;   449 # is OCRed from the rest of page.\
&#x20;   450 return partial_ocr(page, dpi, language, flags)\
\
File c:\東工大\3年3Q\\.venv\Lib\site-packages\pymupdf\utils.py:343, in get_textpage_ocr.\<locals>.full_ocr(page, dpi, language, flags)\
&#x20;   340 pix = page.get_pixmap(dpi=dpi)\
&#x20;   341 # create a 1-page PDF with an OCR text layer.\
&#x20;   342 ocr_pdf = pymupdf.Document(\
\--> 343     stream=pix.pdfocr_tobytes(\
&#x20;   344         compress=False,\
&#x20;   345         language=language,\
&#x20;   346         tessdata=tessdata,\
&#x20;   347     ),\
&#x20;   348 )\
&#x20;   349 ocr_page = ocr_pdf.load_page(0)\
&#x20;   350 unzoom = page.rect.width / ocr_page.rect.width\
\
File c:\東工大\3年3Q\\.venv\Lib\site-packages\pymupdf\\\_\_init\_\_.py:14540, in Pixmap.pdfocr_tobytes(self, compress, language, tessdata)\
&#x20; 14538 from io import BytesIO\
&#x20; 14539 bio = BytesIO()\
\> 14540 self.pdfocr_save(bio, compress=compress, language=language, tessdata=tessdata)\
&#x20; 14541 return bio.getvalue()\
\
File c:\東工大\3年3Q\\.venv\Lib\site-packages\pymupdf\\\_\_init\_\_.py:14518, in Pixmap.pdfocr_save(self, filename, compress, language, tessdata)\
&#x20; 14516 out = JM_new_output_fileptr( filename)\
&#x20; 14517 try:\
\> 14518     mupdf.fz_write_pixmap_as_pdfocr( out, pix, opts)\
&#x20; 14519 finally:\
&#x20; 14520     out.fz_close_output()\
\
File c:\東工大\3年3Q\\.venv\Lib\site-packages\pymupdf\mupdf.py:57280, in fz_write_pixmap_as_pdfocr(out, pixmap, options)\
&#x20; 57276     r"""\
&#x20; 57277     Class-aware wrapper for \`::fz_write_pixmap_as_pdfocr()\`.\
&#x20; 57278         Write a (Greyscale or RGB) pixmap as pdfocr.\
&#x20; 57279     """\
\> 57280     return \_mupdf.fz_write_pixmap_as_pdfocr(out, pixmap, options)\
\
FzErrorLibrary: code=3: Tesseract language initialisation failed
````

### 出力の概要

- PDFの読み込みとページの画像化の後、TesseractがOCR用言語データを読み込んで初期化する段階で失敗したことを説明した。
- `jpn+eng` は日本語と英語の指定で、学習データの保存先の指定違い、データの欠落・破損・互換性などが原因候補になる。提示されたトレースだけでは原因を特定できない。
- PyMuPDF公式資料を確認し、コードは変更せず説明した。

## 2026-11-12 OCRノートブックの修正

### プロンプト原文

````text
C:\東工大\3年3Q\デザイン\code\ocr.ipynbをみて修正して
````

### 出力の概要

- 日本語を含む絶対パスを学習データの保存先として渡すと初期化エラーが再現し、相対パス `tessdata` ではOCRが成功することを確認した。`.resolve()` を取り除いた。
- 大学・年度ごとの一括変換を維持し、出力フォルダの作成を追加した。未使用のパス変数を削除し、入出力パスを読みやすく分けた。
- 作業ディレクトリを `デザイン/code` とする説明を追加し、過去の実行結果をクリアした。
- 修正した関数で法政大学2024年PDFの先頭ページをOCRし、UTF-8テキスト498文字の保存を確認した。全12ファイルの一括変換は未実行。

## 2026-11-12 記録形式の変更

### プロンプト原文

````text
プロンプトはそのまま転記して。概要じゃなくていい。出力は概要でよい
````

### 出力の概要

- 過去3件の依頼を原文に置き換え、今回の依頼も原文で追記した。エラーの質問に添付されたトレースバックも転記した。
- 今後はプロンプトを原文のまま記録し、出力は概要を記録する。

## 2026-11-12 OCRテキストの整形方法

### プロンプト原文

````text
出力txtから日本語部分を削除して英語部分のみにして無駄な空欄なども削除する方法はないですか？
````

### 出力の概要

- 正規表現でひらがな・カタカナ・漢字・日本語の句読点を削除し、行内の余分な空白と空行を整理して別のtxtに保存する関数を回答した。
- 数字・記号は残ること、日本語部分のOCR誤認識で生じた英字は残ることを説明した。

## 2026-11-12 txtファイルを変数に読み込む

### プロンプト原文

````text
textという変数にtxtファイルから読み込んだ文字データとして格納したい。どうすればいいですか？
````

### 出力の概要

- `Path.read_text(encoding="utf-8")` でtxtファイル全体を読み込み、文字列として変数 `text` に代入する例を示した。

## 2026-11-12 英語の形態素分析結果の調査

### プロンプト原文

````text
続いて用意したtxtデータからのnlpまでを行いますC:\東工大\3年3Q\デザイン\code\txt2vector.ipynbを見てください。C:\東工大\3年3Q\デザイン\ocred_txt\waseda\2024.txtの結果を形態素分析したはずなのに形態素分析されていない単語もあります。どうしてか確認してみてください。
````

### 出力の概要

- 実際の入力全文を既存のspaCy英語モデルで再解析し、原形化と品詞付与が動作していることを確認した。
- 元txtの1行目に `froma`、23行目に `ofhelplessness`、51行目に `ofthe`、553行目に `oftrustees` があり、空白が欠落した語が1トークンのまま解析されることを確認した。空白を補った比較例では分割された。
- `security` のように原形と表記が同じ単語や、`disengagement` のような派生語は、そのままの形でも解析済みであることを説明した。
- `ocr.ipynb` の整形結果は `housei/2024_english.txt` に保存され、分析セルは元の `waseda/2024.txt` を読んでいることを指摘した。調査にとどめ、ノートブックは変更していない。

## 2026-11-12 第一文の単語が表示されない件の再確認

### プロンプト原文

````text
そうではなくて、例えば早稲田2024の大問一の第一文はin 2019 there wereから始まるのに、それらの単語がnlpの結果に見当たらないのです。txtファイルを確認したらちゃんとスペースがあることは確認できました。
````

### 出力の概要

- 前回は指摘の対象を取り違えていたため、第一文の4語を指定して再確認した。
- ノートブックに保存済みの実行結果と再解析結果の両方に `In / in / ADP / IN`、`2019 / 2019 / NUM / CD`、`there / there / PRON / EX`、`were / be / VERB / VBD` があることを確認した。
- 出力は約10.7万文字あり、画面上での出力省略が原因候補であることを説明した。画面上の省略状態は未確認。
- 第一文付近に絞って表示するコード例を回答した。

## 2026-11-12 英語の品詞ラベル

### プロンプト原文

````text
日本語ではNOUN,VERB,ADJだけど、英語だとどうなるの
````

### 出力の概要

- spaCyの `token.pos_` では英語も `NOUN`（名詞）、`VERB`（動詞）、`ADJ`（形容詞）を使うことを説明した。
- 英語での具体例と3品詞を抽出するコードを示し、固有名詞の `PROPN`、助動詞の `AUX` は別ラベルであることを補足した。

## 2026-11-12 nx2pyvis_GのNameError

### プロンプト原文

````text
\---------------------------------------------------------------------------\
NameError                                 Traceback (most recent call last)\
Cell In[30], line 1\
\----> 1 pyvis_G = nx2pyvis_G(G)\
&#x20;     2 pyvis_G.show_buttons()\
&#x20;     3 pyvis_G.show("network.html")\
\
NameError: name 'nx2pyvis_G' is not defineってどういう事？
````

### 出力の概要

- `nx2pyvis_G` という名前が現在の実行環境で定義されていないことを示すエラーだと説明した。
- 関数の定義セルの未実行、カーネル再起動、定義・インポートの欠落、名前の誤記が原因候補になることを説明した。

## 2026-11-13 一文ごとの動詞数を数える関数

### プロンプト原文

````text
C:\東工大\3年3Q\デザイン\code\txt2vector.ipynbのprocess_sentences_in_batchesを使えばsentence毎に分解してリスト化できると思うので、そのsentence毎のlistに対して動詞の数を数えてください。要は一文の中に動詞が何個あるのかを求める関数を書いてください。関数の引数は入力ファイルでお願いします
````

### 出力の概要

- `txt2vector.ipynb` の末尾に、入力txtパスだけを引数に取る `count_verbs_per_sentence(input_file)` と実行例を追加した。戻り値は `sentence` と `verb_count` を持つ辞書のリスト。
- 既存の `process_sentences_in_batches` は行単位で解析するため、追加関数では全文を解析して `doc.sents` で区切り、改行をまたぐ文を扱うようにした。
- 動詞数は `token.pos_ == "VERB"` の個数とし、助動詞の `AUX` は集計対象外とした。
- 改行をまたぐ例文で動詞数2、続く例文で1、助動詞を含む例文で1となることを確認した。早稲田2024の `part1.txt` でも実行し、114件の結果を取得した。
- 文境界と動詞判定はspaCyの推定に依存し、見出しやOCR誤り、品詞の誤判定が結果に影響することを説明した。

## 2026-11-13 動詞数の返り値をDataFrameに変更

### プロンプト原文

````text
返り値をdfにしてくれると嬉しいです。
````

### 出力の概要

- `count_verbs_per_sentence` の返り値を、`sentence` と `verb_count` の2列を持つpandas DataFrameに変更した。
- 実行例と説明を更新し、関連セルの古いリスト形式の出力をクリアした。
- 早稲田2024の `part1.txt` で114行2列のDataFrameが返ることを確認した。

## 2026-11-13 既存の文分割関数を使用

### プロンプト原文

````text
doc.sentsをそのまま使うんじゃなくて、process_sentences_in_batchesを通して
````

### 出力の概要

- `count_verbs_per_sentence` で `process_sentences_in_batches(input_file, nlp, is_lemmatized=False)` を呼び、返された文のリストから `VERB` の個数を数えるように変更した。
- 返り値は `sentence` と `verb_count` の2列を持つDataFrameとし、早稲田2024の `part1.txt` で動作確認した。
- 既存関数に従って行単位で解析されることと、文分割関数の定義セルを先に実行することを説明に記載した。

## 2026-11-13 文分割関数の呼び出し箇所の確認

### プロンプト原文

````text
count_verbs_per_sentenceの中でprocess_sentences_in_batchesを呼び出しているようには見えないですが、どこで**process_sentences_in_batchesを利用しているのですか？**
````

### 出力の概要

- 保存済みノートブックの関数定義を確認し、`nlp = spacy.load("en_core_web_sm")` の直後にある `sentences = process_sentences_in_batches(input_file, nlp, is_lemmatized=False)` が呼び出し箇所であることを示した。
- 返された文のリストを `for sentence in sentences` で順に処理して動詞を数えていることを説明した。

## 2026-11-14 全文から文を取得する実装に戻す

### プロンプト原文

````text
C:\東工大\3年3Q\デザイン\code\txt2vector.ipynbでprocexx_sentences_in_batchesを使うように指定したけど、やっぱりprocexx_sentences_in_batchesを使う前のコードにして下さい。
````

### 出力の概要

- `count_verbs_per_sentence` を、入力txt全体を読み込んで `nlp(text)` で解析し、`doc.sents` で一文ずつ動詞数を集計する実装に戻した。
- 返り値は `sentence` と `verb_count` のDataFrame。説明文も更新し、早稲田2024の `part1.txt` で114行2列の結果を確認した。

## 2026-11-14 3トークン以上の文を抽出

### プロンプト原文

````text
早稲田の問題ですが、選択肢の部分の最初にa.b.c.d.って書いてあってこれが無駄に文章として検出されてます。なので対策として.含めて3語以上ないと文章としないことにしたいけど、どうすればいいかな。共通テストにも同じ処理を加えたい。入力データをいじるというより、一度文章化したうえで、3語以上の文章のみを取り出して処理をするという形を取りたい
````

### 出力の概要

- `count_verbs_per_sentence` で文分割後に、句読点を含めて3トークン以上の文を抽出し、その文だけ動詞数を集計するよう変更した。空白・改行のトークンは数えない。
- 既存の実行例から早稲田・共通テスト双方に適用されることを確認し、実データで単独の `a.`、`b.`、`c.`、`d.` が結果に含まれないことを検証した。
- spaCyのトークン単位を使うため、`a.` が1トークンでも2トークンでも除外されることを説明した。

## 2026-11-14 動詞数が0になる文の調査

### プロンプト原文

````text
早稲田の文章だけど、どうしてこいつらがverb counts=0になってるの
````

添付画像に、動詞数が0になった10件のsentence列が表示されている。

### 出力の概要

- 実データの各文についてspaCyの品詞判定を確認した。現在は `VERB` のみを数えているため、`are` が `AUX` と判定された文は0になる。
- `lead` が `NOUN`、`Encouraging` が `PROPN`、`refer` が `NOUN` と判定される箇所では品詞誤判定が原因であると説明した。
- 名詞句、見出し、引用文献には動詞がない例があることと、並べ替え問題の語群は通常の文として解析しにくいことを説明した。
- `VERB` と `AUX` の両方を数える方法を示し、品詞誤判定の修正には別の対応が必要だと説明した。

## 2026-11-14 Part II.が残る理由

### プロンプト原文

````text
Part II.とかは3語以下の条件に抵触しているはずなのに残っているのはなんで。
````

### 出力の概要

- `Part II.` はspaCyで `Part`、`II`、`.` の3トークンに分割されることを確認した。
- 現在は3トークン以上を残す `>= 3` なので、ちょうど3トークンの文も残ることを説明した。3以下を除外する場合の条件 `> 3` を示した。

## 2026-11-15 動詞数を文数で割った列を追加

### プロンプト原文

````text
verb_countを全体の文の数で除算した結果をverb_count_ratioっていうcolumnに格納して
````

### 出力の概要

- `count_verbs_per_sentence` の返すDataFrameに、各行の `verb_count` を `len(df)` で割った `verb_count_ratio` 列を追加した。
- 分母は文長の条件で抽出した後の集計対象の文数とした。早稲田2024のtxtで計算結果を確認した。

## 2026-11-15 ヒストグラムを相対度数表示に変更

### プロンプト原文

````text
ヒストグラムで階級ごとの階級値を表示すんじゃなくて、階級値を階級値の総和で割ったものを表示したい
````

### 出力の概要

- 棒の高さを各階級の度数の総和で割る相対度数の要望と解釈し、ヒストグラムの縦軸を相対度数、横軸を一文の動詞数 `verb_count` に変更した。
- 各文に `1 / len(df)` の重みを設定した。共通テスト・早稲田の2024〜2026年で共通の階級を使い、全データを含む範囲とした。
- 存在しない列 `sentence_verb_counts` を参照する描画処理を修正し、6つのグラフをループで描画する形にまとめた。

## 2026-11-15 6系列のヒストグラムをまとめる

### プロンプト原文

````text
階級幅は1づつ、共通テストも早稲田もグループ化して一つのヒストグラムとして書きたい。共通テストと早稲田で色は変えたい。各階級で左から共通テスト24,25,26早稲田24,25,26の順で書いてほしい
````

### 出力の概要

- 階級幅1の共通の階級に、共通テスト2024・2025・2026、早稲田2024・2025・2026の順で6本の棒を並べた1つのヒストグラムに変更した。
- 共通テストを青、早稲田をオレンジとし、各系列の縦軸は相対度数とした。

## 2026-11-15 re.subの引数によるTypeError

### プロンプト原文

````text
\---------------------------------------------------------------------------\
TypeError                                 Traceback (most recent call last)\
Cell In[21], line 6\
&#x20;     2 for u_name in clean_name:\
&#x20;     3     for year in [2024,2025,2026]:\
&#x20;     4         input_fn=f"../ocred_txt/{u_name}/{year}.txt"\
&#x20;     5         output_fn=f"../ocred_txt/{u_name}/{year}\_english.txt"\
\----> 6         clean_ocr_text(input_fn,output_fn)\
\
Cell In[20], line 9\
&#x20;     5 def clean_ocr_text(input_txt_path, output_txt_path):\
&#x20;     6     text = Path(input_txt_path).read_text(encoding="utf-8")\
&#x20;     7 \
&#x20;     8     # ひらがな・カタカナ・漢字・日本語の句読点を削除する\
\----> 9     text = re.sub(r"[ぁ-ゖゝ-ゟァ-ヺヽ-ヿー㐀-鿿々〆〇、。「」『』【】・]", "", "(",")",text)\
&#x20;    10 \
&#x20;    11     cleaned_lines = []\
&#x20;    12 \
\
File \~\AppData\Local\Programs\Python\Python312\Lib\re\\\_\_init\_\_.py:186, in sub(pattern, repl, string, count, flags)\
&#x20;   179 def sub(pattern, repl, string, count=0, flags=0):\
&#x20;   180     """Return the string obtained by replacing the leftmost\
&#x20;   181     non-overlapping occurrences of the pattern in string by the\
&#x20;   182     replacement repl.  repl can be either a string or a callable;\
&#x20;   183     if a string, backslash escapes in it are processed.  If it is\
&#x20;   184     a callable, it's passed the Match object and must return\
&#x20;   185     a replacement string to be used."""\
\--> 186     return \_compile(pattern, flags).sub(repl, string, count)\
\
File \~\AppData\Local\Programs\Python\Python312\Lib\re\\\_\_init\_\_.py:300, in \_compile(pattern, flags)\
&#x20;   298 if not \_compiler.isstring(pattern):\
&#x20;   299     raise TypeError("first argument must be string or compiled pattern")\
\--> 300 if flags & T:\
&#x20;   301     import warnings\
&#x20;   302     warnings.warn("The re.TEMPLATE/re.T flag is deprecated "\
&#x20;   303             "as it is an undocumented flag "\
&#x20;   304             "without an obvious purpose. "\
&#x20;   305             "Don't use it.",\
&#x20;   306             DeprecationWarning)\
\
TypeError: unsupported operand type(s) for &: 'str' and 'RegexFlag'
````

### 出力の概要

- `re.sub` の第3引数が置換対象文字列、第4引数が置換回数、第5引数がフラグなので、追加した括弧とtextが誤った引数に渡されていることを説明した。
- 日本語と半角括弧を削除する場合は、正規表現の角括弧内に `()` を追加し、第3引数にtextを渡す修正例を示した。括弧内の英語は保持される。

## 2026-11-15 括弧が削除されない問題の修正

### プロンプト原文

````text
入力ファイルの最初がこれで
2024\
bas ME FE PAK AA AS ak BR EY\
af\
2h.\
HH\
1.\
, ()\
()\
,\
()\
2.\
()\
, RO\
[Ml]\
[ ] | (7) |\
,1\
,\
(7)\
)\
@\
3.\
()\
, HB\
3\
9\
(Se Sons\
GC\
4.\
() ()\
@\
,\
5\. 8\
8\
)\
6\. RIMPORA,\
5\
7\.
出力ファイルの最初がこれ2024\
bas ME FE PAK AA AS ak BR EY\
af\
2h.\
HH\
1.\
, ()\
()\
,\
()\
2.\
()\
, RO\
[Ml]\
[ ] | (7) |\
,1\
,\
(7)\
)\
@\
3.\
()\
, HB\
3\
9\
(Se Sons\
GC\
4.\
() ()\
@\
,\
5\. 8\
8\
)\
6\. RIMPORA,\
5\
7.\
1.\
,
()がうまく削除できてない
````

### 出力の概要

- `ocr.ipynb` の正規表現が `[...]()` となっており、括弧が文字クラス外の空のグループとして解釈されていたことを確認した。
- `[...]` の内側に `()` を移し、日本語と半角括弧を削除できるよう修正した。`()`、`(7)`、閉じていない括弧を含むサンプルで削除を確認した。
- 関数定義セルと変換ループの再実行が必要であることを説明した。

## 2026-11-15 記号追加後の正規表現を修正

### プロンプト原文

````text
確認してほしいけど、まだ消えてないです。
````

### 出力の概要

- 現在の正規表現が `[...]|[]]` に相当する文字クラス末尾の構造を持ち、削除対象の直後に `]` がある場合だけ一致する状態になっていたことを確認した。
- 文字クラス内の角括弧を `\[`、`\]` に修正した。
- 現在の変換ループの対象である法政・慶應・日大の2024〜2026年、計9件の `_english.txt` を再生成し、半角・全角の丸括弧、角括弧、@、縦棒が残っていないことを確認した。

## 2026-11-15 大学・年度別のリッジラインプロット

### プロンプト原文

````text
C:\東工大\3年3Q\デザイン\code\txt2vector.ipynbを見てもらえれば早稲田、慶応、共通テスト、法政、日大の単語数データが用意されていると思います。これらを一つのリッジラインプロットにしたいです。上から順番に早稲田2024,2025,2026慶應法政日大共通でお願いします。同じ大学同士は同じ色にまとめてくれると嬉しいです。追加でセルを作ってそこに書いてください
````

### 出力の概要

- ノートブック末尾にリッジラインプロット用のコードセルを追加した。
- 既存の大学別DataFrameにある `verb_count`（一文の動詞数）を使った。依頼中の「単語数」に対し、既存データは動詞数であることを説明した。
- 上から早稲田・慶應・法政・日大・共通テスト、各グループ内は2024・2025・2026年の順とし、同じ大学を同じ色にした。
- カーネル密度推定で分布を滑らかに表示し、15段のプロットを作成した。

## 2026-11-15 リッジラインプロットのx軸範囲

### プロンプト原文

````text
リッジラインプロットのx軸の範囲をいじる方法を教えて
````

### 出力の概要

- `axes[-1].set_xlim(0, 10)` のように下限・上限を指定する方法を示した。`sharex=True` により全段に適用される。
- 目盛り間隔を1にする場合の `set_xticks` の例を示した。

## 2026-11-15 df_kakomonからリッジラインプロット

### プロンプト原文

````text
df_kakomonでリッジラインプロットを書いてください
````

### 出力の概要

- 末尾に `df_kakomon` の `univ_name` と `year` でデータを抽出し、`verb_count` の分布を描くコードセルを追加した。
- 上から早稲田・慶應・法政・日大・共通テスト、各グループ内を2024・2025・2026年の順とし、大学ごとに色を統一した。
- 現在のプロットに合わせてx軸の表示範囲を0〜10、目盛りを1刻みとした。

## 2026-11-15 箱ひげ図への変更

### プロンプト原文

````text
df_kakomoでバイオリンプロットの代わりに箱ひげを書いてください
````

### 出力の概要

- `df_kakomo` は既存変数 `df_kakomon` と解釈し、そのデータのバイオリンプロットを `px.box` による箱ひげ図へ変更した。
- 横軸を大学、縦軸を `verb_count`、色を年度としてグループ表示した。年度別の箱ひげ図を生成できることを確認した。

## 2026-11-15 箱ひげ図のひげを最小値・最大値に変更

### プロンプト原文

````text
外れ値を出さないように、つまりすべてのデータの中の最大値四分位数で箱ひげを書くことはできない？
````

### 出力の概要

- 大学・年度ごとの全データから最小値、第1四分位数、中央値、第3四分位数、最大値を計算し、ひげを最小値〜最大値とする箱ひげ図へ変更した。
- Plotlyの `lowerfence` と `upperfence` に最小値・最大値を渡し、`boxpoints=False` を指定した。最大値が見えるよう縦軸を自動範囲にした。
- 最大値30を含むサンプルで、ひげと四分位数の指定値を確認した。

## 2026-11-15 箱ひげ図のラベルを日本語化

### プロンプト原文

````text
C:\東工大\3年3Q\デザイン\code\txt2vector.ipynbのように箱ひげ図のラベルを日本語に変えたかったんだけど、どうすればいいの？
````

### 出力の概要

- `go.Box` に追加された `labels` 引数が未対応であることを説明し、大学コードから日本語名への辞書と `Series.map` を使う形に修正した。
- 2つの箱ひげ図の大学名、軸タイトル、年度の凡例を日本語化し、サンプルでラベル変換と図の構築を確認した。

## 2026-11-17 箱ひげ図の大学の並び順

### プロンプト原文

````text
import plotly.graph_objects as go\
\
\# Compute the minimum, quartiles, and maximum for each university and year.\
label_name = {"housei": "法政", "keio": "慶應", "kyotu": "共通テスト",\
&#x20;             "nitidai": "日大", "waseda": "早稲田"}\
box_stats = (\
&#x20;   df_kakomon.groupby(["univ_name", "year"])["verb_count"]\
&#x20;   .agg(\
&#x20;       minimum="min",\
&#x20;       q1=lambda values: values.quantile(0.25),\
&#x20;       median="median",\
&#x20;       q3=lambda values: values.quantile(0.75),\
&#x20;       maximum="max",\
&#x20;   )\
&#x20;   .reset_index()\
)\
\
fig = go.Figure()\
for year in sorted(box_stats["year"].unique()):\
&#x20;   stats = box_stats[box_stats["year"] == year]\
&#x20;   fig.add_trace(go.Box(\
&#x20;       name=f"{year}年",\
&#x20;       x=stats["univ_name"].map(label_name),\
&#x20;       q1=stats["q1"],\
&#x20;       median=stats["median"],\
&#x20;       q3=stats["q3"],\
&#x20;       lowerfence=stats["minimum"],\
&#x20;       upperfence=stats["maximum"],\
&#x20;       boxpoints=False,\
&#x20;   ))\
\
fig.update_layout(boxmode="group", xaxis_title="大学・試験", yaxis_title="1文ごとの動詞数", legend_title_text="年度",title="大学毎の1文に含まれる動詞数の比較",title_xanchor="left")\
fig.show()\
\
で箱ひげの並び順を左から順に早稲田、慶応、法政、日大、共通テストにするにはどうすればいい
````

### 出力の概要

- `fig.show()` の前に `fig.update_xaxes(categoryorder="array", categoryarray=["早稲田", "慶應", "法政", "日大", "共通テスト"])` を追加する方法を示した。
- カテゴリ名は `label_name` の変換後の表記に合わせて指定することを説明した。
