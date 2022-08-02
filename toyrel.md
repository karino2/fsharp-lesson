---
title: "第二回 Relational Algebraの処理系、ToyRelを作ろう"
layout: page
---
第二回では [LEAP RDBMS : Home](http://leap.sourceforge.net/) のようなrelational algebraの処理系を作ってみます。
いわゆるリレーショナルデータベースのおもちゃを作る感じです。
名前をtoyのrelational algebraという事でToyRelと呼ぶ事にします。

なおLEAPを題材にRelational Algebraやデータベースの基礎を教える本として、以下があります。

<iframe sandbox="allow-popups allow-scripts allow-modals allow-forms allow-same-origin" style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//rcm-fe.amazon-adsystem.com/e/cm?lt1=_blank&bc1=000000&IS2=1&bg1=FFFFFF&fc1=000000&lc1=0000FF&t=karino203-22&language=ja_JP&o=9&p=8&l=as4&m=amazon&f=ifr&ref=as_ss_li_til&asins=B00OD5CB50&linkId=031b79722ee8b82c89df4ef320cc8118"></iframe>

ちょっと高くて古い本ですが、ちゃんと基本的なrelational modelやrelational algebra関連の理論がまとまっていながら不要に難しいと思う所はちゃんとカットしていたりと悪くない本なので興味があったらどうぞ。
自分は第二回の内容を書く為に買いました。

なお、第二回をやるのに特にこの本の内容を前提にはしません。
上のリンクから買ってくれるとちょっとアフィリエイトが入って嬉しいくらい。
牛の本とか持ってるなら買わなくてもいいと思います（自分は昔読んだが捨ててしまって手元に無い…）

## ToyRelとrelational modelとrelational algebra

RDBの基礎は、relational modelとrelational algebraです。
relational algebraについてはWikipediaの記事でもだいたいは理解出来るでしょう。

[Relational algebra - Wikipedia](https://en.wikipedia.org/wiki/Relational_algebra)

より詳細なRelational algebraについては上記書籍や、ググって引っかかる講義資料などを参照してもらうとして、
ここでは実装上の簡単な説明を行います。

relational modelとは「データベースのテーブルというのは、なんらかの制約を満たしたタプルの集合とする」というモデルです。
タプルというのはテーブルのrowの事。
リレーショナルモデルとは、rowが重複しないテーブルと思っておけばだいたいよろしい。
このテーブルを一般化した概念をリレーションと呼びます。

この重複しないという制約のおかげで集合論的にリレーションを定義出来る、
というのがrelational modelのポイントその1です。

そしてrelational algebraというのはこのrelational model同士を演算して新しいrelational modelを作るという合成方法です。
relational algebraの各演算がまたrelational modelを返すので、この演算は代数をなす、という事ですが、
実装的に重要なのは、演算の結果もテーブルも区別がなく同じrelational modelになる、という事です。

ToyRelとしては、relational modelとしてcsvファイルを使います。relationの名前として、csvファイルのbasename、つまり拡張子を取り除いたものを使います。
カラムの型としてはとりあえずIntとStringだけ考えています（Floatもサポートしたくなったら追加するかも）。
最初から提供するcsvはリレーションとしてvalidなものだけに限定し、そしてrelational algebraの結果生成されるrelationも必ずvalidなものだけを吐くとします（つまりrowの重複は毎回取り除く）。

実装するrelational algebraとしては、以下を実装する事にします。

- project
- restrict (第一回でfilterと呼んでたもの、RAではselectと呼ばれる事も多い)
- union
- difference
- intersect
- rename
- product
- join

また、relational algebra以外のコマンドとしては以下を実装します。

- use (db変更)
- print
- list

本格的なrelational algebra処理系となるので、全仕様を最初に詳細に決めるのは難しいし理解をするのも大変です。
そこで、これを徐々にすすめていく過程で何を作るかを明らかにしていこうと思います。


{% capture comment_ra %}
**みんな大好きRelational Algebra**  

実装の上ではあまり理論を知る必要も無いのですが、数学的には非常に単純なので理解も容易で、
けれどその表現力の高さや実社会に与えた影響の大きさという点からも、
Relational Algebra（略してRA）は学ぶ意義の多いものに思います。

現代の観点からするとテーブルを抽象的にテーブルと考えるという事自体が普通に感じられすぎて、
どこが革新的なのかが分かりにくい。
けれど当時の、皆が生の多次元配列をいじってこうしたシステムを実装していて、
実装言語の違いや保存の方法などによってうまれる配置やアクセス方法の違いから、
一般的な記述をするのは簡単な事ではありません。

そうした時に、ドメインの間を動くタプルとその制約という形で全ての必要となる「なにか」を記述出来る、
というのは、単純でありながら十分な力を持ったエレガントなモデリングであり、
こんな単純なモデルで非常に多くの問題が記述出来るというのは驚きに値します。

以下からリンクされているTuring Award Lectureのpdfを見ると、当時の状況とこの理論の距離感のようなものが垣間見えます。

[Edgar F. Codd - A.M. Turing Award Laureate](https://amturing.acm.org/award_winners/codd_1000892.cfm)

Relational Modelのポイントとしては、テーブルを

- タプルが所属するドメインの全集合
- リレーションが満たすべき制約としてのpreposition

の２つと考える事です。
例えばサイコロの目の集合という全集合に対して、
「偶数の目」というprepositionを満たす集合、というのは、
`{2, 4, 6}` という集合と同じとなります。
これは極めて普通の集合論の言葉でありながら、
プログラムの実装としてもタプルとそのフィルタという形で自然に実装が出来ます。

この数学としての単純さと実際の実装の近さというバランスの良さがRelational Modelが優れていた所でしょう。

集合論として単純な定式化が出来たおかげで、その時に使われていたデータベースシステムがサポートする演算達に対して、
それと同じ表現力を持つ最小の基底の数やその基底を調べる事が出来たり、
新しい演算を考えた時にそれが既存の演算で表現出来るか調べたり、
類似のモデル同士の表現力が等価なのかどちらかがより広い範囲をカバーしているのかなどが証明出来たり出来るようになりました。
また複雑な合成を変形しても等価になるような最適化に使える変形などを調べるなどのご利益もあります。

数学としては非常に入門的なdifferenceやunionなどばかりで、直和などの良く分からん難解なものにも慣れ親しんでいる皆様からすると、
おもちゃみたいな代数でしょう。
プログラムにおいて数学を活かす難しさというのは、出来上がった数学の難解さよりは、
現実の問題に直面して実装に引きずられがちな所に、
一歩下がって数学的な構造を見抜く所にあるという事が読み取れる例です。

実装する側としては、一般的な（数学の意味での）ドメインの上を動くタプルに対して、
ある種のフィルタを適用したものとしてリレーションが生成出来るという事に着目しておくと、
以下で様々な機能を実装していく時に統一的に考える事が出来ます（別にそんな事考えなくても実装は出来ますが）。
全てのリレーションがこの形で統一出来るというのは素晴らしい発明なのですが、
実装する側としてはそんな難しい事は考えなくても、全部同じ枠組みで実装すれば良いだけです。

ちなみにRelational Modelではrowをタプルと呼んでいて、カラムをattributeと呼んでいます。一応知っておくと文献を読む時に楽です。

なお、クエリとしてはほぼ完成されているRelational ModelとRelational Algebraですが、
テーブルの設計としては少し抽象的過ぎて重要なセマンティクスの幾つかを失ってしまってもいます。
そこでテーブル設計としてはRelational Modelの問題点を解決した後発のEntity-Relationshipモデルが一般的に使われています。
ERとかEAR(Entity-Attribute-Relationship)と良く略されていますね。
ER図とかERDとかは実務でも良く出て来るので見た事がある人も多いと思います。このRelationshipがRelationと似ているので混乱しやすいので初学者は注意が必要です。
両者は関連しているので類似している訳で、primary keyを導入する事でEntityをRAの意味でのRelationで表現するというのはテーブル設計の基礎ですらありますが。

この辺の理論的な話を真面目に学ぶならDBの教科書、たとえば先述のTheory and Practice of Relational Databaseは良い本だと思います。
ERDに関しては理論だけでは無くて訓練も重要で、そうした演習書としては楽々ERDレッスンが優れていると思います。

- [楽々ERDレッスン 第1回：「お持ち帰りご注文用紙」編：CodeZine（コードジン）](https://codezine.jp/article/detail/154)
- [楽々ERDレッスン 第2回：「図書館の予約申込書」編：CodeZine（コードジン）](https://codezine.jp/article/detail/175)

この記事を拡充して書籍化された以下の本を若かりし頃、頑張って練習しました。Kindle化されないですかね〜。

<iframe sandbox="allow-popups allow-scripts allow-modals allow-forms allow-same-origin" style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//rcm-fe.amazon-adsystem.com/e/cm?lt1=_blank&bc1=000000&IS2=1&bg1=FFFFFF&fc1=000000&lc1=0000FF&t=karino203-22&language=ja_JP&o=9&p=8&l=as4&m=amazon&f=ifr&ref=as_ss_li_til&asins=4798110663&linkId=d16b70a4d08fd1e3da37295ee773b78f"></iframe>

なお、このToyRelではあえてRelational Modelそのままに近いものを実装していきます。
EAR的な要素は一切出さないように気をつけています。

{% endcapture %}
{% include myquote.html body=comment_ra %}


## wikipediaディレクトリのデータ紹介

Wikipediaの[Relational algebra - Wikipedia](https://en.wikipedia.org/wiki/Relational_algebra)と同じデータを、
sources/data/wikipedia下に置きました。

詳細はWikipedia側を見て下さい。

なお、先述の書籍、Theory and Practice of Relational Databasesのサンプルのデータもsources/data/tandpに作ってあります。
ある程度実装が進んだらこちらも触ってみて下さい。
詳細は[tandpデータの解説と練習問題など](tandp.md)を参照の事。

## こんなものを動かしたい、というイメージ

以下のような処理を実行すると、ランダムに生成された名前のrelationが保存されます。

```
> project (シラバス) 専門, 学年
Relation zzybac returned.
```

名前はこの場合zzybac.csvというファイルになります。
リレーションはprintで表示出来ます。

```
> print zzybac
専門   学年
----  ----
数学    1
物理    1
数学    2
```

同じ行は削除される事に注意。

なお、この第二回では基本的に、入力を`>`のあとの行で行って、結果は`>`の無い行として表示します。
終盤では実際にそうした入出力を実装するつもりですが、この時点では表記上の便宜と思っておいてください。

リレーションは変数に入れる事もできる。
以下のような文を実行すると、

```
> シラバス_専門_学年 = project (シラバス) 専門, 学年
```

「シラバス_専門_学年.csv」というファイルが生成されて、中身は先程と同じ。

differenceなどはwikipediaディレクトリのデータを使うと以下のような感じになる。（Wikipediaの記事の方も参照の事）

```
> emp_dept = project (Employee) DeptName
> dept_dept = project (Dept) DeptName
> (emp_dept) difference (dept_dept)
Relation aabzzc returned.

> print aabzzc
DeptName
----
Human Resources
```

以下のように書いても同じ結果になります。

```
> (project (Employee) DeptName) difference (project (Dept) DeptName)
```


あまり考えずに書いているので、実装してみるともうちょっと仕様は変わるかも。


## プロジェクトの名前と場所

とりあえずプロジェクトの名前はToyRelとしましょう。
作る場所はsourcesの直下にします（PlayDeedleなどより一つ上のディレクトリになることに注意）。

ブランチは`toyrel/...`の形式の名前にしていきましょう。

また、FParsecとDeedleはfsprojに加えておいてください。（Arguは今回は使わないのでいいです）

### databaseという概念

将来的には複数のデータベースというのを持てるようにしたいと思っています。
データベースはリレーションの集まりです。
ディレクトリをデータベースとして扱いたい。

useでデータベースを切り替える。

例えばシラバスや成績などの「講義成績」データベースとか、住所とオーダーの「注文システム」データベースなどというような。

ただ当面はとりあえず一つとしたい。その辺の事を意識しつつ、けれどまずは簡単な構成にしておきましょう。

ToyRelのプロジェクトにdatabase/masterというディレクトリを作って、その中にsource/data/wikipediaのcsvと `シラバス.csv` をコピーするシェルスクリプトを作り、
普段の作業はそれに対してやります。
開発時はちょくちょくクリアしたくなると思うので、スクリプトはディレクトリを削除して作り直す所からやる感じにしておいてください。

setupdb.shとかそんな感じの名前でコミットしてください。別にbatファイルでもps1ファイルでもいいです。

当面はここのパスを決め打ちで作業していきます。
つまり、"シラバス"といったら、`source/ToyRel/database/master/シラバス.csv`を意味します。

なお、このdatabase下は実装が進むと変数名に対応するファイルがどんどん保存される事になるので、このディレクトリはcommitしないで.gitignoreに入れておいてください。

## identifierのパーサーを実装する

まずはウォームアップも兼ねて、identifierのパーサー、pIdentifierから実装しましょう。

identifierというのは普通の言語では変数や関数名に使われるなにか、を意味します。
例えば以下のident1とかident2とかident3の場所に来るのがidentifierです。

```
ident1 = project (ident2) idnet3, ident4
```

左辺の変数名、projectの引数のリレーションの名前、projectの引数のカラムの名前などで使われます。
（ただしカラムの名前は空白が入る場合などはidentifier以外の表記も可能）。

この左辺のident1はそのままident1.csvというファイルに保存する仕様にする予定なので、
identifierはファイル名として使えるものである必要があります。

細かい所は違っていても良いのですが、注意したい事としては

- 1文字目数字は禁止とする（パースで数字の即値と区別が難しいのでだいたいの言語では禁止されている）
- カタカナや漢字を使いたいのでその辺は真面目に対応する
- .やスペース、スラッシュやバックスラッシュ、シングルクオートやダブルクオート、コロン、セミコロン、アスタリスクなどのファイル名としてやばそうな物は禁止する

という所は満たしたい。

例で出てくるようなのは対応した上で、わかるものだけ受け入れる、つまり「怪しいものをはじくでは無くて安全なものを受け入れる」という風にパターンを書くと良いでしょう。
全ての怪しいケースを調べ上げるのはだいたい不可能なので、
「安全とわかっているものだけを受け入れる」は、入力を扱うプログラム全般で言える原則です。

大雑把には以下みたいな感じかなぁ、と思います。

```
identifier = nondigitchar normalchar*

nondigitchar = ([a-zA-Z]|p{IsHiragana}|p{IsKatakana}|p{IsCJKUnifiedIdeographs})
normalchar = nondigitchar | [0-9_]
```

以後このように、第二回ではふんわりした文法のようなものが提示される事がありますが、これはあくまでアイデアを伝える為のものであって、
実際に実装する時にはちゃんと自分なりに考えて良さそうな文法にしてください。
とくに、途中の文法要素を表すようなものを間に導入したりした方が良い場合は多いと思います。

IsHiraganaとかは次の「正規表現の文字クラスで遊ぼう」で解説します。

### 正規表現の文字クラスで遊ぼう

さて、pIdentifierにはひらがなとかカタカナとか出てくるので、正規表現を使うのが良さそうです。
FParsecではregexという関数に正規表現の文字列を渡すとそれにマッチするパーサーを返してくれます

```
let pNum = regex "[0-9]+"

run pNum "123abc"
```

こんな風に正規表現さえ分かれば、それからパーサーを作る事はかんたんに出来ます。
そこで目的のidentifierにマッチする正規表現を書ければ良さそう。

正規表現自体は知っていると思うのだけれど、ひらがなとかカタカナとか漢字というのはなかなか知らない人も居ると思うので、
ここではちょっとその辺をつついてみましょう。
こういう時はScratch.fsxで書いてみるのが良い。

F# では正規表現には別段特別な何かは無く、.NET のRegexクラスを使います。

まずは以下のコードをScratch.fsxで実行してみましょう。

```
open System.Text.RegularExpressions

Regex.IsMatch("123abc", "[0-9]+")
```

IsMatchは第一引数に対して、第二引数のパターンがマッチするかどうかを調べます。
どこがマッチしたかを知りたければMatchを使います。

```
Regex.Match("123abc", "[0-9]+")
```

ただ今回は文字クラスになれるのが目的なのでIsMatchで十分でしょう。

ひらがなにマッチする正規表現は、「文字クラス」というもので指定出来ます。`\p`のあとに中括弧でくくくって文字クラスを書くと、それにマッチします。
たとえばひらなな一文字は`\p{IsHiragana}`でマッチします。
試してみましょう。

```
Regex.IsMatch("あ", "\p{IsHiragana}")
Regex.IsMatch("ア", "\p{IsHiragana}")
```

これは通常の文字のパターンとして、他の正規表現と組み合わせる事が出来ます。
例えばひらがなかカタカナ一文字とマッチさせたければ、縦棒でつなげれば良い。

```
Regex.IsMatch("あ", "\p{IsHiragana}|\p{IsKatakana}")
Regex.IsMatch("ア", "\p{IsHiragana}|\p{IsKatakana}")
Regex.Match("あアほげいか123", "(\p{IsHiragana}|\p{IsKatakana})+")
```

123にはマッチしてない事に注目してください。

どんな文字クラスがあるかは以下に一覧がありますが、膨大なので使う予定のものだけ見るくらいでいいでしょう。

 [.NET 正規表現での文字クラス - Microsoft Docs](https://docs.microsoft.com/ja-jp/dotnet/standard/base-types/character-classes-in-regular-expressions#SupportedNamedBlocks)


### 課題0: identifierにマッチする正規表現を書け

identifierにどういうものがふさわしいかは、「こんなものを動かしたい、というイメージ」の用途を考えた時にどんなのならいいのかを考えて自分で決めて下さい。
ようするにファイル名として有効で変数名としても有効で、csvのカラムの名前としても大多数はサポート出来るのが良い。

仕様自体は自分で考えてそれっぽいのを決めて欲しいですが、以下には全体マッチして欲しい。

```
abc
_abc123
abc_123
専門
フロア
```

そして以下には全体マッチはしないで欲しい（.や*はその手前までマッチはしても良い）

```
123
abc.def
abc*
abc:def
abc def
(abc)
abc+def
```

ブランチ名は `toyrel/1_pexpression` でお願いします。

なお、github上で私が見るために、どれはマッチしてどれはマッチしなかったのかをScratch.fsxのテストコードの上にコメントを書いてください。（一言コメントでいいです）

### 正規表現とregexを使ってpIdentifierを作る

正規表現さえ出来てしまえば、FPrasecのregexを使ってそれにマッチするパーサーが作れます。
正規表現は上で作った課題に差し替えてもらうとして、以下みたいな感じで作れるでしょう。

```
let pIdentifier = regex "[_a-zA-Z][0-9a-zA-Z_]*"
```

FParsecのregex関数について知りたければ以下を見てみて下さい。

[FParsec.CharParsers](https://www.quanttec.com/fparsec/reference/charparsers.html#members.regex)

## projectを簡易的に実装してみる

さて、pIdentifierが出来たら、Relational Algebraで一番簡単そうな、projectから実装してみましょう。

projectは最終的にはかなり本格的なExpressionの仕様が必要になりますが、いきなり最終形を考えるのは良いプログラムのスタイルではありません。
まずは小さく限定的な仕様から始めて、徐々に本格的に育てていくのが良い。

ここで考えるべき限定的な仕様としては、なるべく以下の性質を満たしたものが良いです。

- 最終的な仕様としてもvalidな限定的なケース
- 実装が一番簡単なケース

という事でここでは、そんな暫定的な仕様から始めたいと思います。

### projectの文法

projectは最初の引数がカッコでくくったexpressionというもので、そのあとにカラムの名前のリストが並びます。
expressionは後述するので後回しにして、それ以外は以下のようなイメージ。

```
project_expression = 'project' expression columnlist

columnlist = column
  | column ',' columnlist

column = '[' string ']'
     | identifier

```

stringとかはまぁ適当に(閉じ大かっこ以外とかでいいと思う)。

columnの名前としてはファイル名で使えないものが含まれていてもいいのですが、
それは大かっこでくくる方で対応したい。という事で大かっこでくくらなくていいケースはidentifierで十分でしょう。

なおLEAPではcolumnlistはカッコでくくっていたけれど、toyrelではここのカッコは無しで行きたいと思います。たぶん要らない気がするので。

という事でとりえあず一番下のcolumnから実装していきましょう。

### pColumnの実装

さきほど作ったpIdentifierを使って適当に大かっこの方のパーサー、pSBracketColumnとつなげればpColumnになりそうです。

```
let notSBracket s = s <> '[' and s <> ']'
let pSBracketColumn = (str "[") >>. many1Satisfy notSBracket .>> (str "]")

let pColumn = pIdentifier
           <|> pSBracketColumn
```

pSBracketColumnの定義は適当に今書いたので、もうちょっと真面目に考えた方がいいかもしれません（別にこのくらいでもいい気もしますが）。
名前はもう少し考えたいかも。

SQLなどでは大かっこはフィールド名に空白などが入っているケースで使われます。なので空白は含んだ名前がパース出来るといいでしょう。
例えば`[理学部 物理学科]`みたいな。

今回いろいろと仕様を自分で考えて実装してもらう事になりますが、基本的には以下のように考えるといいでしょう

- SQL的に普段使いそうなものが入っているか
- サンプルで出てくる例は入っているか
- csvに変なのが入っていた時におかしく振る舞うのはOKとする（勉強目的なので実装が簡素な方を重視）

### expressionの暫定的な仕様

expressionは中を評価するとrelationになっているようなものです。
まず考えるべきは以下の２つです。

- リレーションの名前をそのままカッコでくくったもの
- projectなどの結果

例えば、１つ目の例は以下になります。

```
project (シラバス) 専門, 学年
```

この時、１つ目のカッコの中はリレーションの名前になります。

２つ目の例としては以下のようなものが考えられます。

```
project (project (シラバス) 専門, 学年, 場所) 専門, 学年
```

以上をそのまま書くと以下のようになります。

```
expression =  '(' identifier ')'
           |'(' project_expression ')'
```

さて、これをそのまま実装しようとしてみると、pExpressionを実装する時にはpProjectExpressionが必要で、
pProjectExpressionを実装する時にはpExpressionが必要になってしまいます。

文法的は再帰で定義していくものなので、左再帰で無い限りは再帰は問題無いはずですが、
実際にどのように書けば良いでしょう？

そこで出てくるのが `createParserForwardedToRef` です。

### createParserForwardedToRefを使ってexpressionの再帰を解決する

expressionの定義にはproject_expressionが必要で、project_expressionの定義にはexpressionが必要、
という状態を解決する為の機能として、FParsecには `createParserForwardedToRef()` という関数が提供されています。

これは、実体の無い仮のパーサーを返して、とりあえずそれを使ってルールを定義したあとに、その定義したルールを使って仮のパーサーの実体をあとから定義する、
という事を可能とします。

例として、pExpressionを仮のパーサーとして生成してpProjectExpressionを定義してみましょう。

```
let pExpression, pExpressionRef = createParserForwardedToRef()
```

Refについては後述します。こうやって得た仮のパーサーを使ってpProjectExpressionを定義する。

```
let pProjectExpression = (str "project") >>. pExpression .>>. pColumnList
```

このように、まだ定義していないpExpressionを使ってpProjectExpressionが定義出来ました。

次に、このpProjectExpressionを使ってpExpressionを実際に定義します。
この実際に定義する時に重要になるのが先程 `createParserForwardedToRef()` で得た二番目の戻り、 `pExpressionRef` です。
これは変更という副作用が必要なので通常のletでは無く`<-`を使います。（FParsecのチュートリアルでは`:=`が使われていますが、[F# 6ではdeprecated](https://docs.microsoft.com/en-us/dotnet/fsharp/language-reference/symbol-and-operator-reference/#reference-cell-operators-deprecated)となっています。）

```
pExpressionRef.Values <- (pstring "(") >>. pidentifier .>> (pstring ")")
                         <|> (pstring "(") >>. pProjectExpression .>> (pstring ")")
```

この右辺でpProjectExpressionを使っている事に注目してください。

もちろんこのまま書くと、pidentifierとpProjectExpressionの返りがコンパチブルじゃない的なエラーが出てくると思います。
でもその解決はここまで進めた読者なら自分で出来るでしょう。

### 課題1: pExpressionとpProjectExpressionをここまでの仕様で完成させよ

ちゃんと返す型も作ってください。
パースだけでいいです。ちょっと大きめの課題なので、一気に出来ないようなら言って下さい。分割します。

以下がパース出来る事を確認してください。

```
project (project (シラバス) 専門, 学年, 場所) 専門, 学年
```

{% capture recursive_type %}
**型が相互に依存する場合の書き方（andを使う)**  

さて、この課題では、場合によってはExpressionがProjectExpressionを参照するけれど、
ProjectExpressionではExpressionを参照したい、という事が起こる人もいるでしょう。
こういう時に、F#ではandを使うトリックで定義する事が出来ます。

例えば以下のように書くと、

```
type CalculatorState = {
     display: CalculatorDisplay
}
and CalculatorDisplay = string
```

CalculatorStateの中でまだ出てきてないCalculatorDisplayを使う事が出来ます。
同様に相互を参照するような型もこの方法で定義する事が出来ます。

このandを使った例についてのもう少し詳しい解説は、
[Calculator Walkthrough: Part 1 - F# for fun and profit](https://fsharpforfunandprofit.com/posts/calculator-design/#defining-the-calculatorstate-type)の「Defining the CalculatorState type」のあたりに書いてあります。
{% endcapture %}
{% include myquote.html body=recursive_type %}

### 第一回と同じ感じのprojectでdfを返す所まで実装してみよう

まずは第一回と同じように、ProjectExpressionを処理する関数を作ってみましょう。rowはこの時点では重複があってOK。
擬似コードっぽく関数の型を示すと以下みたいな感じでどうでしょう？

```
evalExpression: Expression -> Frame
evalProjectExpression: ProjectExpression -> Frame
```

パイプでdfを流していくと思うので、引数の順番は expr dfがよさそうか。

evalExpressionはパターンマッチしてこの時点ではリレーションの名前のみとProjectExpressionに処理を分岐。どちらもdfを返す。
リレーションの名前のみの場合は、そのリレーションを開いてdfを返す。ProjectExpressionの場合は、evalProjectExpressionを呼んでdfを返す。


evalProjectExpressionでは最初のExpressionの処理でevalExpressionを処理するので、相互再帰になりそうです。

相互再帰は以下でどうにかなりそうか？

[Recursive Functions: The rec Keyword - F# - Microsoft Docs](https://docs.microsoft.com/en-us/dotnet/fsharp/language-reference/functions/recursive-functions-the-rec-keyword#mutually-recursive-functions)

{% capture recursive %}
**無限の再帰で計算が進まない！という事にならない為の基本的な考え方**  

文法では再帰の定義を行います。
また、F#では型も良く再帰的に定義されます。
また、F#では今回のように関数が再帰する事も良くあります。

この手の再帰について書く場合には、意図せず無限に再帰になって計算が前に進まない、という事にならないように気をつける必要があります。

再帰の表現力は非常に高く、一般に再帰が止まるかどうかを判定するのはややこしい問題ですが（型付けが無い場合は不可能、型付きなら強正規化定理で可能だった気もするけれど詳細は忘れました）、簡単に計算が進むのがわかるようなサブセットの範囲でプログラムを記述するようにする事で、
実用的な問題の多くはカバー出来ます。

再帰を書く時に自分が気をつけている事としては、

1. パターンのどれかは１段下の概念になるようにし、それをパターンの上の方に並べる
2. 再帰する時には１段計算が進んでいるものを渡すようにする（ちゃんと一つ計算が進む事を見直して確認する）

という事です。

例えばevalExpressionではまずリレーションの名前だけのケースを先に処理し、evalProjectExpressionを呼ぶ時には必ず元の引数をunwrapして一つ進めたなにかを渡している事を確認する、という具合です。

全ての問題をこう書ける訳ではありませんが、結構多くの問題をこう書けるものです。
また、なるべくこう書こう、と注意していると、そう書けない所には特別注意を払う事にもなるし、
どうしてそう書けないかを考える過程で良く問題を考えられるので、
そう書けないケースでも計算が進まない再帰を書いてしまうのを防ぐ役に立ちます。

{% endcapture %}
{% include myquote.html body=recursive %}

## rowをdistinctにする

さて、ここまでだと、rowに重複が生まれる場合があります。
例えば以下のようにすると、

```
> project (シラバス) 専門, 学年
```

「数学,1」などの行が複数残るはずです。
Relational Algebraとしてはこうした重複は削除する必要がある。

そこでdfを渡して、rowの重複を除去したdfを返す、distinctを作りましょう。

distinct関連はListやSeqにdistinctという関数があるので、
それを使うのが良さそう。

[Seq (FSharp.Core)](https://fsharp.github.io/fsharp-core-docs/reference/fsharp-collections-seqmodule.html#distinct)

という事でRowsをまずはSeqにして、それをSeq.distinctを使って、そのあとdfに戻す感じにしましょう。

### rowの比較周りの話

Deedleのrowは内容が同じなら違う行でもイコールの比較はtrueを返します。
例えば以下を実行してみてください。

```
let df = Frame.ReadCsv "../../data/シラバス.csv"
let rows = df.Columns[ ["専門"; "学年"] ].Rows.Values |> Seq.toList

rows[0] = rows[1]
```

行が違っていても、値が同じであれば両者はイコールとなります。
ハッシュ値も同じになります。

### 課題2: dfのrowを一意にしたdf2を返す、distinct1を作れ

まずは、以下のような感じで、単純にdfのrowをdistinctにする関数を作りましょう。

```
type Distinct1 = Frame -> Frame
```

この手の表記の見方を簡単に。
まずこれは実装する目標となる関数の型を表しています。
型の名前は大文字始まり。

そして実際の関数は先頭を小文字にした関数名で実装するのがコンベンションです。
つまりこの場合は、以下のような実装になる。

```
let distinct1 df = ...
```

上記のtype文は基本的には使わなくていいです。
使う場合はdistinctを例えば以下のような定義に変えれば使えます。

```
let distinct1: Distinct1 = fun df ->
```

Distinctを使わない定義の方が実装が簡潔なので、別に使わなくてもいいです。
ただこの形にするとdfなどが型推論で型がtype定義から引き継がれるので、
いちいちdfに型指定しなくてもFrame型になるというメリットはあります。

### Relation型を作る

さてこのままではrowが重複しているdfとしていないdfが、型の上では区別出来ません。
そこで、rowが重複していないFrameを表すRelationという型をつくりましょう。

Relationの作り方は２つくらいあります。

- 普通にRelation型を作る
- module RelationのT型とする

いい機会なので、上と下のやり方でそれぞれどう違うかを試してみましょう。
まずは上のやり方でやってみます。

### 課題3: Relationの型をつくれ

Relationを普通のsingle case unionで作り、中身はFrameでいいでしょう。
generics型もreadCsvして返ってきた型を参考に具体的に指定してしまって良いと思います。

single case unionについては以下を参照の事。

[Single case union types · F# for Fun and Profit](https://swlaschin.gitbooks.io/fsharpforfunandprofit/content/posts/designing-with-types-single-case-dus.html)


このRelationを使ってdistinct, project, あとcsvをロードする関数(readCsvしてdistinctするなにか）を書いてみましょう。

### Relation型を作る意義などについて考える

この辺で、pureな型とその入出力的な話を書く。
ロード、distinct、projectを使う人にとってはもうFrame型は見えない、という話とか。

### 課題4: modle Relationを作りADTしよう

Relationという型を作ると、その型に関する操作がいろいろ必要になる事に気づくと思います。
こうしたものは、Relationの型の周辺だけが知っていれば良い情報です。

この考えを反転させて、型というものがその関連する操作によって定義されるという考え方を、Abstract Data Typeといいます。
数学などで足し算というものを、交換法則だとか結合法則がどうだとかで再定義するようなものです。
ある型を、それに適用出来る演算でその定義とする考え方。

[抽象データ型とはなんぞや？](https://karino2.github.io/2022/07/28/abstract_data_type.html)

この辺もなんか書くが、もうちょっと具体例が増えてからの方がいいかも？

## リレーションの保存

toyrelではトップレベルで評価されたリレーションは必ずcsvに保存されます。
左辺の変数がある時はそれがファイルのbasenmaeになります。
左辺の変数が無い時はランダムで振られたファイル名になります。

まずは左辺が無いケースでランダムのファイル名に保存する場合を考えましょう。

### 保存

dfを basename + ".csv"という名前でデータベースのディレクトリに保存する。

### ランダムファイル名の生成

とりあえず長すぎず変な文字が無い無難なbasenameを作りましょう。
`[a-z][A-Z]`だけで8文字くらいでいいかしら？

leapのソースを見ると最初二文字は`zz`で固定、残りは `(rand() % 26)+97`、になっていた。
`[a-z]` っぽい。文字数は6文字っぽい。随分短いが、まぁそれくらいでいいかもしれない。長いと打ちづらいしね。

### printの実装

printのパースと実行を実装しましょう。

### 左辺を含むパース

```
> hoge = (シラバス)
```

みたいに左辺がある時は `hoge.csv` になるようにします。
まずは文法を考える所から始めましょう。

### listコマンドの実装

データベース内のリレーションの一覧を出力。

## モジュール構成などを考える

この辺で型とかファイルとかちゃんと考える。
この時点ではCommon, Parser, Evalくらいか。

## differenceの実装

projectだけだといまいち面白い事が出来ないので、次にdifferenceを実装してみます。

### differenceの説明

`rel1 difference rel2` で、rel1だけにあってrel2に無いrowだけが残る。Union Comparableじゃない時はエラー。
とりあえずはfailwithで落とす。

文法としては、

```
DifferenceExpression = Expression "difference" Expression
```

で良いでしょう。（別に中置にしなくても良いのだけどLEAPがそうなっているしパーサーの練習に手頃と思う）

この辺でUnion Comparableの説明をする。

### 処理の実装

まずはパースが終わったあとの処理から書く。
rel2を全部辞書に入れて、rel1のrowsでfilterして辞書に入ってなかったら流す感じにする。

### パース

上記文法でパースしてつなげる。

### 以下を動かす

```
> r2 = (project (Employee) DeptName) difference (project (Dept) DeptName)
> print r2
```

### エラーを実装する

エラーメッセージを保持するべく、Union型で。中身は文字列だけでいいでしょう。

## restrictの実装

第一回でfilterと読んでたものの実装。第一回を書いている時点ではこの名前に気づいていなかったのでfilterという

restrictの条件は以下みたいな感じで。

```
binop = '<' | '<=' | '=' | '>=' | '<>'
binoprand = value | colname

cond_atom = binoprand binop binoprand
cond = cond_atom
     | cond_atom "and" cond
     | cond_atom "or" cond
```

これもまたいつものように実際に実装する人が文法は真面目に考えて下さい。

これくらいは守って欲しい、という事については以下に並べておきます。

- 等価は`=`
- ノットイコールは`<>`
- `[学年]=[専門]` のようなカラム名同士や、 `[学年]=1`のようなカラム名と値の両方が許されます
- 値はとりあえず整数と文字列は対応する（小数とかはやりたければどうぞ）

TODO: この辺でtheta-comperableの話をする

## theta-joinの実装

joinはtheta-joinだけでいいでしょう。
以下が動くように。

```
> join (Employee) (Dept) (Employee.DeptName = Dept.DeptName)
```

joinのconditionではrelationのprefixは必須とします。

とりあえず小手調べにproductを先に実装する。

## replの実装

radline使ってreplを作る

## 細々としたものの対応

- `@last`
- 複数データベース対応 (`use`の実装)

## renameの実装

一応実装しておく。

```
> rename (シラバス.専門) 科目
```

- リレーションは最初のカッコでわかるので別途は指定しない
- rename対象はidentifierか`[`とかでくくったもの（colname)

## 残りを一通り実装する

- union
- intersect

この辺はやらなくてもいいけれどここまでやったらか一応。

## コマンドにしてみる

一応シングルバイナリー（ただしランタイムは含めない奴）にしてみる。

## いろいろ書いてみよう

- Wikipediaの例を一通りやる
- tandpもやってみるといいかも（よさげな課題をピックアップしてtandp.mdの方に書く）

