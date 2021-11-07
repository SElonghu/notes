# ruby on rails笔记

## 1. 路由文件

​		/config/routes.rb文件，使用特殊的DSL（Domain-Specific Language，领域专属语言）编写，告诉rails把访问请求发往哪个控制器和动作。

```ruby
Rails.application.routes.draw do
    get 'welcome/index'
    root 'welcome#index'
end
```

​		root 'welcome#index'告诉 Rails 对根路径的访问请求应该发往 welcome 控制器的 index 动作，get 'welcome/index'告诉 Rails 对 http://localhost:3000/welcome/index 的访问请求应该发往 welcome 控制器的 index 动作。后者是之前使用控制器生成器创建控制器（bin/rails generate controller Welcome index）时自动生成的。

## 2. 资源（resource）

​		资源是一个术语，表示一系列类似对象的集合，如文章、人或动物。资源中的项目可以被创建、读取、更新和删除，这些操作简称 CRUD（Create, Read, Update, Delete）。

​		Rails 提供了 resources 方法，用于声明标准的 REST 资源。把 articles 资源添加到 config/routes.rb 文件，此时文件内容应该变成下面这样：

```ruby
Rails.application.routes.draw do
    get 'welcome/index'
    #:articles表示articles这个控制器
    resources :articles
end
```

​		执行 bin/rails routes 命令，可以看到所有标准 REST 动作都具有对应的路由。输出结果中各列的意义稍后会作说明，现在只需注意 Rails 从 article 的单数形式推导出了它的复数形式，并进行了合理使用。

```
$ rails routes
	  Prefix Verb   URI Pattern                  Controller#Action
    articles GET    /articles(.:format)          articles#index
             POST   /articles(.:format)          articles#create
 new_article GET    /articles/new(.:format)      articles#new
edit_article GET    /articles/:id/edit(.:format) articles#edit
     article GET    /articles/:id(.:format)      articles#show
             PATCH  /articles/:id(.:format)      articles#update
             PUT    /articles/:id(.:format)      articles#update
             DELETE /articles/:id(.:format)      articles#destroy
        root GET    /                            welcome#index
```

### 2.1. 声明的资源如何使用

​		第一步：创建资源对应的控制器

```
$ rails generate controller Articles
#声明控制器时名称大写开头，并生成ArticlesController类，该类继承于ApplicationController
```

​		第二步：在控制器内声明REST方法

```ruby
class ArticlesController < ApplicationController
    def new
    end
end
```

​		第三步：创建控制器方法对应的视图

```
$ cd app/views/articles/
$ vim new.html.erb
```

## 3.视图(view)

### 3.1.提供视图功能的gem

ActionView: actionview gem

### 3.2.视图所在目录

- app/views/
- 其他自定义目录：append_view_path method （很少用）

### 3.3.视图分类

- Template: 如index.html.erb（一般用于方法对应的视图）
- Partial: 如_user.html.erb（局部模板，用于嵌入重复的模板到其他页面）
- Layouts: 如application.html.erb（布局模板、父模板，用于每个页面通用视图）

### 3.4.ERB解析

```ruby
<% %> # => 没有输出，用于循环，遍历，赋值等操作
<%= %> # => 输出内容
#注释：<%# hello world %>
```

```ruby
example:
<% @users.each do |user| %>
	<p><%= user.username %></p>
<% end -%>   #这里end后面不加-，会在页面生成一个换行符
example2:
<% a = 1 %>
<%= a %>  # => 1
```

### 3.5.视图命名

index.html.erb

- index: action对应的名称或partial名称
- html: 输出文件类型
- erb: 解释引擎（由erb的gem实现的，所以也可在Gemfile添加使用其他解释引擎）

### 3.6.helper方法

#### 3.6.1.render

**作用**

- 生成HTTP response
- 渲染和解释子视图（sub-view）

**controller里的render**

作用：渲染action对应的view，组件当前request的response

```ruby
#常见用法，渲染一个action的view
def index
    #...
end
def search
    @users = User.page(params[:page] || 1).per_page(params[:per_page] || 5).order("id desc").where(["username like ?", "%#{params[:username]}%"])
    render action: :index
end

#其它用法
render text: 'ok' #返回text类型数据
render json: @users #返回json类型数据
render xml: @users #返回xml类型数据
render file: 'app/views/users/index' #用相对路径返回一个视图index.html.erb
render partial: 'app/views/users/search' #用相对路径返回一个模板视图_search.html.erb
```

return: 由于**一个action只能有一个render或redirect_to方法**，所以执行完一次render方法后可以通过return方法不继续执行后面的render方法，如：

```ruby
def index
    ...
    if @users
        render text: 'ok'
        return #没有这个return方法的话，会接着执行下面的render方法，就会报错
    end
    render json: @users
end
```

**view里的render**

作用：渲染子视图sub-view

```ruby
#一般用法
# app/views/shared/_menu.html.erb

# app/views/users/index.html.erb在这个view里渲染模板_menu.html.erb
<#= render "shared/menu" %>  相对路径
<h1>Productions</h1>
...

#参数
<%= render "shared/menu" %> #缩写方式指定需要渲染的模板视图
<%= render partial: "shared/menu" %> #hash方式指定需要渲染的模板视图
<%= render "shared/menu", locals: { val_1: 'value_1' } %> #传递变量进menu模板视图

#遍历输出
<% @users.each do |user| %>
   <%= render partial: "one_user", locals: {user: user} %>
<% end %>
# or
<%= render "one_user", collection: @users, as: :user %> # 这种方式和上面的遍历输出一样，将user变量传递进one_user模板视图中
```

**子视图能够访问当前视图或者aciton的所有实例变量**

#### 3.6.2.layout

作用：定义view的父视图

```ruby
# app/views/layouts/application.html.erb
<body>
    ...
    <%= yield %>
	...
</body>
```

```ruby
# app/controllers/users_controller.rb
class UsersController < ApplicationController
    layout 'application'  #layout指定了users视图的父模板为application，默认就是application所以可以不显示指定
    def index
        # ...
    end
end
```

其它用法：

```ruby
#可单独指定action对应视图使用的模板
class Admin::UsersController < ApplicationController
    layout 'admin' #定义users控制器内的所有action对应的视图使用admin模板
    def index
        #...
    end
    def new
        render layout: 'application'  #new方法的视图使用application作为模板
    end
    def edit
        render layout: false  #edit方法的视图不使用任何模板
    end
end
```

```ruby
#layout运行时逻辑(每个action动态采用模板)
class Admin::UsersController < ApplicationController
    layout :generate_layout #使用generate_layout方法指定的模板，这里的symbol表示方法名称
    def index
        #...
        @current_user = :normal #使用application父模板
    end
    def new
        @current_user = :admin #使用admin父模板
    end
    def edit
        render layout: false  #edit方法的视图不使用任何模板
    end
    
    private
    def generate_layout
        @current_user == :normal ? 'application' : 'admin'
    end
end
```

```ruby
#layout还可以继承
class Admin::BaseController < ApplicationController
    layout 'admin'
end
class Admin::BlogsController < Admin::BaseController
    # layout依然是admin
end
class Admin::UsersController < Admin::BlogsController
    layout 'user' #指定父模板为user
end
```



## 4.控制器(controller)

### 4.1提供控制器功能的gem

ActionController: actionpack gem