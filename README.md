# Ruby on rails

## Table of Contents

1. [Basic](#basic)
2. [Routes](#routes)
3. [Database](#database)
4. [Console](#console)
5. [Model](#model)
6. [Authentication](#authentication)
7. [Testing](#testing)

## Basic

### create project name ‘blog’

    rails new blog

### run server

    rails s

### update Gemfile

    bundle install

### show all routes

    rails routes

note. `link` use in `slim` = `###_path` [ดูแล้วเติม_path]

## Routes

config/routes.rb

```ruby
Rails.application.routes.draw do
    # home page
    root 'articles#index'
    # others page
    get "/articles", to: "articles#index"
end
```

### generate controller

    bin/rails generate controller Articles index

## Database

### create file for database name ‘…\_create_article’

    rails g migration CreateArticle

### create database (create schema.rb)

    rails db:migrate

in app/models

Create class Article < ApplicationRecord

## Console

### run in console

    rails -c

or

    irb

```irb
Article.new
Article.create(title: “Article B”, body: “Another Article”)
Article.count
Article.first
Article.second
…
Article.last
Article.where(title: ’This is ’)
Article.all
a.attribute
```

### use rbenv

    rbenv init

## Model

```ruby
class Article < ApplicationRecord

    has_many :comments

    # validates -> rails own check
    validates :body, length: { minimum: 4 }
    # validate -> your own check
    validate :no_bad_words_in_title

    # nomal function
    def say_hello
        # return 'Hello'
        'Hello'
    end

    # validate function
    def no_bad_words_in_title
        if title.downcase.include?('bad')
            # throw error
            errors.add(:title, 'cannot contain bad words')
        end
    end
end
```

### generate model

    bin/rails generate model Article title:string body:text

### display error on web page

```slim
<% flash[:error]&.join(', ') %>
```

`&` = check ว่ามี errorไม

## Authentication

### add bunde authentication

    rails generate devise:install

### generate Admin

    rails generate devise Admin

### use Admin

ApplicationController.rb

```ruby
# add to check before redirect to page
before_action :authenticate_admin!
```

index.slim

- to call admin data

```slim
div = current_admin.email
div = "#{current_admin.firstname} #{current_admin.lastname}"
```

- to logout

```slim
div = link_to 'Logout', destroy_admin_session_path, method: :delete
```

note. ถ้าไม่ใส่ `method:` จะเป็น GET เสมอ

## Testing

### add bunde Testing

```
group :development, :test do
# For testing
gem 'rspec-rails', '~> 4.0.2'
# ...
```

### generate spec (testing)

    rails generate rspec:install

### create test for a model

    rails generate rspec:model [MODEL_NAME]

for model `admin` -> `rails g rspec:model admin`

### run Test

Run all spec files

    bundle exec rspec

### test File

admin_spec.rb

```ruby
# describe -> method name
describe "email_cannot_have_bob" do
    # context -> tell test by set up ...
    context "emails contains bob" do
        # it -> text display when error
        it "is invalid to have bob in email" do
            admin = Admin.new(email: 'bob@gmail.com', password: '111111')
            # check to error
            # if true -> pass, false -> error
            expect(admin.valid?).to eq(false)
        end
    end

    # re-check another way
    context "emails does not contain bob" do
        it "is valid not to have bob in email" do
            admin = Admin.new(email: 'beb@gmail.com', password: '111111')
            expect(admin.valid?).to eq(true)
        end
    end
end

```
