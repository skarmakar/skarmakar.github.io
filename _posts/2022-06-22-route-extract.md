---
title: "Fun - Rails extract routes in a CSV"
date: 2022-06-22T00:00:00+00:00
author: Santanu Karmakar
layout: post
permalink: /fun-rails-extract-routes-in-a-csv/
categories: Ruby on Rails
tags: [ruby-on-rails, routes]
---

When we start looking at a Ruby on Rails project, often we do consider routes as the entry-point. But, often by looking directly at routes, it's hard to understand how many endpoints the application is handling. The below simple code snippet generates a CSV file combining all the HTTP verbs.  

```ruby

    # Copy this code, and create a route_extract.rb file
    # Open rails to console
    # load the file: load 'path/to/route_extract.rb'
    # RouteExtract.generate_csv('project_name')

    require 'tempfile'
    require 'csv'

    class RouteExtract
      HTTP_VERBS = %w[GET POST PUT PATCH DELETE].freeze

      def self.generate_csv(project_name = '')
        t = Tempfile.new
        system "rails routes > #{t.path}"
        paths = File.read(t.path).split("\n")
        File.delete(t.path)

        hash = {}
        HTTP_VERBS.each do |verb|
          hash[verb] ||= []

          paths.each do |path|
            next if path.exclude?(verb)

            formatted_path = path.split(verb).last.split('(').first.squish
            # merge PATCH and PUT into single, Rails by default works for both
            next if verb == 'PATCH' && hash[verb].include?(formatted_path)

            hash[verb] << formatted_path
          end

          hash.delete(verb) if hash[verb].blank?
        end

        CSV.open([project_name, 'routes.csv'].select(&:present?).join('_'), 'w') do |csv|
          hash.each do |verb, values|
            csv << [verb]
            values.each { |val| csv << [val] }
            csv << ['']
          end

          puts "Check #{csv.path} for the extracted routes"
        end
      end
    end

```

That's It! As it was quite helpful for me, thought about sharing :) 
