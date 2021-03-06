bdf2ttf フォントコンバートツール 説明書
                                                            Since: 21-Aug-2002
                                                                  Version: 1.0
                                                  Author: MURAOKA Taro (KoRoN)
                                                     Last Change: 06-Apr-2015.


説明
  ビットマップフォントであるBDFをTrueTypeフォントに変換するためのツールです。
  変換に際してはTrueTypeの一機能である埋め込みビットマップを用いるため、変換に
  かかる時間は短く、変換後のファイルサイズは小さくなります。ただし変換後のフォ
  ントを埋め込んだBDFの指定サイズ以外で使用した場合、一般的には表示品質に問題
  が生じます。


使用法
  コマンドラインから次のようなコマンドを実行します。

    bdf2ttf [flags] {outname} {info} {infile} [infiles...]

  [flags]
    省略可能。以下のオプションが指定できます。
    -b, --bold          太字フォントを生成します。
    -i, --italic        斜体フォントを生成します。
    --no-autoname       -b/i指定時に、自動的に表示フォント名が修正されるのを回
                        避します。
    --no-stylecheck     -b/i指定時に、整合性チェックを行うのを抑制します。
  {outname}
    必須。出力ファイル名を指定します。例: out.ttf
  {info}
    必須。フォントの名前や著作権表示を設定するための、フォント情報ファイルを指
    定します。書式については後述します。 例: font.ini
  {infile} [infiles...]
    必須。入力となるBDFファイルを指定します。スペースで区切って複数指定するこ
    ともできますが、最低1ファイル以上を指定する必要があります。複数のファイル
    を指定し、かつ異なるファイルに同じ文字に対するグリフが含まれている場合、
    より後者のファイルのものが優先されます。異なるサイズのBDFを指定することも
    可能です。

  注意: 変換には文字コード変換テーブルが必要になります。変換テーブルについては
  後述します。

  (フォント情報ファイル書式)
  フォント情報ファイルはUTF-8エンコーディングで記述する必要があります。フォン
  ト名や著作権表示に英語のみを使用する場合は特に気にする必要はありませんが、日
  本語を使用する場合には留意してください。

  ファイルは1行に1つのキーと値の組み合わせを記す、INIファイルに近い構成になっ
  ています。基本的な書式は:
      key=value もしくは key = value
  の形です。キーと値の中に空白文字を含むことは可能ですが、先頭と末尾の空白は無
  視されます。例:
      greeting practise  =   Hello, World
          キー = greeting practise
           値  = Hello, World
  「#」で始まる行はコメントとして無視されます。

  有効なキーとその意味は以下のとおりになります。
      Copyright   著作権表示(英語)
      Fontname    フォント名(英語)
      Version     フォントバージョン
      Trademark   商標表示(英語)
      CopyrightCP 著作権表示(日本語)
      FontnameCP  フォント名(日本語)
      TrademarkCP 商標表示(日本語)
      Coresize    メインとなるBDFフォントのサイズ
  キーを設定しない場合には、各キーともデフォルトの値が使用されます。Coresizeを
  指定しない時は、フォントサイズが最大のBDFに書かれた情報をフォント作成に利用
  します。

  Coresizeはフォントの縦横比など基本情報の収集に使われます。残念ながらTTFで
  は、サイズに応じてフォントグリフの縦横比を変えることはできません。それに対し
  てbdf2ttfでは縦横比の異なる複数のBDFファイルを指定することができます。ですか
  らbdf2ttfにとってはどのBDFファイルに書かれた縦横比をTTFに出力すれば良いのか
  わかりません。それを指定するのがCoresizeです。BDFファイル内のPIXEL_SIZEが
  Coresizeとして指定できる値です。PIXEL_SIZEが無い場合にはSIZEの第1パラメータ
  が流用されます。

  例えばM+フォントでは10x11 (Coresize=10)と12x13 (Coresize=13)が設定できます。
  設定しないと最大の、この場合は13が使用されます。M+の例では縦横比の違いは小さ
  いので、画面上に反映されるほどではなく問題になりません。しかし縦横比が極端に
  異なるフォントを埋め込む時にはCoresizeの設定が生きてくるでしょう。

  フォント情報ファイルのサンプルはsample.iniを参照してください。

  (文字コード変換テーブル)
  TTFファイルを出力する際には、内部的に文字コードをUNICODEにしています。一方
  BDFでは日本語についてはJISが一般的ですが、英字などはISO-8859シリーズを利用し
  ているものもあり、UNICODEへ変換する必要があります。

  そのためbdf2ttfではunicode.orgが配布している文字コード対応表ファイルを利用・
  加工して、変換の度に文字コード変換テーブルを構築した上で実際の変換を行なって
  います。その対応表ファイルとして、通常はカレントディレクトリの
      Windows表記:  .\ucstable.d\*.TXT
      UNIX表記:     ./ucstable.d/*.TXT
  が検索されます。環境変数UCSTABLEDIRが指定されている場合には
      Windows表記:  %UCSTABLEDIR%/*.TXT
      UNIX表記:     $UCSTABLEDIR\*.TXT
  を検索します。

  使用するファイルは配布アーカイブのucstable.d/以下に見られるように、エンコー
  ド名の先頭の「ISO-」は削除し、アルファベットは大文字に変換、その上で拡張子に
  「.TXT」もしくは「.WIN.TXT」を付ける、という一定の規則でファイル名を決定して
  います。未知のエンコードを利用する場合には、これらの情報を用いて変換テーブル
  を追加することが可能です。

  (太字・斜体フォント)
  通常、太字・斜体のフォントは個別にBDFを用意し、-b/iフラグを指定してそれぞれ
  について埋め込みフォントファイルを作成する必要があります。太字フォントファイ
  ルが提供されないフォントは、OSが自動的に太字グリフを生成するので表示は可能で
  す。しかし斜体フォントは生成されないので表示できません。

  理想的な解決方法は斜体フォントファイルを提供することですが、次善の方法として
  通常グリフを斜体グリフとして強制的に代替利用することができます。その場合には
  通常グリフのコンバート時に、以下の3つのオプションを追加指定してください。
    --italic
    --no-autoname   : -i指定時に、フォント名が自動補正されるのを防ぐ
    --no-stylecheck : -i指定時に、通常文字としても利用可能にする


ソースコード
  bdf2ttfのソースコードはディレクトリsrc以下にavailableです。


フォントの著作権
  bdf2ttfで変換したTrueTypeフォントの諸権利(利用許諾条件及び著作権等)は、オリ
  ジナルとなったBDFフォントに従います。つまり再配布が禁止されているBDFフォント
  を、bdf2ttfを用いてTrueTypeフォントへ変換した場合、そのTrueTypeフォントを第
  三者へ再配布することはできません。

  bdf2ttfの管理者は、bdf2ttfを用いて変換・作成したTrueTypeフォントについては、
  一切の権利と責任を有しません。


更新履歴
  ● 06-Apr-2015 (2.X)
    ライセンスの変更
    連絡先メールアドレスの変更
    日本語のテキストファイルをCP932からUTF-8へ変換
  ● 12-Aug-2005 (2.0)
    スタイルチェックを無効化するオプションを追加
    斜体フォントのサイズがズレる問題を修正
    太字と斜体フォントに対応
    変換部分をリファクタリング
    bdf2_tの導入: 複数のサイズのフォントを埋め込めるように変更
    フォント名に日本語を使えないことがあるバグを修正
  ● 08-Feb-2003 (1.0.005)
    (1.0.006)compile/config_default.mkを追加
    (1.0.005)mkpkgを差し替え
    (1.0.004)MacOSX用TTFを作成するためのテスト
    (1.0.003)MacOSX用のMakefileを追加
    (1.0.002)JISX0208.WIN.TXTのu22beをu22bfに修正
    (1.0.001)bloc及びbdatを出力するように修正
  ● 23-Aug-2002 (1.0)
    公開


-------------------------------------------------------------------------------
                  生きる事への強い意志が同時に自分と異なる生命をも尊ぶ心となる
                               MURAOKA Taro/村岡太郎 <koron.kaoriya@gmail.com>
 vim:set ts=8 sts=2 sw=2 tw=78 et ft=memo:
