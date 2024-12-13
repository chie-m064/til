# コメント投稿、削除機能のajax化

## 概要
* コメント投稿後、コメントフォームは空の状態になること。
* コメントは新しいものから上から順に表示されること。
* 空でコメントを投稿した際に「コメントを入力してください」というバリデーションエラーメッセージが表示されること。
* 自分が投稿したコメントのみ、編集・削除ボタンが表示されること。
* コメントの削除ボタンを押すと、「削除しますか？」と確認が表示されること。
* コメントを削除できること。
* 投稿時、削除時のフラッシュメッセージは表示されないこと。

## Turbo Stream の代表的なアクション
Turbo Streams は、HTMLの断片をリアルタイムに更新するための強力なツールです。

JavaScript の知識がなくても、動的な更新を実現できます。Turbo Streams のアクションは、特定の要素をどのように操作するかを定義します。

代表的なアクションには、 append、prepend、replace、update、remove、before、after があります。
これらのアクションを使用することで、ユーザーインターフェースを動的に操作し、ユーザーエクスペリエンスを向上させることができます。
以下に各アクションについて詳しく説明します。

### Append
Appendアクションは、指定した要素の内部に新しいコンテンツを追加
```ruby
<%= turbo_stream.append "comments" do %>
  <%= render @comment %>
<% end %>
```
このコードは、comments という ID を持つ要素の内部に、@comment の部分テンプレートを追加します。
これにより、新しいコメントがリストの最後に追加されます。

### Prepend
Prepend アクションは、指定した要素の内部の最初に新しいコンテンツを追加
```ruby
<%= turbo_stream.prepend "comments" do %>
  <%= render @comment %>
<% end %>
```
このコードは、comments という ID を持つ要素の内部の最初に、@comment の部分テンプレートを追加します。
これにより、新しいコメントがリストの先頭に追加されます。

### Replace
Replace アクションは、指定した要素を完全に置き換える
```ruby
<%= turbo_stream.replace "comment_#{@comment.id}" do %>
  <%= render @comment %>
<% end %>
```
このコードは、comment_#{@comment.id} という ID を持つ要素を、@comment の部分テンプレートで置き換えます。

### Update
Update アクションは、指定した要素の内容を更新します。
```ruby
<%= turbo_stream.update "comment_likes_#{@comment.id}" do %>
  <%= @comment.likes_count %>
<% end %>
```
このコードは、comment_likes_#{@comment.id} という ID を持つ要素の内容を、@comment.likes_count で更新
いいね数がリアルタイムで更新

### Remove
Remove アクションは、指定した要素を削除
```ruby
<%= turbo_stream.remove "comment_#{@comment.id}" %>
```
このコードは、comment_#{@comment.id} という ID を持つ要素を削除
これにより、指定したコメントが DOM から削除されます。Remove アクションは、不要になった要素を DOM から取り除く際に使用

### Before
Before アクションは、指定した要素の直前に新しいコンテンツを追加
```ruby
<%= turbo_stream.before "comment_#{@comment.id}" do %>
  <%= render @new_comment %>
<% end %>
```
このコードは、comment_#{@comment.id} という ID を持つ要素の直前に、@new_comment の部分テンプレートを追加
新しいコメントが指定した位置の前に追加される

### After
After アクションは、指定した要素の直後に新しいコンテンツを追加
```ruby
<%= turbo_stream.after "comment_#{@comment.id}" do %>
  <%= render @new_comment %>
<% end %>
```
このコードは、comment_#{@comment.id} というIDを持つ要素の直後に、@new_comment の部分テンプレートを追加

## app/views/comments/_comment.html.erbを編集する
```ruby
<tr id="comment-<%= comment.id %>">
  ... 省略 ...
  <% if current_user.own?(comment) %>
    <td class="action">
      <ul class="list-inline justify-content-center" style="float: right;">
        <li class="list-inline-item">
          ... 省略 ...
        </li>
        <li class="list-inline-item">

        / comment_path(comment):削除対象のコメントへのURLを指定する
        / date: { turbo_method: :delete}:Turboフレームワークを利用し、DELETE HTTPメソッドを使うよう指定(destroy.turbo_stream.erbを実行)
        / date: {tourbo_confirm: t(...)}:削除前に確認メッセージを表示
          <%= link_to comment_path(comment), class: "delete-comment-link", data: { turbo_method: :delete, turbo_confirm: t('defaults.delete_confirm') } do %>
            <i class="bi bi-trash-fill"></i>
          <% end %>
        </li>
      </ul>
    </td>
  <% end %>
</tr>
```
## app/views/comments/create.turbo_stream.erbを生成・編集する
```ruby
/@commentにエラーがあるかどうか確認 
<% if @comment.errors.present? %>

/エラーが存在する場合、comment-formというIDを持つ要素を新しいフォームの内容で置き換える
  <%= turbo_stream.replace "comment-form" do %>
    <%= render 'comments/form', comment: @comment, board: @comment.board %>
  <% end %>

/ エラーがない場合
<% else %>

/ 新しいコメントをコメントリストの最初に追加。table-commentというIDを持つ要素の内部の先頭に、新しいコメントの部分テンプレートを追加
  <%= turbo_stream.prepend "table-comment" do %>
    <%= render 'comments/comment', comment: @comment %>
  <% end %>

/ 新しいコメントが追加されたあと、コメントフォームをリセットして空の状態にする
  <%= turbo_stream.replace "comment-form" do %>
    <%= render 'comments/form', comment: Comment.new, board: @comment.board %>
  <% end %>
<% end %>
```
## app/views/comments/create.turbo_stream.erbを生成・編集する
```ruby
<%= turbo_stream.remove "table-comment" %>
```
