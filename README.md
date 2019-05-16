# README

Rails new blog
- cd blog
- rails server
- rails generate controller Welcome index
- views/welcome/index.html.erb add: <h1>Hello, Rails!</h1>
- config/routes.rb add: + resources :articles
                        + root 'welcome#index'
- rails generate controller Articles
- controllers/articles_controller.rb add: + def new
                                            end
- views/articles/new.html.erb add: + <h1>New Article</h1>
+ <%= form_with scope: :article, url: articles_path, local: true do |form| %>
+   <p>
+     <%= form.label :title %><br>
+     <%= form.text_field :title %>
+   </p>
+
+   <p>
+     <% =form.label :text %><br>
+     <%= form.text_area :text %>
+   </p>
+ 
+   <p>
+   <%= form.submit %>
+   </p>
+ <% end %>
- controllers/articles_controller.rb add: + def create
                                              render plain: params[:article].inspect
                                            end
- rails generate model Article title:string text:text
- rails db:migrate
- controllers/articles_controller.rb add: + def create
                                             @article = Article.new(article_params)
                                             @article.save
                                             redirect_to @article
                                            end

                                            private
                                            def article_params
                                              params.require(:article).permit(:title, :text)
                                            end
- controllers/articles_controller.rb add: + def show
                                              @article = Article.find(params[:id])
                                            end
- views/articles/show.html.erb add: + <p> 
                                        <strong>Title:</strong>
                                        <%= @article.title %>
                                      </p>

                                      <p>
                                        <strong>Text:</strong>
                                        <%= @article.text %>
                                      </p>
- controllers/articles_controller.rb add: + def index
                                              @articles = Article.all
                                            end
- views/articles/index.html.erb add: + <h1>Listing articles</h1>
                                      <table>
                                        <tr>
                                          <th>Title</th>
                                          <th>Text</th>
                                        </tr>

                                        <%= @articles.each do |article| %>
                                          <tr>
                                            <td><%= article.title %></td>
                                            <td><%= article.text %></td>
                                            <td><%= link_to 'Show', article_path(article) %></td>
                                          </tr>
                                        <% end %>
                                      </table>
- views/welcome/index.html.erb add: + <%= link_to 'My Blog' ,controller: 'articles' %>
- views/articles/index.html.erb add: + <%= link_to 'New article', new_article_path %>
- views/articles/new.html.erb add: + <%= link_to 'Back', articles_path %>
- views/articles/show.html.erb add: + <%= link_to 'Back', articles_path %>
- models/article.rb add: + validates :title, presence: true,
                                             length: { minimum: 5 }
- controllers/articles_controller.rb add: + def create
                                            ...
                                              ..redirect_to @articles
                                              else
                                                render 'new'
                                              end
- views/articles/new.html.erb add: + ..|form| %>
                                      <% if @article.errors.any? %>
                                        <div id="error_explaination">
                                          <h2>
                                            <%= pluralize(@article.errors.count, "error") %>
                                            prohibited this article from being saved:
                                          </h2>
                                          <ul>
                                            <% @article.errors.full_messages.each do |msg| %>
                                              <li><%= msg %></li>
                                            <% end %>
                                          </ul>
                                        </div>
                                      <% end %>
- controllers/articles_controller.rb between the new and create actions add: + def edit
                @article = Article.find(params[:id])
             end
- views/articles/edit.html.erb add: + <h1>Edit article</h1>
                                      <%= link_to 'Back', articles_path %>
- controllers/articles_controller.rb between the create and private method add: + def update
                                                                                    @article = Article.find(params[:id])

                                                                                    if @article.update(article_params)
                                                                                      redirect_to @article
                                                                                    else
                                                                                      render 'edit'
                                                                                    end
                                                                                  end
