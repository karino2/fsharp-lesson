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

また、FParsecとDeedleはfsprojに加えておいてください。（Arguは使うか未定なのでまだいいです）

## まずはprojectを簡易的に実装してみる

さて、とりあえず一番簡単な例を作ってみる所から始めます。projectが簡単で良いでしょう。

projectは最終的にはかなり本格的なExpressionの仕様が必要になりますが、いきなり最終形を考えるのは良いプログラムのスタイルではありません。
まずは小さく限定的な仕様から始めて、徐々に本格的に育てていくのが良い。

ここで考えるべき限定的な仕様としては、なるべく以下の性質を満たしたものが良いです。

- 最終的な仕様としてもvalidな限定的なケース
- 実装が一番簡単なケース

という事でここでは、そんな暫定的な仕様から始めたいと思います。

### databaseという概念

将来的には複数のデータベースというのを持てるようにしたいと思っています。
データベースはリレーションの集まりです。
ディレクトリをデータベースとして扱いたい。

useでデータベースを切り替える。

例えばシラバスや成績などのデータベースと、住所とオーダーのデータベースなどというような。

ただ当面はとりあえず一つとしたい。

ToyRelのプロジェクトにdatabase/masterというディレクトリを作って、その中にsource/data/wikipediaのcsvと `シラバス.csv` をコピーするシェルスクリプトを作り、
普段の作業はそれに対してやります。
開発時はちょくちょくクリアしたくなると思うので、スクリプトはディレクトリを削除して作り直す所からやる感じにしておいてください。

setupdb.shとかそんな感じの名前で。別にbatファイルでもps1ファイルでもいいです。

当面はここのパスを決め打ちで作業していきます。
つまり、"シラバス"といったら、`source/ToyRel/database/master/シラバス.csv`を意味します。

### projectの文法

projectは最初の引数がカッコでくくったexpressionというもので、そのあとにカラムの名前のリストが並びます。
expressionは後述するので後回しにして、それ以外は以下のようなイメージ。（厳密な文法という訳じゃないので雰囲気で読み取ってください）

```
project_expression = 'project' expression columnlist

columnlist = column
  | column ',' columnlist

column = '[' string ']'
     | identifier

identifier = nondigitchar normalchar*

nondigitchar = ([a-zA-Z]|p{IsHiragana}|p{IsKatakana}|p{IsCJKUnifiedIdeographs})
normalchar = nondigitchar | [0-9_]
```

stringとかはまぁ適当に(閉じ大かっこ以外とかでいいと思う)。
IsHiraganaとかは以下を参考に。 [.NET 正規表現での文字クラス - Microsoft Docs](https://docs.microsoft.com/ja-jp/dotnet/standard/base-types/character-classes-in-regular-expressions#SupportedNamedBlocks)

identifierは左辺の変数としてもあとで使うので、ファイル名として使えるものである必要があります。
細かい所は違っていても良いのですが、注意したい事としては

- 1文字目数字は禁止とする（パースで数字の即値と区別が難しいのでだいたいの言語では禁止されている）
- カタカナや漢字を使いたいのでその辺は真面目に対応する
- .やスペース、スラッシュやバックスラッシュ、シングルクオートやダブルクオート、コロン、セミコロン、アスタリスクなどのファイル名としてやばそうな物は禁止する

という所は満たしたい。
例で出てくるようなのは対応した上で、わかるものだけ受け入れる、つまり「怪しいものをはじくでは無くて安全なものを受け入れる」という風にパターンを書くと良いでしょう。
全ての怪しいケースを調べ上げるのはだいたい不可能なので、
「安全とわかっているものだけを受け入れる」は、入力を扱うプログラム全般で言える原則です。

columnの名前としてはファイル名で使えないものが含まれていてもいいのですが、
それは大かっこでくくる方で対応したい。

なおLEAPではcolumnlistはカッコでくくっていたけれど、toyrelではここのカッコは無しで行きたいと思います。たぶん要らない気がするので。

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
これは変更という副作用が必要なので通常のletでは無く`:=`を使います。

```
pExpressionRef := (pstring "(") >>. pidentifier .>> (pstring ")")
               <|> (pstring "(") >>. pProjectExpression .>> (pstring ")")
```

この右辺でpProjectExpressionを使っている事に注目してください。

もちろんこのまま書くと、pidentifierとpProjectExpressionの返りがコンパチブルじゃない的なエラーが出てくると思います。
でもその解決はここまで進めた読者なら自分で出来るでしょう。

### 課題1: pExpressionとpProjectExpressionをここまでの仕様で完成させよ

ブランチ名は `toyrel/1_pexpression` でお願いします。

ちゃんと返す型も作ってください。
パースだけでいいです。ちょっと大きめの課題なので、一気に出来ないようなら言って下さい。分割します。

### 第一回と同じ感じのprojectでdfを返す所まで実装してみよう

まずは第一回と同じように、ProjectExpressionを処理する関数を作ってみましょう。rowはこの時点では重複があってOK。
擬似コードっぽく関数の型を示すと以下みたいな感じでどうでしょう？

```
evalExpression: Expression -> Frame -> Frame
evalProjectExpression: ProjectExpression -> Frame -> Frame
```

パイプでdfを流していくと思うので、引数の順番は expr dfがよさそうか。

evalExpressionはパターンマッチしてこの時点ではリレーションの名前のみとProjectExpressionに処理を分岐。どちらもdfを返す。

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

### rowをdistinctにする

csvで同じ内容の行を２つ作って、df.Rows.Getした結果を比較してイコールがtrueになる事を確認。
Dictionaryに突っ込んで同じ行が出ないようにする。

せっかくなので型も作ろう。Relationという名前にしますかね。

TODO: この辺でRelational Modelのタプルとかの用語も一応解説しておく。

## リレーションの保存

toyrelではトップレベルで評価されたリレーションは必ずcsvに保存されます。
左辺の変数がある時はそれがファイルのbasenmaeになります。
左辺の変数が無い時はランダムで振られたファイル名になります。

まずは左辺が無いケースでランダムのファイル名に保存する場合を考えましょう。

### ランダムファイル名の生成

とりあえず長すぎず変な文字が無い無難なbasenameを作りましょう。
`[a-z][A-Z]`だけで8文字くらいでいいかしら？

leapのソースを見ると最初二文字は`zz`で固定、残りは `(rand() % 26)+97`、になっていた。
`[a-z]` っぽい。文字数は6文字っぽい。随分短いが、まぁそれくらいでいいかもしれない。長いと打ちづらいしね。

### 保存

dfを basename + ".csv"という名前でデータベースのディレクトリに保存する。

### printの実装

printのパースと実行を実装しましょう。

### 左辺を含むパース

`> hoge = (シラバス)`

みたいに左辺がある時は `hoge.csv` になるようにします。
まずは文法を考える所から始めましょう。

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

