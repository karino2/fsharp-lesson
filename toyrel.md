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

より詳細なRelational algebraについては上記書籍を参照してもらうとして、
ここでは実装上の簡単な説明を行います。

relational modelとは「データベースのテーブルというのは、なんらかの制約を満たしたタプルの集合とする」というモデルです。
rowが重複しないテーブルと思っておけばだいたいよろしい。

そしてrelational algebraというのはこのrelational model同士を演算して新しいrelational modelを作るという合成方法です。
relational algebraの各演算がまたrelational modelを返すので、この演算は代数をなす、という事ですが、
実装的に重要なのは、演算の結果もテーブルも区別がなく同じrelational modelになる、という事です。

ToyRelとしては、relational modelとしてcsvファイルを使います。relationの名前として、csvファイルのbasename、つまり拡張子を取り除いたものを使います。
カラムの型としてはとりあえずIntとStringだけ考えています（Floatもサポートしたくなったら追加するかも）。

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

リレーションは変数に入れる事もできる。
以下のような文を実行すると、

```
> シラバス_専門_学年 = project (シラバス) 専門, 学年
```

「シラバス_専門_学年.csv」というファイルが生成されて、中身は先程と同じ。

differenceなどは以下のような感じになる。（Wikipediaの方も参照の事）

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

という事でここでは、暫定的な仕様から始めたいと思います。

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

細かい所は違っていても良いのですが、注意したい事としては

- 識別子は1文字目数字は禁止とする（パースで数字の即値と区別が難しいのでだいたいは禁止されている）
- カタカナや漢字を使いたいのでその辺は真面目に対応する

という所は満たしたい。
なおLEAPではcolumnlistはカッコでくくっていたけれど、toyrelではここのカッコは無しで行きたいと思います。

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

### projectでdfを返す所まで実装してみよう

まずは第一回と同じように、ProjectExpressionを処理する関数を作ってみましょう。
擬似コードっぽく関数の型を示すと以下みたいな感じでどうでしょう？

```
evalExpression: Expression -> Frame -> Frame
evalProjectExpression: ProjectExpression -> Frame -> Frame
```

パイプでdfを流していくと思うので、引数の順番は expr dfがよさそうか。

EvalExpressionはパターンマッチしてこの時点ではリレーションの名前のみとProjectExpressionに処理を分岐。どちらもdfを返す。

EvalProjectExpressionでは最初のExpressionの処理でEvalExpressionを処理するので、相互再帰になりそうです。

相互再帰は以下でどうにかなりそうか？

[Recursive Functions: The rec Keyword - F# - Microsoft Docs](https://docs.microsoft.com/en-us/dotnet/fsharp/language-reference/functions/recursive-functions-the-rec-keyword#mutually-recursive-functions)

{% capture recursive %}
**無限の再帰で計算が進まない！という事にならない為の基本的な考え方**  

文法では再帰の定義を行います。
また、F#では型も良く再帰的に定義されます。
また、F#では今回のように関数が再帰する事も良くあります。

この手の再帰について書く場合には、意図せず無限に再帰になって計算が前に進まない、という事にならないように気をつける必要があります。

再帰の表現力は非常に高く、一般に再帰が止まるかどうかを判定するのはややこしい問題ですが（型付けが無い場合は不可能、型付きなら強正規化定理で可能だった気もするけれど詳細は忘れました）、

簡単に計算が進むのがわかるようなサブセットの範囲でプログラムを記述するようにする事で、
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
とりあえず`[a-z][A-Z]`だけで8文字くらいでいいかしら？

### 保存

dfを basename + ".csv"という名前でデータベースのディレクトリに保存する。

### printの実装

printのパースと実行を実装しましょう。

### 左辺を含むパース

`> hoge = (シラバス)`

みたいに左辺がある時は `hoge.csv` になるようにします。
まずは文法を考える所から始めましょう。

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

エラーメッセージを保持するべく、Unon型で。中身は文字列だけでいいでしょう。

## ファイル構成などを考える

この辺で型とかファイルとかちゃんと考える。projectが終わったあたりでもいいかも。
