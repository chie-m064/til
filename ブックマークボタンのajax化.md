# 　ブックマークボタンのajax化

## 概要
掲示板のブックマーク登録・ブックマーク解除を非同期でできるように実装

## gem 'torbo-rails'
gem 'turbo-rails'は、Railsアプリケーションを高速かつインタラクティブにするためのgemです。
リンクやフォーム送信を自動的にキャプチャし、ページ全体の再読み込みを避けて部分更新を行うTurbo Drive、ページの特定部分だけを動的に更新するTurbo Frames、リアルタイムでページの一部を更新しサーバーからのデータを即座にユーザーインターフェースに反映させるTurbo Streamsの3つの主要な機能があります。
これにより、パフォーマンスが向上し、滑らかなページ遷移が可能になります。

## app/controllers/bookmarks_controller.rb
```ruby:
class BookmarksController < ApplicationController
  def create
    @board = Board.find(params[:board_id])
    current_user.bookmark(@board)
  end

  def destroy
    @board = current_user.bookmarks.find(params[:id]).board
    current_user.unbookmark(@board)
  end
end
```
上記コードはcreate, destroyアクションから redirect_to boards_path ... を削除することでリダイレクトされなくなり、対象アクションのビューファイルが呼ばれるようになります。app/views/boards/_unbookmark.html.erb, app/views/boards/_bookmark.html.erbにあるlink_toのオプションに data: { turbo_method: :xxxx } が記述されていることで、リダイレクトしない際に xxxxx.turbo_stream.erbファイルを探してレスポンスするようになります。

## app/views/bookmarks/create.turbo_stream.erbを生成・編集する
```ruby
#turbo_stream.replace の引数に渡されているID属性、bookmark-button-for-board-xxx（xxxにはコントローラーから渡ってきた@boardのidの値が渡されます）
<%= turbo_stream.replace "bookmark-button-for-board-#{@board.id}" do %>

#対象のDOMをブロック内のものと置き換え（replace）ます。
  <%= render 'boards/unbookmark', board: @board %>
<% end %>
```

## app/views/bookmarks/destroy.turbo_stream.erbを生成・編集する
```ruby
<%= turbo_stream.replace "unbookmark-button-for-board-#{@board.id}" do %>
  <%= render 'boards/bookmark', board: @board %>
<% end %>
```
