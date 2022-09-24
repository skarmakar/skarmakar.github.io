---
title: "Ubuntu wallpaper slideshow - using Ruby ;)"
date: 2022-09-23T00:00:00+00:00
author: Santanu Karmakar
layout: post
permalink: /ubuntu-wallpaper-slideshow/
categories: Ruby
tags: [ruby]
---

I am a long-time Ubuntu (currently 20.04 LTS) user. Recently, I got bored of the same desktop wallpaper being shown always and wanted to have a slideshow. Shotwell is a good option, but every time I need to download the images, select them and set them for slideshow. As a lazy (developer) person, did not like it.

Then I found [this blog post](https://ubuntuhandbook.org/index.php/2020/09/desktop-wallpaper-slideshow-ubuntu-20-04/) stating how to use XML to achieve this! Great!

I did create the xml manually, then thought about having fun automating the image replacement. Found [Unplash](https://unsplash.com/) providing awesome free images. So let's get started.

Create an XML file in the following path:

```

  sudo touch /usr/share/backgrounds/contest/focal.xml

```

and paste the following:

```xml

  <background>
    <starttime>
      <year>2020</year>
      <month>04</month>
      <day>01</day>
      <hour>00</hour>
      <minute>00</minute>
      <second>00</second>
    </starttime>
    <!-- This animation will start at midnight. -->
    <static>
      <duration>1795.0</duration>
      <file>/usr/share/backgrounds/1.jpg</file>
    </static>
    <transition>
      <duration>5.0</duration>
      <from>/usr/share/backgrounds/1.jpg</from>
      <to>/usr/share/backgrounds/2.jpg</to>
    </transition>
    <static>
      <duration>1795.0</duration>
      <file>/usr/share/backgrounds/2.jpg</file>
    </static>
    <transition>
      <duration>5.0</duration>
      <from>/usr/share/backgrounds/2.jpg</from>
      <to>/usr/share/backgrounds/3.jpg</to>
    </transition>
    <static>
      <duration>1795.0</duration>
      <file>/usr/share/backgrounds/3.jpg</file>
    </static>
    <transition>
      <duration>5.0</duration>
      <from>/usr/share/backgrounds/3.jpg</from>
      <to>/usr/share/backgrounds/4.jpg</to>
    </transition>
    <static>
      <duration>1795.0</duration>
      <file>/usr/share/backgrounds/4.jpg</file>
    </static>
    <transition>
      <duration>5.0</duration>
      <from>/usr/share/backgrounds/4.jpg</from>
      <to>/usr/share/backgrounds/5.jpg</to>
    </transition>
    <static>
      <duration>1795.0</duration>
      <file>/usr/share/backgrounds/5.jpg</file>
    </static>
    <transition>
      <duration>5.0</duration>
      <from>/usr/share/backgrounds/5.jpg</from>
      <to>/usr/share/backgrounds/6.jpg</to>
    </transition>
    <static>
      <duration>1795.0</duration>
      <file>/usr/share/backgrounds/6.jpg</file>
    </static>
    <transition>
      <duration>5.0</duration>
      <from>/usr/share/backgrounds/6.jpg</from>
      <to>/usr/share/backgrounds/7.jpg</to>
    </transition>
    <static>
      <duration>1795.0</duration>
      <file>/usr/share/backgrounds/6.jpg</file>
    </static>
    <transition>
      <duration>5.0</duration>
      <from>/usr/share/backgrounds/6.jpg</from>
      <to>/usr/share/backgrounds/7.jpg</to>
    </transition>
    <static>
      <duration>1795.0</duration>
      <file>/usr/share/backgrounds/7.jpg</file>
    </static>
    <transition>
      <duration>5.0</duration>
      <from>/usr/share/backgrounds/7.jpg</from>
      <to>/usr/share/backgrounds/8.jpg</to>
    </transition>
    <static>
      <duration>1795.0</duration>
      <file>/usr/share/backgrounds/8.jpg</file>
    </static>
    <transition>
      <duration>5.0</duration>
      <from>/usr/share/backgrounds/8.jpg</from>
      <to>/usr/share/backgrounds/9.jpg</to>
    </transition>
    <static>
      <duration>1795.0</duration>
      <file>/usr/share/backgrounds/9.jpg</file>
    </static>
    <transition>
      <duration>5.0</duration>
      <from>/usr/share/backgrounds/9.jpg</from>
      <to>/usr/share/backgrounds/10.jpg</to>
    </transition>
  </background>


```

Copy this ruby code in a file named `ubuntu_slideshow.rb`:

```ruby


  # Supporting https://unsplash.com urls only
  require 'nokogiri'
  require 'open-uri'
  require 'fileutils'

  class UbuntuSlideshow
    attr_accessor :url
    attr_reader :dir_path

    def initialize(url)
      @url = url
      @dir_path = '/tmp/tmp_slideshow'
    end

    def perform
      create_directory
      download
      move
    end

    private

    def download
      download_urls = Nokogiri::HTML(URI.open(url))
        .search('#app a')
        .select { |l| l.attributes['title']&.value&.match('Download') }
        .collect { |a| a.attributes['href'].value }
        .shuffle
      
      download_urls.each_with_index do |url, i|
        `wget -O #{dir_path}/#{i + 1}.jpg #{url}`
        break if i + 1 == 10
      end
    end

    # this will ask for sudo password
    def move
      `sudo mv #{dir_path}/*.jpg /usr/share/backgrounds/` 
    end

    def create_directory
      FileUtils.mkdir_p(dir_path) unless File.directory?(dir_path)
    end
  end

  # Usage from command line
  # ruby /path/to/ubuntu_slideshow.rb 'https://unsplash.com/t/nature'
  if ARGV.length.positive?
    UbuntuSlideshow.new(ARGV.first).perform 
  else
    puts "
      Please provide an unplash url ar argument, example:
      ruby /path/to/ubuntu_slideshow.rb 'https://unsplash.com/t/nature'
    "
  end

```

When you're bored with current slideshow images, just run this:

```ruby

  ruby /path/to/ubuntu_slideshow.rb 'https://unsplash.com/t/nature' # or some other unplash url

```

And enjoy new slideshow! Thanks Unsplash for the awesome free images!
