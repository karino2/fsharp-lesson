---
title: "第一回 まずはライブラリをつついてみよう"
layout: page
---
F# での基本的な作業の進め方に慣れつつ、あとで使う事になる準備として、
ライブラリをつついてみたいと思います。

## セットアップ

[Install F# with Visual Studio Code](https://docs.microsoft.com/en-us/dotnet/fsharp/get-started/install-fsharp#install-f-with-visual-studio-code)に従って.NET、VSCode、Ionideのインストールを行ってください。

## Deedleをつついてみよう

Deedleをつついてみましょう。

### fsharp-lesssonでのフォルダとブランチのルール

初回なので、少しfsharp-lesson全般の話をここでします。

まず作業は、fsharp-lessonのレポジトリ、[https://github.com/karino2/fsharp-lesson](https://github.com/karino2/fsharp-lesson)を各自がgithub上でforkして、それをローカルにcloneしたフォルダの上で作業する事にします。

ソースコードはsourcesの下のサブディレクトリに置く事にします。
第一回だったら、`sources/play_library/` というディレクトリがあると思うので、この下にそれぞれディレクトリをほって作業します。

また、少し通常のブランチの使い方と違いますが、添削の都合で各作業はブランチを切る事にします。
第一回のブランチ名は全て `play_library/` を前につけて、あとは数字の通し番号とどこかを表す名前を適当につけます。

例えばDeedleをいじってみるというこのブランチは`play_library/1_playdeedle` としましょう。
次のArguは`play_library/2_helloargu` とします。（2のラベル名はあとで変えるかも）

### PlayDeedleのブランチとプロジェクトの作成

それではまずはブランチとプロジェクトを作成してみます。
Deedleをいじってみるのはfsx上で行うのでプロジェクト無しでも良いのですが、
今後の開発の練習という事でPlayDeedleというプロジェクトを作る事にします。

まずは上記で説明した通り、`play_library/1_playdeedle`というブランチを切り、以後このブランチで作業します。

次にプロジェクトを作成します。場所は`sources/play_library/`の下に`PlayDeedle`というディレクトリが作られるようにプロジェクトを作ります。
以下のように実行しましょう。

```
$ cd sources/play_library
$ dotnet new console -lang "F#" -o PlayDeedle
```

この時にPlayDeedleというディレクトリは勝手に掘られるので、そのディレクトリは掘らないのがコツです。

プロジェクトの作り方の詳細については以下のドキュメントを見ましょう。

[Get Started with F# in Visual Studio Code - Microsoft Docs](https://docs.microsoft.com/en-us/dotnet/fsharp/get-started/get-started-vscode)

### fsxでDeedleをつついてみる

プロジェクトを作ったら、最初にするのは、私の場合はScratch.fsxというファイルを作ってVSCodeでこのプロジェクトを開く事です。
fsharp-lessonでもそうしてみましょう。

```
$ cd PlayDeedle
$ touch Scratch.fsx
$ code .
```

これでVSCodeが開くはずです。

Scratch.fsxはとりあえず実験用の書き捨てのコードを置く場所にしています。

VSCode上でScratch.fsxを開いて、以下のように書いてみましょう。

```
#r "nuget:Deedle"

open Deedle
```

そしてそれぞれの行の終わりで Alt+Enterを押します（その行を実行というショートカットです）

この時、最初の実行ではDeedleという所が破線のままでエラーっぽく表示されたままになっているかもしれません。
これはfsxではちょくちょくある事なので、実行したあとに「Windowの再読み込み」というのをやります。

せっかくなのでもし破線が出てない人も、ここでWindowを再読み込みしてみましょう。

Windowの再読み込みは、「Cmd+Shift+P」を押してから、「Reload」と入力するとそれっぽい候補が出ると思うのでそれを選んでEnterを押して下さい。
なおCmdはWindowsだとCtrlになると思います（以後全部Macのショートカットで書くので適宜読み替えて下さい）

![reload_window](reload_window.png)

こうするとウィンドウごと再読み込みされて、破線が無くなると思います。

なお、再読み込みするとまっさらな状態になるので、先程と同様に、書いたスクリプトの2行のそれぞれの末尾でAlt+Enterして下さい。

そのあとに以下のように入力してAlt+Enterしてみてください。

```
Frame.ReadCsv "../../data/シラバス.csv"
```

無事、下のウィンドウになにかずらずらと、csvの内容を読んだDataFrameっぽいものが出ているでしょうか？

（たまに文字列が化ける事があるのでその場合はもう一回Alt+Enterで実行してください）

もっと見やすい方法はあとで示すので現時点ではとりあえずなにかそれっぽいのが表示される所までいったらOKです。

ではここまでにやった事を簡単に解説していきます。

### fsharp scriptとfsxについて

F# には.fsのファイルと.fsxのファイルがあります。以下簡単に両者について説明しておきます。

拡張子が.fsのファイルは、通常のF#のプログラムを置く所で、XXX.fsprojに管理されています（または管理するようにfsprojを変更する必要があります）。
これはいわゆる「本番」のコードを置く場所で、これをビルドする事で実行バイナリを作るものです。

一方で拡張子が.fsxのファイルはfsharp scriptというスクリプトを置くファイルです。

fsharp scriptはほとんど通常のF#のコードと同じですが、パケージのロードなどの拡張が入っていたりと微妙に違う所もあります。

Ionideでは.fsxファイル上でAlt+Enterすると裏でfsharp scriptのインタープリタが立ち上がって、そこに選択したテキストを送る感じの挙動になっています。
シンタックスハイライトなどはファイルを先頭から見ていっておかしな所を指摘しますが、インタープリタとしては送られてきたテキストを順番に見ていって問題が無ければ正しく動きます。

例えば、以下のようにopen文と使う文を逆にすると、

```
Frame.ReadCsv "../../data/シラバス.csv"

open Deedle
```

シンタックスハイライトとしてはエラーっぽい表示がされますが、
先にopen文の上でAlt+Enterしたあとにカーソルを上に戻してFrameの行でAlt+Enterをしてやれば正しく動きます。（もちろんそういう事はあとで見た時にどう実行するかが分かりにくいのでなるべく避けた方がいいですが）

また、fsxファイル上でちょっとなにかを書いては実行してそれを元に修正して、またちょっと変更して実行して、と繰り返していくと、
裏のインタープリタに何が実行されている状態かわからなくなる事もありますし、なにか変な状態になってしまう事もあります。
そういう時は先程説明した「Reload Window」でインタープリタの状態をリセットし、また最初から実行しましょう。

基本的には触っていきながら慣れていけばいいと思いますが、もっと詳しく知りたい方は以下を見るといいでしょう。

- [F# Interactive (dotnet) Reference - Microsoft Docs](https://docs.microsoft.com/en-us/dotnet/fsharp/tools/fsharp-interactive/)
- [F# でのfsxベースの開発 - なーんだ、ただの水たまりじゃないか](https://karino2.github.io/2021/02/06/fsx_eval_based_dev.html)

### fsx上でのNuGetパッケージのロード

先程、以下のようなスクリプトを実行しました。

```
#r "nuget:Deedle"
```

これはnugetというパッケージマネージャ上からDeedleの最新版を取ってきてそのパッケージを「触れる状態」にしてくれます。
これを実行するまではVSCodeはDeedleというものを何も知らないので、open文などがシンタックスエラーになっていました。

さて、ここに書く文は、Nugetのサイトを見るとわかります。
基本的には"Deedle NuGet"でググって、引っかかったnugetのサイトを見れば良い。
例えば今検索して引っかかったのが以下。

[NuGet Gallery - Deedle 2.5.0](https://www.nuget.org/packages/Deedle)

バージョンが幾つかは検索した時期で違うと思う。

で、このページには様々な環境でのライブラリの使い方が書いてあるのだけれど、`Script & Interactive` と書いてあるタブを見ると以下のように書いてある。

```
> #r "nuget: Deedle, 2.5.0"
```

基本的にはこれがfsharp script上での書き方なのだけれど、自分は書き捨てスクリプトでいろいろ試したい時はバージョン指定しない事が多い。

そこでカンマのあとを削除して、以下の文が出来る。

```
#r "nuget: Deedle"
```

なお、 `.NET CLI` というタブを見るとfsprojへの追加方法が書いてある。これは後ほど出てきます。

### Deedle入門

さて、ここで使ったDeedleというライブラリの解説も軽くしておきましょう。
Deedleは、pythonでいう所のpandasみたいなものです。
ただpandasに比べてもうちょっと型に厳格な印象です。
良い事もあれば小回りが効かないと思う事もあるでしょう。

Deedleに関する情報としては、公式ドキュメントがよく書けていると思います。

[Deedle: Exploratory data library for .NET](https://fslab.org/Deedle/)

またこのページから辿れるチュートリアル、[Deedle in 10 minutes using F#](https://fslab.org/Deedle/tutorial.html)もよく書けています。

fsharp-lessonとしてはこれらを全て読む必要は無いと思いますが、最初の方くらいはちらっと見ておいてからこの先を読む方が分かりやすいかもしれません。

### 対話的に処理している時の整形について

さて、それでは実際にcsvをロードしてちょっとした処理をしてみましょう。

まず、現状は表示があまり見やすくありません。
対話的に処理をする時には以下を実行すると見やすくなります。

```
#load "Deedle.fsx"
```

これが何をやっているかは私もしりません。
ただこれをやるとFrameをロードしたりした時に表示が綺麗になります。

以下を試してみてください。

```
#load "Deedle.fsx"
Frame.ReadCsv "../../data/シラバス.csv"
```

また、この"Deedle.fsx"をロードすると、本来何も出力していないような処理、例えば以下のような処理でも内容が出力されます。

```
let df = Frame.ReadCsv "../../data/シラバス.csv"
```

これは便利な事もあれば不便な事もあるので、不便な時にはまたReload Windowした上でDeedle.fsxをロードせずに作業してください。

"Deedle.fsx"をロードしなくてもちょっと整形した結果を見てみたい、という場合には、
FrameのインスタンスのPrintメソッドを呼ぶと同じ出力が得られます。

```
let df = Frame.ReadCsv "../../data/シラバス.csv"
df.Print()
```

### 簡単なフィルタリングをしてみよう

ではこのシラバスのdf

```
let df = Frame.ReadCsv "../../data/シラバス.csv"
```

から、専門が数学だけの行を取り出してみましょう。


フィルタに関しては、[Deedle in 10 minutes using F#](https://fslab.org/Deedle/tutorial.html)の「Projection and filtering」という所に記述がありますが、
以下のようになっています。

```
joinedOpens.RowsDense
|> Series.filterValues (fun row -> row?MsftOpen > row?FbOpen)
```

このSeries.filterValuesというのを使えば良さそうですが、それに渡す関数が問題です。
以下のようになっています。

```
fun row -> row?MsftOpen > row?FbOpen
```

MsftOpenとFbOpenはカラムのタイトルのようです。
この間の?はクエスチョンマークオペレータというもので、詳細はあとで解説します。

ただ我々の例ではカラム名が日本語なので、このクエスチョンマークオペレータというのは使えそうにありません。

答えの前に、こういう時にどうやって試行錯誤して答えにたどり着くのか？という話をしたいと思います。

もともとのやりたい事に戻れば、たぶん以下のようになるのはわかると思います。

```
df.RowsDense
|> Series.filterVlaues( fun row -> ???ここに「専門」が「数学」と書きたい???)
```

そこでrowに何が出来るかを調べたい。
その為にはとりあえずrowを一つ取り出してみるのがいいと思います。
という事で取り出す方法を探してみる。

まず、`df.`と入力してみる。
するとプロパティやメソッドの一覧が出ますが、その中から使えそうなプロパティを探すとRowsかRowsDenseが見つかる。

まずはdf.Rowsを見てみる。以下を実行してみます。

```
df.Rows
```

すると `RowSeries<int,string>` というのがかえってくる模様。このドキュメントを見てみてもいいのだけれど、もうちょっと適当につついてみる。

次は `df.Rows.` と入力して、Rowsの使えるプロパティはメソッドの一覧を出してみましょう。

するとGetとGetAtというのが使えそうに見える。このメソッドの違いはこの例は偶然ややこしいので後回しにして、どちから好きなメソッド、ここではGetAtを使いましょう。
たぶんこれで指定した位置のRowが取れそうです。

```
df.Rows.GetAt(0)
```

これを実行したら、 `ObjectSeries<string>` というものが取れました。これがrowの正体のようです。
そこでこれを変数に入れて、いろいろつついてみます。

```
let row = df.Rows.GetAt(0)
```

として、`row.`と入力して何が使えるかをいろいろつついてみます。

### 課題1: GetとGetAsの違いを調べよう。

いろいろつついてみた所、どうもrow.Getとrow.GetAsのどちらかでカラムが取れそうに見えます。
そこでこの２つを実行してみましょう。

まず以下を実行してみます。

```
row.Get("専門")
```

どのような表示がされるか見てみましょう。

次にGetAsを読んでみるとエラーが出ると思います。

```
row.GetAs("専門")
```

このエラーメッセージはどういう意味でしょう？gitterで私に説明してください。

次に以下のように実行すると正しく実行されます。

```
row.GetAs<string>("専門")
```

この文と先ほどのGetの結果はどう違うでしょうか？gitterで私に説明してください。

### 課題2: 専門が数学の行だけを残そう

以下の???を書いて、専門が数学の行だけ取り出して下さい。

```
df.RowsDense
|> Series.filterValues(fun row -> ???)
```

これはScratch.fsxに書いてcommitしてpushし、gitterで私にgithub上のリンクを送って下さい。
なお、次の課題3も一緒にやってしまっても良いです。

ちなみにこのように複数行にわたったスクリプトを実行する場合はマウスやShift+矢印キーでこの二行を選択して、Alt+Enterします。
なお、検索の入力フォームが出てるとAlt+Enterが奪われて実行出来ないので注意しましょう（何度もやってイラつく…）

### 課題3: 専門が数学の行だけを持ったFrameを作ろう

課題2では、型がSeiriesのSeriesになっていると思います。
これをFrameにしてください。

[Deedle in 10 minutes using F#](https://fslab.org/Deedle/tutorial.html)の「Creating series and frames」を見ながら適当に試せば出来るでしょう。

これもcommitしてpushして私に見せてください。

### 課題4: 場所と学年だけのFrameを作ろう

これも[Deedle in 10 minutes using F#](https://fslab.org/Deedle/tutorial.html)のProjection and filteringを参考に（joinedOpensを作っているあたり）


### 課題5: フィルタとプロジェクションを関数にしよう

rowを引数にboolを返す関数を引数にとってフィルタしたFrameを返すfilterと、
カラムの名前のリストを引数にとってそのカラムだけを含んだFrameを返すproject関数を作ろう。


## Arguをつついてみよう

fsxでDeedleをつつくのはだいたいわかったと思うので、次は.fsの方を見ていきます。ついでにArguというライブラリをつついてみます。
ブランチ名は `play_library/2_playargu` で行きましょう。

### プロジェクトを作って実行しよう

前回同様コマンドラインから `dotnet new` でPlayArguというプロジェクトを作ってください。
そして出来たPlayArguをVSCodeで開く所までは前回と同様です。

ただ今回はプロジェクトを生成した時に一緒に作られるProgram.fsを見てみましょう。
以下のようになっていると思います。

```
// For more information see https://aka.ms/fsharp-console-apps
printfn "Hello from F#"
```

このurlを開いてみるのも有益ですが、まずは実行してみましょう。
このプロジェクトまでターミナルからcdして、以下のように実行します。

```
$ dotnet run
```

すると少しまったあとに、以下のように出力される事でしょう。

```
Hello from F#
```

これがProgram.fsに書かれている内容なのはまぁいいでしょう。
試しに出力される文を`Hello World`になるように変更して実行してみてください。

### dotnet buildとfsproj

次に `dotnet run` を実行した時に生成されるファイルを見ます。

binというディレクトリが作られているはずです。
このbinの下の、`bin/Debug/net6.0/` という所を見ると、なんだか一杯ファイルがあると思います。

ここにある `PlayArgu` というファイルを実行すると、同じ結果になるはずです。

```
$ bin/Debug/net6.0/PlayArgu
Hello from F#
```

`dotnet run` というコマンドは

1. PlayArguという実行ファイル（及び関連するdllや設定ファイルなど）を作る
2. PlayArguを実行する

という２つの事をやってくれるコマンドです。

1だけを実行する別のコマンドもあって、それは`dotnet build`です。

```
$ rm -r bin
$ dotnet build
$ bin/Debug/net6.0/PlayArgu
Hello from F#
```

dotnet buildというのは、現在のディレクトリにあるfsprojファイル（この場合はPlayArgu.fsprojという名前）に書かれている.fsのファイルを全部まとめて実行ファイルに変換する、という事をしてくれます。

こちらがF# の正規（？）の開発方法と思う。

### dotnet publishの話を少しだけ

なお、dotnet buildで作ったPlayArguバイナリは、同じフォルダにあるPlayArgu.dllやそのほかいろいろな物に依存しているので、PlayArguという実行ファイルをコピーして違う所に持っていくだけでは動きません。

```
$ cp bin/Debug/net6.0/PlayArgu ./
$ ./PlayArgu
The application to execute does not exist: 'XXXX/fsharp-lesson/sources/play_library/PlayArgu/PlayArgu.dll'.
```

これらのファイルがそれぞれ何なのかはdotnetの方の話になりますが、コンソールアプリで使っている分にはあまり知る必要も無いでしょう。

よその場所に持っていって実行する場合はpublishというのを実行する必要があります。
Macの場合は以下のように実行します。(他のOSについては、`-r <RID>`で指定する[Runtime Identifier (RID)](https://docs.microsoft.com/en-us/dotnet/core/rid-catalog)を変えて下さい。)

```
$ dotnet publish -r osx-x64 -p:PublishSingleFile=true
$ cp bin/Debug/net6.0/osx-x64/publish/PlayArgu ./
$ ./PlayArgu
Hello from F#
```

self-containedもつけろとかワーニングは出ますが動くはずです。
なお、この時も実行するマシンにはdotnet runtimeが入っている必要があります。ランタイムもくっつけて一つにする方法などもありますが、
このシリーズでは使わないのでこのくらいにしておきます。

publishについては以下に詳しく書いてありますが、

[dotnet publish command - .NET CLI - Microsoft Docs](https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet-publish)

結構長いので必要になるまでは読まなくて良いでしょう。

### 課題6: オプションhelloを処理

とりあえずArguは使わずに、引数が `-hello` だったら"Hello World"とコンソールに出力し、
それ以外なら "I don't know"と出力するようにProgram.fsを変更して動作を確認しましょう。

以下のようになるようにします。

```
$ dotnet run -hello
Hello World
$ dotnet run
I don't know
```

これを２つの方法でやってみます。

1. `Environment.GetCommandLineArgs()`を使う方法
2. `[<EntryPoint>]`を使う方法

この時に、Program.fsに書かれているリンク先を見るとヒントになります。

[コンソール アプリケーションと明示的なエントリ ポイント - F# - Microsoft Docs](https://docs.microsoft.com/ja-jp/dotnet/fsharp/language-reference/functions/entry-point)

まず1で実現してcommitし、次に2に変更してcommitしてgithubの履歴のリンクをgitterに貼って下さい。

### VSCodeからの実行

次にVSCodeからの実行方法を見てみます。

- とりあえずF5押す
  - この２つのステップが上手く行かない時は画面左端の三角と虫のアイコンをクリックして、「launch.jsonファイルを作成します」を選ぶ
- Blazor WebAssembly Debugを選ぶ（とりあえずlaunch.jsonが作られるものならなんでも良い）
- launch.jsonを以下のように書き換える

```
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": ".NET Core Launch (console)",
      "type": "coreclr",
      "request": "launch",
      "preLaunchTask": "Build: PlayArgu.fsproj",
      "program": "${workspaceFolder}/bin/Debug/net6.0/PlayArgu.dll",
      "args": ["-hello"],
      "cwd": "${workspaceFolder}",
      "stopAtEntry": false,
      "console": "internalConsole"
    }
  ]
}
```

- F5をもう一度押すと"Build: PlayArgu.fsprojが見つけられませんでした”と言われてダイアログが出るので、そこからタスクの構成を選ぶ
- Build: PlayArgu.fsprojを選ぶ
- task.jsonが生成されるので保存する
- F5

これでデバッグ実行が出来ます。Program.fsの適当な行の上でF9を押すとブレークポイントがはれるので、そのあとF5するとそこで止まると思います。
F10で次の行に進む、F11でステップイン（後述）、F5で次のブレークポイントまで進みます。
デバッガの使い方などは進めていく過程で学んでいくのがいいと思います。

なお、この辺の手順はionideのバージョンなどで挙動が違うので、少し上とは違うかもしれません。なんにせよF5で実行してブレークポイントで止まるならOKです。

上記のlaunch.jsonのポイントとしては、以下の点に注目しておきましょう。

- preLaunchTaskにビルドの名前を書く（fsprojの名前はプロジェクトごとに変える）
- programの行も生成されるdllのパスに変える
- argsは必要に応じて足す（ここに足したものがコマンドライン引数として渡される。上記の例では"-hello"を渡している）


### Arguをつついてみよう

では次に、fsprojにArguというライブラリを追加して使ってみましょう。
Arguはコマンドラインの引数のパーサーです。

[Argu: Introduction](https://fsprojects.github.io/Argu/)

このIntroductionやそこからリンクされている[Tutorial](https://fsprojects.github.io/Argu/tutorial.html)を見ればだいたい全てがわかるのですが、
割と簡単なものなのでとりあえず触ってみてどういうものか先に理解する方が手早いでしょう。

とりあえずいつものようにScratch.fsxというファイルを作ってつついてみます。
まずはNuGetのサイトで探します。

[NuGet Gallery - Argu](https://www.nuget.org/packages/Argu)

```
#r "nuget: Argu"
```

を実行すれば良さそう。

そして一番簡単な例として、以下のようなコードを実行してみてください。

```
open Argu

type Arguments =
    | Hoge
    | Foo of path:string

    interface IArgParserTemplate with
      member s.Usage =
        match s with
        | Hoge -> "print Hello World"
        | Foo _ -> "print Bar with arg"

let parser = ArgumentParser.Create<Arguments>(programName="hogehoge")

parser.PrintUsage()
```

IArgParserTemplateというインターフェースの定義が5行もあるのでぎょっとしますが、これはヘルプに関する記述をしているだけなので大した事はありません。
ヘルプの内容と、ArgumentsのUnion定義を見比べると、だいたいどういうものかわかるでしょう。

次に以下を実行してみます。

```
parser.Parse[| "--hoge" |]
parser.Parse[| "--foo" |]
parser.Parse[| "--foo"; "fugafuga" |]
```

なお、`[|`と`|]`でくくって中身をセミコロンで区切るのは、F#では配列の記法です。
普通リストしか使わないので見かけないかもしれませんが、引数のパーサーなのでmainに渡ってくる文字列の配列がそのまま渡せるようになっている為、
こうして実験でつつく時は配列を渡す必要があります。

上記の例だと、二番目はパースエラーに、1番目と3番目はなにかパースが成功したっぽく見えます。

### Arguのパース結果をつつく

では結果を変数に入れて少しつついてみましょう。

```
let res = parser.Parse[| "--foo"; "ikaika" |]

res.Contains Hoge
res.Contains Foo
```

ContainsのあとにはUnionで定義した値がそのまま使えるようです。
これでif文で何が渡されたかを判断出来そう。

さらにfooの時の引数を取得する為には、以下のようにGetResultを実行します。

```
res.GetResult(Foo)
```

引数は複数同時に指定出来るので、どのオプションの引数かを指定する必要があります。

```
let res = parser.Parse[| "--hoge"; "--foo"; "ikaika" |]
```

などに対してもやってみてください。


### 課題7: -hello をArgu化しよう

課題6でやった `-hello` の処理をArguで実装しなおしましょう。PlayArguプロジェクトで作業を続けます。

- fsporjへの追加は `dotnet add package Argu`
- ArguのパーサーへはEntryPointの引数をそのまま渡せば良い
- `--hello` を `-hello` にするのは `[<CliPrefix(CliPrefix.Dash)>]` をtypeにつける

あたりをヒントにやってみてください。

### 課題8: PlayDeedleのprojectとfilterをArguでコマンドライン化

`--project "場所" "学年"` みたいなオプションと、 `--filter "専門" "数学"` みたいなオプションを実装しましょう。
csvのパスは決め打ちで開いてしまっていいです。

PlayArguのプロジェクトに `dotnet add` でDeedleも追加して、課題3や課題5のコードを持ってきて修正してください。


## FParsecをつついてみよう

ライブラリをつついてみよう、第三弾はFParsecです。

- [stephan-tolksdorf/fparsec: A parser combinator library for F#](https://github.com/stephan-tolksdorf/fparsec)
- [Tutorial](http://www.quanttec.com/fparsec/tutorial.html)
- [fparsec/Samples at master · stephan-tolksdorf/fparsec](https://github.com/stephan-tolksdorf/fparsec/tree/master/Samples)

FParsecはいわゆるパーサーコンビネーターという奴ですね。パーサーのライブラリ。
少しいじったあとにチュートリアルを軽く読んで、あとはサンプルを見ればだいたいの事はわかると思う。
このページの最後に参考文献へのリンクも貼っておきますが、まずはいじりつつ必要な事だけ見ていくのが良いと思います。

FParsec入門としてprojectとfilterを`project([場所], [学年])`みたいなのと、`filter([専門] = "数学")` みたいなのをパースして実行する、というのを実装してみましょう。

### まずはいじってみる

いつものように、`play_library/3_playfparsec` というブランチを作り、
PlayFParsecというプロジェクトを作り、そしてScratch.fsxを足します。

そして以下のように書きます。

```
#r "nuget: FParsec"
open FParsec

run pfloat "1.25"
```

これでとりあえず動くと思います。

なお、以下のように書くとエラーが出ると思いますが、

```
let ws = spaces
let float_ws = pfloat .>> ws
```

その次に以下の行を足すとエラーが消えるはずです。

```
run float_ws "1.25 "
```

これはFParsecをfsharp scriptで試す時の落とし穴で、使う側のコードが無いと型が確定しないのでこうなります。
とりあえず開発の途中の段階ではrunをどこかに書いておくようにするといいでしょう。

### 課題9: チュートリアルを4.6（floatのリストのパース）までやってみる

とりあえずチュートリアルを4.6まで進めてみましょう。

[Tutorial](http://www.quanttec.com/fparsec/tutorial.html)

これはcommitはして欲しいですが、私に見せなくてもいいです。

### projectのパースをする

次に、 `project([場所], [学年])` をパースしてみましょう。
一気にやるのはちょっと大変なので順番に進めます。

### 課題10: カラム名のパーサーを書こう

まずはカラム名のパースです。

`[場所]`

のパースを考えます。チュートリアルの4.3と似ていますが、中身がpfloatじゃないですね。
`[`から始まり、`]` まで。変なエスケープとかは考えないでいいでしょう。

チュートリアルの4.7のstringLiteralのパーサーがこれをもっと複雑にした事をしているので、それを見つつ書いてみましょう。
名前はpColumnにしますか。

ここまで書けたら一旦見せて下さい。

### パーサーと文法

さて、ここまでなんとなくで触ってきたパーサーというものについて、簡単に解説したいと思います。
その為には、まず文法という話をする必要があります。

例えば以下の２つの式を見て下さい。

```
sin(3.5)
sin(2*3.5+0.3)
```

両者はカッコの中が違うけれど、違うのはカッコの中だけでsinという処理自体には違いが無い、というのは人間ならわかると思います。
そしてカッコの中にはなにか「評価」をすると数値になる式ならなんでも入れられるだろう、という事もわかります。
例えば`sin(2*3.5-3.0/2)`と先ほどの文には無い`-`や`/`があっても、人間が見えればこれが問題の無い文だと解釈出来ます。

でもプログラムに、「何が正しくて何が間違っているのか」を伝えるのはなかなか難しい。
プログラムには、厳密に指定するなにかが必要です。

こうした問題で正しい事、及びその解釈を伝えるものを、「文法」と呼びます。BNFの記法を適当に拡張したものがよく使われます。
文法はようするに正規表現みたいな何かだけどもっと強力なもの、と思っておくとだいたい正しい。

文法では、構成要素を再帰的に記述します。
たとえばよくあるのは以下。

```
stmt = funcall
     | expr

funcall = identifier '('  arglist ')'

arglist = expr
        | expr ',' arglist

expr = term '+' expr
     | term '-'  expr
     | term

term = factor '*' term
     | factor '/' term
     | factor

factor = number
       | identifier
```

ここではnumberとかidentifierは最初から与えられるものという前提になっています。
通常numberはintかfloatっぽい文字列、identifierは`[a-zA-Z_]`で始まってアルファベットと数字が続くようなものと定義される事が多いです。

縦棒で並べたものが「or」という意味で、並べたどれかのうちの一つ、という事を意味します。例えば、以下の場合

```
factor = number
       | identifier
```

「factorとは、numberまたはidentifier」と読みます。


下から順番に見ていくと、factorというのが数字か文字（変数）、で、掛け算割り算の項がtermとして定義されて、
exprとはtermを足したり引いたりしたもの、となっています。

このように、最初から与えられる「number」とか「ideintifier」などを組み合わせて、再帰的に構成要素を定義していって、
最終的にパース対象全体を定義するのが文法というものです。

このような記述と、`"sin(2*3.5+0.3)"`という文字列を比較してあっているかどうかを判定して、各文字を構成要素に分解するのがパーサーと呼ばれるものです。

これだとfuncallがexprにならないので、`sin( sin(3.0) )`みたいなのがエラーになってしまうので、普通はもうちょっと複雑になりますが、文法というものの基本を理解するにはこの程度で十分でしょう。

### パーサーの気分で考える

このように定義されたstmtをパースするparseStmtというものが何らかの方法で文法から生成出来たとします（これはパーサージェネレータやパーサーコンビネータと言われるものを使って実際に作る事ができる）。

このparseStmtは、これを上から順番に探していきます。（この探し方によってパーサーの種類がいろいろあるのですが、ここではFParsecを前提にそうした解説を省きます。厳密な事を知りたい人はコンパイラの教科書でちゃんと勉強しましょう）。

つまり`"sin(2*3.5+0.3)"`という文が与えられると、parseStmtはまずstmtの文法定義を見る。

すると、funcallかexprのどっちかだな、という事が文法に書かれている。
で、funcallを見るとidentifierと'('で始まる、と書いてあるので、とりあえずfuncallだと思ってパースを進める、という風に進みます。
基本的には２つの字句（この場合は`sin`と`(`）までを見てこれらの分岐を判断する、と思っておくと、だいたいの挙動は理解できると思います。
字句とはなんぞやとかはあまり真面目に解説する気が無いので、それっぽい単位と思っておいてください。（なおこの2の数字をどこまで増やすかで文法の種類が決まるが、我らは2（つまり先読み1）しか使わない）

次に`3+4*2`の例を考えてみる。

parseStmtはfuncallかexprのどちらかだ、と書いてあるので、最初の２つの字句を見ると、`3`と`+`になっている。
funcallのルールを見ると`identifier`と`(`と書いてあって、普通3はidentifierとはパースされないように作るので、
これは駄目っぽい（どっちにしろ`(`はマッチしてないので駄目）。

という事でexprの方に行く。
exprの方は〜と見ていくと無事最後まで解釈できるので、これはパースが成功した事になります。

### 文法の書き方の注意

さて、文法は割と自由度が高く、文法的には問題無いが、パーサーには苦手な文法、というものが幾つかあります。
パーサーの気持ちで考えてみると分かりますが、
最初の数個を読んで判断出来ないような文法は苦手です。

例えば以下のexprを、

```
expr = term '+' expr
...
```

次のようにtermとexprを入れ替えても、人間には意味がわかります。

```
expr = expr '+' term
...
```

ですが、パーサーはこういうのは苦手です。左辺のexprが右辺の「一番左」で再帰しているので、「左再帰」とか呼ばれます。
最初の要素を見てもそれがより下の要素に、簡単には確定出来ないような文法は苦手です。

文法を書く時には、「この`|`は一つの字句を先読みする事で判断できるか？」と考えながら書くのが大切です。
なるべく簡単にどれか確定するように記述してある方が、デバッグも簡単ですしパーサーも得意です。

### 文法の違いについてもう少し詳しく

上の左再帰の話が良く分からないというフィードバックをもらったので、少し解説を足してみます。
以下のような文字列に対してパースを試みる事を考えます。

```
1+2+3-4
```

この時に、以下の２つの文法のケースを考えてみます。

**ケース1: termが左にあるケース（expr1と呼ぶ）**

```
expr1 = term '+' expr1
     | term '-'  expr1
     | term
```

**ケース2: exprが左にあるケース（expr2と呼ぶ）**

```
expr2 = expr2 '+' term
     | expr2 '-'  term
     | term
```

この２つのケースで、それぞれ先程の「1+2+3-4」をパースする時にどう違うかを考えてみましょう。
対象は必ずexprで、+のケースなのか-のケースなのか単体の項なのかの、どれなのかを判断する問題と思ってこれを見てみます。

まず「1」を読む時点では何も決まらない。
次に+を読んだ所、つまり「1+」まで読んだ所で両者に違いが出てきます。

expr1の場合は 1+を読んだ時点で、以下のルールの一行目である事が確定します。

```
expr1 = term '+' expr1
     | term '-'  expr1
     | term
```

その結果、1がtermである事も確定します。
この3択のうちのどれかが確定すると、その先に進んでいける事が出来ます。

でもexpr2の場合、この時点では以下の３つのルールのどれなのかが確定しません。（3番目でない事は分かりますが）

```
expr2 = expr2 '+' term
     | expr2 '-'  term
     | term
```

それは、このルールが一番右端、つまり「....-4」まで行って初めて確定するルールだからです。
「...-4」を読むと、これが二番目の'-'のルールだと確定します。
この3択のどれかが確定するのは、「...-4」まで読まないと確定しない訳です。
expr2のルールは、「(1+2+3)-4」という感じで最初のマッチが行われるからです。
ここまで読んで初めて「expr2 - 4」にマッチする訳ですね。
これにマッチする所まで進んでようやく、次の(1+2+3)のマッチングに進めます。

一方でexpr1の方は「1+(2+3-4)」にマッチして、それは「1+expr1」に見える。
これは「1+」に見た時点で確定出来るので、先読みしないでも三択のうちのどれかが確定出来ます。

文法が単純なこのケースでは大した事が無いように見えますが、
実際にはパーサーはこれが本当にexprなのかも分からない状態で読んでいきます。
どこが端かも分からないので、毎回端を疑って全ルールを潜っていってなめる必要があります。
「1+」を読んでも成立するルールが複数あり、「1+2」まで読んでも成立するルールが複数あり、
「1+2+」まで読んでも成立するルールが複数あり、「1+2+3」まで読んでも成立するルールが複数あり…
と探索の木がどんどん深く複雑になっていく。

一方でexpr1は潜っていかなくても最初に`+`を見た時点で確定するので、3択という可能性を一つここで潰す事が出来ます。

より階層が深くなっていくとこの違いは非常に大きく、
expr2のような文法は複雑な文法のどこにマッチするのかを人間が把握するのも大変になりますし、
プログラムが探索する範囲も多くなっていって大変です（そして原理的に確定出来ないものと大変なだけの区別も非常に分かりにくいし、どのくらい探索に時間がかかるようなケースになっているのかを直感的に把握するのも難しい）。

そういう訳で文法ルールを書く時には、今書いているorのルールのどれかがどこまで読めば確定出来るのか、
と考えて、なるべく早く確定出来るようなルールを書いていくように心がけましょう。

### パーサーとはなにか（その2）

さて、パーサーとは文法を元に、文字列を解釈してあっているかどうかを調べるものだと言いましたが、これだと少し不完全です。
プログラマとしては、合っているかどうかだけじゃなくて、各構成要素をもらって、それを処理したい。

だからパーサーとは文字列を解釈して構成要素に分解して返すもの…と言いたい所だけれど、実際はもうちょっと複雑です。

先程の例を見てみましょう。`sin(3+5*2)`という文をパースしたとします。
これをパースした結果は何が返るべきでしょうか？

そもそも、`3+5*2` は文法のうちどの構成要素でしょうか？

sinの引数なのでarglistのように思います。
でもarglistには複数の構成要素があり、そのうちのどれか、という事が知りたい。

そのうちのどれかというと、expr一つのパターンにマッチするのでexprという事になります。

つまり、arglistであると同時にexprでもあるのです。

文法とは定義のされ方から、一つの部分が複数の文法要素のネストしたものになっています。

そこでこうした構造をそのまま返そうとすると、ツリー構造となります。これをExpression Treeと言い、
パーサーの多くは何らかの方法でExpression Treeを返すモードもあります。

ただ普通のパーサーは、各パースの構成要素が確定した段階で、なにかを返すという関数を外から指定する事が出来ます。
だからパーサーというのは、「文字列を読んで構成要素にマッチングしていき、各構成要素が確定した時に指定された値を返していく」ものとなります。

だからパーサーの記述には「文法」と、「それがマッチした時に返すもの」の２つを考えていく必要があります。

パーサーコンビネータの各パーサーを考える時には、「XXという文字列にマッチしてYYを返すもの」という風に、
マッチする対象と返すものの２つに着目すると良いでしょう。

以下少し練習してみます。

### pfloatとfloat_wsを見直してみる

冒頭でやった、以下の文を見てみましょう。

```
let ws = spaces
let float_ws = pfloat .>> ws

run float_ws "1.25 "
```

まずpfloatとはなにかを考えます。pfloatは「"123.45"のような文字列にマッチして、123.45というfloatを返すパーサー」となります。

float_wsはなにか、というと、「"123.45  "のような文字列にマッチして、123.45というfloatを返すパーサー」になります。
この返すのがなにか、というのを意識する必要があります。

どこでそれが決まっているのかを見ると、以下の文です。

```
let float_ws = pfloat .>> ws
```

ここで`.>>`というのが出てきています。これは「左と右のパースを行って、左側の結果を返す」という「パーサー」を新たに作る、という意味です。

マッチングと返すものを別々に考えるのがコツです。
マッチングは「pfloatとws」、返すものは「pfloatと同じ結果」です。

以上をふまえて、課題10で自分が書いたものも何とマッチングして何を返すのか、考えてみてください。


### パーサーコンビネータとはなにか

パーサーコンビネータとは、パーサーのライブラリの一種です。
文法のような記述からパーサーを作ります。
この文法のような記述を単なるホスト言語の言語機能による所が特徴です（言語内DSLと呼ばれる事もあります）。

パーサーコンビネータは、個々のパーサーを関数で表します。
どの関数が「パーサー」になっているかを意識してコードを読むのがポイントです。

パーサーコンビネータには

1. 最初から定義されているパーサー（pfloatなど）
2. パーサーを生成する為の関数（pstring, stringReturnなど）
3. パーサーを合成する演算 (`.>>, >>., .>>., <|>`など)

の３つが提供されているのが普通です（2と3は抽象的には同じものとも見る事が出来ますが、ここでは分けておきましょう）。

2と1を区別して、どれがパーサーなのかに着目するのがパーサーコンビネータのコードを理解するコツとなります。
その区別の為、パーサーを表す関数はpで始めるという紳士協定がなんとなくありますが、公式ドキュメント自体そこまで徹底している訳でも無いので、
あくまで目安という事で良いでしょう（そもそもpstringはパーサーじゃないし）。

返すものを考えなければ、`.>>.`と`<|>`の２つだけで、先程の文法とだいたい同じ事が書けます。（`.>>.`と`.>>`などの違いはこの解説を最後まで読んだあとにTutorialを見ればわかると思いますが、返すものだけが違うので返すものを考えなければどちらでも良い）

例えば以下のような文法を考えましょう。

```
pnumber = pint32
       | pfloat
```

これをそのままFParsecで以下のように書いてみます。

```
let pnumber = pint32
            <|> pfloat
```

するとpfloatの所でエラーになります。これは`<|>`の左側がint32を返すのに右側がfloatを返すので、pnumberとしてはどっちを返して良いかわからない、という意味のエラーです。
簡単のため、どちらもfloatを返すというように変更してみましょう。

その為にはpint32の結果をfloatにキャストする必要があります。
このパーサーの要素が確定した時に返す値を変えるのは、`|>>`という関数です。

```
let pintToF = pint32 |>> (fun i->(float)i)
```

floatにキャストするだけなら、float(i)と関数のように呼び出せるという事を知っていれば、funをかます必要もありません。

```
let pintToF = pint32 |>> float
run pintToF "123"
```

これでパースした結果がfloatになります。これを用いてpnumberは以下のように書けます。

```
let pnumber = pintToF
            <|> pfloat
```

これを以下の文法と見比べると、

```
pnumber = pint
        | pfloat
```

似通っている事がわかるでしょう。

ここで注目したいのは、どこがパーサーか、という所です。

- pint32はパーサー
- `(pint32 |>> float)`もパーサー
- `(pintToF <|> pfloat)`もパーサー

こういう、どこがパーサーかに着目するのは大切です。この例だと少し簡単過ぎますが、もうちょっと複雑になってくると言っている意味がわかるようになるでしょう。

### 課題 10.1 pidentifierを書け

あとから挟まった課題なので、ナンバリング直すの面倒なので小数で10.1に。有理数は稠密なので安心。

簡単の為、isLetterで始まってisLetter か isDigitが続く文字列、という感じのパーサーを書いてください。
これは単に次の解説で必要なだけなので、文字列が返れば細かい仕様はなんでもいいです。

### pfactorを定義してみる

同様にして、factorを定義してみます。
文法は以下のようでした。

```
pfactor = pnumber
       | pidentifier
```

さて、pfactorは何を返すべきでしょうか？それはパーサーコンビネータを使う人、つまり我々が決めないとけません。

pnumberの時はfloatで、identifierの時は変数名の文字列でしょう。

こういう「AまたはB」を表す型は、F#ではそのままDisciminated Unionで定義する事が出来ます。

例えば以下のような型が考えられます。

```
type Factor = Number of float
            | Identifier of string
```

一応Discriminated Unionの復習を簡単にしておくと、右辺が型構築子になります。

だから以下のように使える。

```
let fnum = Number 3.0
let fid = Identifier "hogehoge"
```

fnumもfidも型はFactorとなります。Discriminated Unionについての詳細は以下。

- [Discriminated Unions - F# - Microsoft Docs](https://docs.microsoft.com/en-us/dotnet/fsharp/language-reference/discriminated-unions)
- [Discriminated Unions - F# for fun and profit](https://fsharpforfunandprofit.com/posts/discriminated-unions/#:~:text=In%20F%23%2C%20a%20sum%20type%20is%20called%20a,like%2C%20but%20must%20start%20with%20an%20uppercase%20letter.)


ということで、このFactorを返すようにしてみましょう。

```
let pfactor = (pnumber |>> (fun f-> Number f))
       <|> (pidentifier |>> (fun id-> Identifier id))
```

NumberもIdentifierも関数のように使えるじゃん、と気づけば以下のように書ける事も分かります（慣れないと読みにくいかもしれませんが）

```
let pfactor = (pnumber |>> Number)
       <|> (pidentifier |>> Identifier)

run pfactor "123.4"
run pfactor "abc"
```

これでpnumberでもpidentifierでも、結果はFactorになるのでpfactorの型が確定しました。

先程と同様に、何がパーサーかも考えてみましょう。

- `pnumber`や`pidentifier`はパーサー
- `(pnumber |>> Number)`もパーサー
- pfactorもパーサー

こうやって、個々のパーサーとそれの合成、というように式を解釈してやると、何をやっているかがわかりやすくなります。

上記のコード、何を返すか、という事のあたりで記述はややこしいですが、そこを無視すれば以下のようになります。

```
let pfactor = pnumber
       <|> pidentifier
```

これは元の文法とかなり似通っていると思います。

同様に何を返すのかを無視すれば、以下のexprの文法は、

```
expr = term '+' expr
     | term '-'  expr
     | term
```

以下のようになるでしょう。

```
let pexpr = pterm  .>>. (pstring '+') .>>. pexpr
     <|> pterm .>>. (pstring '-') .>>.  pexpr
     <|> pterm

```

pstringというのがちょっとかっこ悪いですが、そこさえ気にしなければ、元の文法に対して

- 横に並べるのは `.>>.`
- 縦棒は `<|>`

で機械的に置き換えていけばだいたいは同じものなのがわかると思います。

また、上記の文のうち、パーサーがどこかを考えておきましょう。

- `pterm`, `pexpr`はパーサー
- `pstring`はパーサー「では無い！」
- `(pstring '+')`はパーサー（！）
- `pterm .>>. (pstring '+')`はパーサー
- `pterm .>>. (pstring '+') .>>. pexpr`はパーサー

このように、コードをそのままぐちゃぐちゃするのではなく、一旦文法的に考えたあとにそれに対応するようにコードを書いていく（読んでいく）と、
構造がより良く理解できると思います。

このように、文法の記述とプログラムの記述がほとんど同じ、というのがパーサーコンビネータのアイデアです。
一方でこれは単なるF#の関数なので、いろいろな便利関数を作る事ができるというのが、パーサーを文法から自動生成するタイプのライブラリと比べた、
パーサーコンビネータの利点です。

そしてパーサーコンビネータでは、文法にプラスして各項目で何を返すかを考えて、左辺では型が定まるようにプログラムをしてくのが一般的な考え方となります。
両者がorの関係にある場合はDiscriminated Unionでそのまま対応づけられる所がF#でこの手のプログラムを書くメリットです。

### パーサーコンビネータのコードの読み方まとめ

- 対応している文法を考える（書いてみても良い）
- 右辺の各`<|>`のそれぞれにマッチした時に何が返るか考える
- それを統合したなにかとして左辺の型を考える

こんな感じで大きく考えた上で、個々の合成の演算子などを調べていくと良いと思います。


{% capture comment1 %}
**コンビネータ型ライブラリについて**

関数型言語というと、関数がファーストクラスオブジェクトで高階関数がうんぬん、とみんな言います。
ですが現代的な言語だと普通に関数は引数でコールバックなどを渡せるし、mapやreduce（foldかも）とかはたいていの言語に入っているので、
高階関数なんてみんな使ってるじゃん、という話になる。

個人的には何が関数型言語で何が違うか、みたいなのはくだらない話だと思っているのであまり参加する気はありませんが、
このパーサーコンビネータなどはいかにも関数型言語っぽいライブラリなので、ちょっとそういう視点を考えてみる価値はあります。

普通のプログラムの入門では、共通の処理をくくりだして関数というのを定義します。
関数という便宜は考えるにせよ、頭の中ではそれが「展開されたもの」が実行される、と最初のうちは考えます。

一方でパーサーコンビネータの関数はちょっと違った考えを要求します。
実際に実行する時には展開されて実行されると思っても良いのですが、
プログラムの大多数の所でpfloatなどの関数が出てくるけれど、その引数は出てこない。

pfloatやpidentifierなどの関数を合成して新しい関数を作る。
この「引数を渡して展開されて実行するという事から離れて」関数というものを考えるのは、ちょっと論理的なジャンプがあって慣れが必要と思います。

そして合成した結果も関数で、それがどんどん連鎖していくので展開されると頭の中で考えるのはすぐに限界が来てしまう。

単にコールバックを渡すだけ、よりはもうちょっと個々の関数自体を実体のあるものと考えてそれを組み合わせて新しい関数を作っていく、という感じがあると思います。
引数を適用して展開されたものを考えるのではなく、その実行の手前の状態を実体として考えるのがコツです（それこそがまさに関数！）。

物理の人なんかは偏微分の演算子を最初は適用するなにかと考えているけれど途中からそれを独立した実体として考えたりする訓練をやったと思いますが、
それと同じような話ですね。

また関数を合成していく時には、返す型がどうなっているのか、という事を考える必要があるのもここまでやってみてわかると思います。
特に、`<|>`などの合成関数を自分で書く場合は、どういう制約がなくてはいけないのか、
というのを考える必要があり、それらの制約とはなんぞやという事をちゃんと議論するには数学の言葉が必要になります。

以下の`<|>`のシグニチャなどを見るのは教育的でしょう。

[fparsec/Primitives.fs at fdd990ad5abe32fd65d926005b4c7bd71dd2384f · stephan-tolksdorf/fparsec](https://github.com/stephan-tolksdorf/fparsec/blob/fdd990ad5abe32fd65d926005b4c7bd71dd2384f/FParsec/Primitives.fs#L266)
{% endcapture %}
{% include myquote.html body=comment1 %}


### パーサーとパーサーコンビネータの参考文献

この辺にあまり深入りする必要は無いと思いますが、
もし興味があって文法とかパーサーをより本格的に学びたいなら、コンパイラのコースや教科書を読むのが良いでしょう。

無料のオンラインコースでは以下がおすすめ。

[Compilers - edX](https://www.edx.org/course/compilers)

Week3〜Week4が文法とパーサーの真面目な解説です（Week2のLexierも見ておく方が良い）。ただ期限が来るとコンテンツにアクセスできなくなる（もう一回enrollすれば見れるんだけど）のがちょっとかったるい。
自分は以前別の所で勉強した事があるので、復習したい所だけをたまにenrollして見る、という程度の使い方しかしてませんが、見ている範囲ではなかなか出来は良いと思う。

書籍なら虎本を自分はよく参照する。

<iframe sandbox="allow-popups allow-scripts allow-modals allow-forms allow-same-origin" style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//rcm-fe.amazon-adsystem.com/e/cm?lt1=_blank&bc1=000000&IS2=1&bg1=FFFFFF&fc1=000000&lc1=0000FF&t=karino203-22&language=ja_JP&o=9&p=8&l=as4&m=amazon&f=ifr&ref=as_ss_li_til&asins=B00D2WQAE8&linkId=ed4cca3cbb4a5c8c72621a31d3dceb24"></iframe>

これ。コンパイラの教科書としては虎本とDragon bookの２つが有名だが、Dragon bookはkindleになってないので、自分は文法などの話題を調べたくなったら主に手元の虎本を参照しているし、これで十分と思う。
3章のParsingは十分以上の内容になっていると思う（読む時は2のLexical Analysisから読む方が良いかも）。
日本語版もあるけれど読んだ事無いので英語版をおすすめしておく。
別に日本語版でもいいかもしれない。知らない。

どちらも割と本格的な内容なので、ここまで知らんでもなぁ、というものも多く含まれていると個人的には思います。
理論的な所に分量が割かれ過ぎていて、複雑で使われていないが表現力が高いものとかに多くの紙面が割かれ、
逆に実際の実装ではもっと改善が進んでいる話題が入っていない。

パースは割とこうした教科書の話題からはみ出た重要な話題が近年は技術ブログなどでちょこちょこ出てきますが、
そういうのは必要になったら調べるくらいで十分な気もする。

FParsecとかパーサーコンビネータをもっとちゃんと勉強したい人向けにも、参考文献リンクを貼っておきます

- [The "Understanding Parser Combinators" Series · F# for Fun and Profit](https://swlaschin.gitbooks.io/fsharpforfunandprofit/content/series/understanding-parser-combinators.html)  
FParsecを意識した解説になっているので連続性はある。ただし具体的なコードが多い分、本質が見えにくい気もする。コード読むのが得意な人向け。
- [Monadic Parser Combinators.pdf](http://www.cs.nott.ac.uk/~pszgmh/monparsing.pdf)  
理論的な事だけ知りたいならこれが良いが、実装言語がGoferとか言われても…という気はするが、Haskellの入門くらいかじっておけば雰囲気で読める。数学的な記述に近くて本質が分かりやすい。著者のGraham HuttonとErik Meijerは数年後に[parsec-paper-letter.pdf](https://www.microsoft.com/en-us/research/wp-content/uploads/2016/02/parsec-paper-letter.pdf)というのも書いているが、勉強目的なら最初のだけで良いでしょう。関連文献などもあるし論文の体裁が得意な機械学習屋はこういう方が好みかもしれない。


### 課題11: projectのパーサーを書こう

以上の内容を踏まえて、projectのパーサーを書きます。

`project`から始まり、 `(`が続き、カラム名のカンマ区切りが続き、`)`で終わります。

パーサーの名前はpProjectとします。

### susumu2357氏のコードを考えてみる

パーサーコンビネータの理解を深める演習問題として、この課題11でsusumu2357氏が書いてきたコードが何を返すのかを見てみましょう。
以下のようなコードを書いてきました。

[Q11 · susumu2357/fsharp-lesson@4f3ccc6](https://github.com/susumu2357/fsharp-lesson/commit/4f3ccc6d759036afaee1b5d66607607c7db4e0b5)

抜粋すると以下になります。

```
let eachColumn = sepBy pColumn2 (pstring ",")
let pProjcet = ws2 >>. (str_ws2 "project") >>. (str_ws2 "(") >>. eachColumn .>> (str_ws2 ")")
```

str_ws2、ws2がなにかなどはこれだけではわかりませんが、それでもこのコードをだいたい理解する事は出来ます。
pColumn2は `"[専門]"` とマッチして  `"専門"`を返すパーサーです。

同様にeachColumnやpProjectについて、「XXとマッチしてYYを返すパーサー」のXXとYYを考えてみましょう。

eachColumnは `"[専門], [学年]"`とマッチして`"専門", "学年"`という感じの文字列のリストを返すパーサーです（これはsepByの知識が要ります。チュートリアルでその辺の解説を読んでみて下さい）。

pProjectは何とマッチして何を返すか？
マッチするのはまぁいいと思います。問題はなにを返すかです。

`>>.`は左右のパーサーとマッチして右側の結果を返します。

```(str_ws2 "project")```は"project"とマッチして"project"を返します。

ws2はたぶん0個以上の空白にマッチする感じでしょう（実際は全角空白にもマッチする）

`>>.`が並んでいるけれど、これは左側から順に合成されていく。（[Function associativity and composition - F# for fun and profit](https://fsharpforfunandprofit.com/posts/function-composition/)参照）

だから、以下は

```
ws2 >>. (str_ws2 "project") >>. (str_ws2 "(")
```

カッコをつけると以下の意味になります。

```
(ws2 >>. (str_ws2 "project"))
   >>. (str_ws2 "(")
```

最初のカッコでは"project"が返るけれど、その次の`>>.`の合成で`"("`が返るので、左側で返った"project"は捨てられます。
こうして考えると、実は一番右の`>>.`以外の返りは意味が無い事が分かります。
だから一番右以外は`>>.`でも`.>>`でも`.>>.`でも結果は変わらない。

そして肝心の、元の式での一番右の`>>.`は以下。（その右隣は`.>>`とドットが左側なのに注意）

```
let pProjcet = ... >>. eachColumn .>> (str_ws2 ")")
```

という事で`... >>. eachColumn`の結果はeachColumnが返ります。つまり文字列のリストです。

で、これと `.>> (str_ws2 ")")`の合成結果はドットが左側についているので左が返ります。
つまり結局文字列のリストが返る訳です。

つまり全体としては、pProjectは「`"project([専門], [学年])"`にマッチして`"専門", "学年"`のリストを返すパーサー」となります。

ここまで見ると、左側の間の合成に使っている演算子は`>>.`になっていますが、`.>>`であっても`.>>.`であっても結果は結局捨てられるのでどちらでも良い事が分かります。

慣れないとこれを読み解くのは大変ですね。


### 課題12: filterのパーサーを書こう

同様にフィルタを書きます。パーサーの名前はpFilterにします。
`filter([専門] = "物理")`をパースします。


引数は将来的にはandやorも対応しますが、まずは条件は一つにします。
つまり、カッコの中はいつも `[カラム名] = "文字列"` の形で、カンマとかも無しで一つだけです。


### Expressionのパーサーとその返すべき型を考える

さて、課題12まででfilterとprojectのパーサーが出来ました。
次にこのfilterかprojectのどちらかをパースするパーサーを作りたい。

filterとprojectのどちらか、という事を表す言葉がなにか欲しい所ですね。
プログラムの用語ではステートメントかExpressionとかがよく使われます。
第二回で作ろうとしている物の参考にしている[LEAP RDBMS : Home](http://leap.sourceforge.net/)ではexpressionと呼んでいるので、ここでもExpressionとしておきましょう。

という事でExpressionをパースするパーサー、pExpressionを作りたい。
ですが、単純に以下のように書くと、返す型が違う、と怒られるはずです。

```
let pExpression = pFilter
      <|> pProject
```

pFilterとpProjectのどちらなのかの情報を保持しつつ、pExpressionとしては共通の型を返す必要があります。
これはいかにもDiscriminated Unionの出番です。

最終的に目指すのは、以下のような型を定義し、

```
type ProjectExpression =
  // なにか埋める

type FilterExpression =
  // なにか埋める

type Expression =
| ProjectExpression
| FilterExpression
```

pExpressionが `string->ParseResult<Expression, unit>` となるようにします。

でも突然ここから始めるのは大変かもしれないので、まずはprojectの型、、、は簡単過ぎるので、filterの型を作って返す所から始めましょう、

### 課題13: pFilterのパーサーの返す型を作ろう

まずFilterExpressionの型を考えます。レコード型で、この処理を行う（deedleとつなげる事をイメージ）時に必要になる物を考えます。
課題8のfilter関数の引数を見て、それに必要な情報が含まれるような型にすると良いでしょう。

で、次にpFilterがFilterExpressionを「返す」ようにパーサーを変更します。

返す方法としては、チュートリアルの`|>>`, `pipe2`などを参考にしてみてください。

{% capture singlecase_union %}
**Single case unionについて**  

この課題ではSingle case unionを使っている人が多かったのですが、皆わりと混乱しているようで、case identifierの名前が意味が分からなくなってしまっているケースが散見されます。
Single case unionではコンベンションとして型名とcase identifierは同じ名前にする事になっています。
意味的におかしい変な名前にしてしまうなら、素直にコンベンションに従うのが良いでしょう。

その辺の話について、ブログを書きましたので参照ください。＞ [blog: F#のSingle case union入門](https://karino2.github.io/2022/08/01/single_case_union.html)
{% endcapture %}
{% include myquote.html body=singlecase_union %}


### 課題14: pProjectも型を作って返すようにし、projectとfilterの両方をパースするpExpressionを作る

次にpProjectでも同じ作業をして、最後にpExpressionからExpressionを返すようにしましょう。

### 課題15: 両者のパースをくっつけて課題5のPlayDeedleとくっつけよう

pExpressionを課題5で作ったfilterやprojectとつなげてみましょう。
このくらいならまだScratch.fsxに全部書いてしまって良いと思います。

課題5の段階では引数に型が無かったと思いますが、課題15で作ったProjectExpressionやFilterExpressionを引数にとるように変更してつなげましょう。

以下が動く感じのrunExprを作ります。結果はDeedleのFrameを返すでいいでしょう。

```
runExpr "project([場所], [学年])" df

runExpr "filter([専門] = \"数学\")" df
```

{% capture comment1 %}
**基本的な言語処理の構造を考える**  

無事課題15まで終えてみると、パースをどうしているか、というのは、定義した型のデータができるとあまり関係ない事が分かります。
例えばArguで引数を処理して同じ構造を作っても、文字列をパースしても、プログラムのその他の大多数の場所には大した影響は無い。

これはこの課題に限らず、言語処理系のような処理では一般的に言える事です。

開発をする時にも、パース結果の型さえ定義しておけば、パーサーの実装は後回しにしてScratch.fsx上でデータを構築して食わせてやる事で、
以後の処理の開発は出来ます。

この手のプログラムを、

1. パースをして定義した型のデータを作る
2. 定義した型のデータを使って処理をする

と大きく２つの分離した要素にプログラムを構成し、その間を「定義した型」がつなぐ感じでプログラムを構成するのは、応用の効く構成方法です。
なんとなく実装すると両者がくっついてしまうのですが、間に型を定義して両者を切り離すのがポイントです。

一度こうした構成を作る体験をしておくと同じようなプログラムを作る時にも応用が効くので、次の第二回でこうした構成をもっと本格的な例で体験する事にします。

{% endcapture %}
{% include myquote.html body=comment1 %}

## 第一回の終わりに

これで第一回は終わりです。
ライブラリを少しつついてチュートリアルやサンプルを見て使い方を覚える、という流れには慣れてきたでしょうか。

次の第二回から本格的にプログラムを開始していきます。

