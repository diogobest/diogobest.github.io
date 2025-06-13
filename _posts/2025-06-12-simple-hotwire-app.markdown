---
layout: post
title:  "Simple Hotwire App"
date:   2025-06-12 12:00:00 +0000
categories: hotwire, rails, tutorial
---
Tldr

The simplest configuration of hotwire is:

- In a model, add the `broadcasts_to -> (<model_name>) { <symbol_indentification> }`
- In your view, create a partial(good practice) with the content that should be updated / replaced for every event of created / update/ delete on your model

After that, if you open your console and create a new instance, you will have a functional connection.

The first step is to create the rails app with: `rails new hotwire-simple-test --database sqlite3`. I’ll be using sqlite and redis in this example. If you want to know about some configuration, see at: https://github.com/diogobest/hotwire-simple-example

With the project created, we are going to create a model:

{% highlight ruby %}
### rails g model Category name:string description:string
### Migration:
class CreateCategories < ActiveRecord::Migration[8.0]
  def change
    create_table :categories do |t|
      t.string :name
      t.string :description

      t.timestamps
    end
  end
end
{% endhighlight %}

In our model, we need to broadcast all events of create / update and delete of that model, with that, we will be able to see the changes in our view.

{% highlight ruby %}
class Category < ApplicationRecord
  broadcasts_to -> (category) { :categories}
end
{% endhighlight %}

Broadcasts_to creates a new stream called categories, with this, we need to subscribe to this channel to receive new updates:

{% highlight ruby %}
# app/views/categories/index.html.erb
<%= turbo_stream_from :categories %>
<div id="categories">
  <%= render @categories %>
</div>
{% endhighlight %}

I created a partial with the content that will be changed:

{% highlight ruby %}
<%= turbo_frame_tag dom_id(category) do %>
  <h1><%= category.name %></h1>
  <p> <%= category.description %></p>
<% end %>
{% endhighlight %}

There is everything you need to change to have a functional hotwire rails app working. Everytime you create // update // delete  a category, it will update in your view.

[Github project]: https://github.com/diogobest/hotwire-simple-example

---

