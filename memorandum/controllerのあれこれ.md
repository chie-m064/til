# controllerとはなんぞや
Railsアプリのコントローラには、主に以下の3つを記載します。
これらを理解することで、コントローラの役割を正しく把握し、アプリを構築できるようになります。

## 1.アクション
* アクションとは、HTTPリクエスト（GET, POST, PATCH, DELETEなど）に応じて、どの処理を実行するかを決定するメソッドです。
* 例えば、indexアクションはリソース（例: ユーザーや記事）の一覧を表示し、showアクションは特定のリソースを表示します。

```ruby
class ArticlesController < ApplicationController
  def index
    @articles = Article.all
  end

  def show
    @article = Article.find(params[:id])
  end

  def new
    @article = Article.new
  end

  def create
    @article = Article.new(article_params)
    if @article.save
      redirect_to @article, notice: '記事を作成しました。'
    else
      render :new, alert: '記事の作成に失敗しました。'
    end
  end

  private

  def article_params
    params.require(:article).permit(:title, :content)
  end
end
```
## 2.HTTPリクエストに応じた処理
* 各アクションには、リクエストの種類（GET, POST, など）に対応する処理を記載します。
* 主に使われるアクションは以下です。

|アクション名 	|HTTPメソッド	  |役割                             |
|:---       |:---         |:---                             |
|index      |	GET	        |リソースの一覧を表示する             |
|new	      | GET	        |新しいリソースの作成フォームを表示する |
|create     |	POST	      |新しいリソースを保存する             |
|edit	      | GET      	  |既存のリソースの編集フォームを表示する  |
|update	    | PATCH/PUT	  |既存のリソースを更新する             |
|destroy  	| DELETE   	  |既存のリソースを削除する             |

## 3.Strong Parameters
* ユーザーが送信するデータを安全に受け取るため、paramsの中で許可されたデータを明示的に定義します。

```ruby
def article_params
  params.require(:article).permit(:title, :content)
end
```

## Railsのジェネレーターの活用
コントローラを作成する際に、以下のようなコマンドを使うと便利です。
```ruby
$ rails generate controller Articles index show
```
