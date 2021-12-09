---
title: "Ruby on Rails - postgres table and index size in gb"
date: 2020-10-10T00:00:00+00:00
author: Santanu Karmakar
layout: post
permalink: /ruby-on-rails-postgres-table-and-index-size-in-gb/
categories: Ruby on Rails
tags: [ruby-on-rails, postgres, index size, table size]
---
Sometimes, before planning for optimization, we need to find the table size and index size from the database.
We can use a ruby script for fetching the information from `rails console`


{% highlight ruby %}

# change this to megabytes in case you want it in MB
unit = 1.0.gigabytes

{}.tap do |h|
  connection = ApplicationRecord.connection
  connection.tables.sort.each do |table_name|
    table_size = connection.execute("select pg_relation_size('#{table_name}')")[0].values[0] / unit
    index_size = connection.execute("select pg_indexes_size('#{table_name}')")[0].values[0] / unit
    
    h[table_name] = [table_size, index_size] 
  end
end.sort_by {|k,v| -1 * v[0]}

{% endhighlight %}