- views/articles/index.html.erb add: + <td><%= link_to "Edit", edit_article_path(article) %></td>
- views/articles/show.html.erb add: + <% link_to 'Edit', edit_article_path(@article) %>
- views/articles/_form.html.erb add:
  <%= form_with model: @article, local: true do |form| %>

    <% if @article.errors.any? %>
      <div id="error explaination">
        <h2>
          <%= pluralize(@article.errors.count, "error") %>
          probihited this article from being saved:
        </h2>
        <ul>
          <%= @article.errors.full_messages.each do |msg| %>
            <li><%= msg %></li>
          <% end %>
        </ul>
      </div>
    <% end %>

    <p>
      <%= form.label :title %>
      <%= form.text_field :title %>
    </p>

    <p>
      <%= form.label :text %>
      <%= form.text_area :text %>
    </p>

    <p>
      <%= form.submit %>
    </p>
  <% end %>
- views/articles/new.html.erb rewriting it as follow: 
  <h1>New article</h1>
  <%= render 'form' %>
  <%= link_to 'Back', articles_path %>
- controllers/articles_controller.rb above the private method add: + def destroy
                                                                        @article = Article.find(params[:id])
                                                                        @article.destroy

                                                                        redirect_to articles_path
                                                                      end
- views/articles/index.html.erb add: + ..'Edit'..
                                      <td><%= link_to 'Destroy', article_path(article), method: :delete, data: { confirm: 'Are you sure?' } %></td>


- Adding COMMENT MODEL:
- rails g model Comment commenter:string body:text article:references
- models/comement.rb add: + belongs_to :article
- rails db:migrate
- models/article.rb add: has_many :comments
- config/routes add: + resources :articles do
                          resources :comments
                       end
- rails g controller Comments
- views/show.html.erb add: + ..@article.text %>
                            </p>
+ <h2>Add a comment</h2>
<%= form_with(model: [ @article, @article.comments.build ], local: true) do |form| %>
  <p>
    <%= form.label :commenter %><br>
    <%= form.text_field :commenter %>
  </p>

  <p>
    <%= form.label :body %><br>
    <%= form.text_area :body %>
  </p>

  <p>
    <%= form.submit %>
  </p>
<% end %>
- controllers/comments_controller.rb add: 
+ def create
  @article = Article.find(params[:article_id])
  @comment = @article.comments.create(comment_params)
  redirect_to article_path(@article)
  end

  private
    def commnent_params
      params.require(:comment).permit(:commenter, :body)
    end
- views/articles/show.html.erb add: + ..@article.text %>
                          </p>
  <h2>Comments</h2>
  <% @article.commemnts.each do |comment| %>
    <p>
      <strong>Commenter:</strong>
      <%= comment.commenter %>
    </p>

    <p>
    <strong>Comment:</strong>
    <%= comment.body %>
    </p>
  <% end %>
  - views/comments/_comment.html.erb add: 
    <p>
      <strong>Commenter:</strong>
      <%= comment.commenter %>
    </p>

    <p>
    <strong>Comment:</strong>
    <%= comment.body %>
    </p>
  - views/articles/show.html.erb edit as:
  ...@article.text %>
  </p>

  <h2>Comments</h2>
  <%= render @article.comments %>
  - views/comments/_form.html.erb add: 
  <%= form_with(model: [ @article, @article.comments.build ] local: true) do |form| %>
    <p>
      <%= form.label :commenter %>
      <%= form.text_field :commenter %>
    </p>

    <p>
      <%= form.label :body %>
      <%= form.text_area :body %>
    </p>

    <p>
      <%= form.submit %>
    </p>
  <% end %>
- views/articles/show.html.erb edit as:
...@article.comments %>

<h2>Add a comment:</h2>
<%= render 'comments/form' %>
- views/comments/_comment.html.erb add:
<%= comment.body %>
</p>

<p>
  <%= link_to 'Destroy Comment', [comment.article, comment], method: :delete, data: { confirm: 'Are you sure?' } %>
</p>

- controllers/comments_controller.rb add:
def destroy
  @article = Article.find(params[:article_id])
  @comment = @article.comments.find(params[:id])
  @comment.destroy
  redirect_to article_path(@article)
end
- models/article.rb add: has_many :comments, dependent: :destroy
- controllers/aricles_controller add: + http_basic_authenticate_with_name: "dhh", password: "secret", except: [:index, :show]
- controllers/comments_controller.rb add: + http_basic_authenticate_with_name: "dhh", password: "secret", only: :destroy


