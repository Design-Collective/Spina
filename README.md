![Spina CMS](http://www.spinacms.com/spinacms.png)

[Visit the website](http://www.spinacms.com)

[![Codeship Status for denkGroot/Spina](https://img.shields.io/codeship/e13debf0-e6af-0132-8abf-32d84f3372de.svg)](https://codeship.com/projects/82322)
[![Code Climate](https://codeclimate.com/github/denkGroot/Spina/badges/gpa.svg)](https://codeclimate.com/github/denkGroot/Spina)
[![Test Coverage](https://codeclimate.com/github/denkGroot/Spina/badges/coverage.svg)](https://codeclimate.com/github/denkGroot/Spina/coverage)
[![Slack](https://slack-spinacms.herokuapp.com/badge.svg)](https://slack-spinacms.herokuapp.com)

# Getting Started

Spina is a CMS built upon the Rails framework. This guide is designed for developers with experience using Ruby on Rails.

To start using Spina CMS add the following line to your Gemfile:

```ruby
gem 'spina'
```

Make sure you run the installer to get started.

    rails g spina:install

The installer will help you setup your first user.

Then start `rails s` and access your admin panel at `/admin`.

## Upgrading from 0.10 to 0.11

The spina-template gem is merged into the spina gem. You don't have to use the original spina-template gem anymore.

## Upgrading from 0.9 to 0.10

When upgrading to Spina 0.10 it's essential to update spina-template to version 0.4 or higher. Otherwise layout issues will occur.

## Upgrading from 0.8 to 0.9

Theme configuration changed to:

```ruby
# config/initializers/themes/default.rb
Spina::Theme.register do |theme|
# Theme config
end
```

And theme sections, structures, layouts, view_layouts and layout_parts
has been normalised.

Check out [config/initializers/themes/demo.rb](https://github.com/denkGroot/Spina/blob/master/lib/generators/spina/templates/config/initializers/themes/demo.rb) for an example.

Add new migrations `rake spina:install:migrations` and `rake db:migrate`

## Upgrading from 0.7 to 0.8

Spina-specific configuration moved from `Spina::Engine.config` to just `Spina.config`.
Change the following in your initializer:

```ruby
# config/initializers/spina.rb

Spina::Engine.configure do |config| # OLD
Spina.configure do |config| # NEW
```

# Basics

The installer generates a few initializers that contain necessary configuration for Spina.

In the initializers folder there's a new folder named `themes`. Inside you will find a configuration file named `default.rb`. This file contains all of your theme-specific settings. You can define multiple Page parts, Layout parts, View templates and Custom pages.

## Page parts

A page in Spina has many Page parts. By default these page parts can be one of the following:

- `Spina::Line`
- `Spina::Text`
- `Spina::Photo`
- `Spina::PhotoCollection`
- `Spina::Structure`

These are the building blocks of your view templates. You can have an unlimited number of page parts in a page. We prefer to keep the number of parts to a minimum so that managing your pages won't become too complex.

Spina uses an initializer to create the basic building blocks of your page. There are three steps to add a new building block or page part to your app:

1. Set up a new page part in the initializer
2. Set the new initializer into a view template
3. Add it to the view

#### Create a new page part
When you install Spina, you will see the following in `config/initializers/themes/default.rb`

```ruby
::Spina::Theme.register do |theme|

  theme.name = 'default'
  theme.title = 'Default Theme'

  theme.page_parts = [{
    name:             'content',
    title:            'Content',
    partable_type:    'Spina::Text'
  }]

  theme.view_templates = [{
    name:       'homepage',
    title:      'Homepage',
    page_parts: ['content']
  }, {
    name: 'show',
    title:        'Default',
    description:  'A simple page',
    usage:        'Use for your content',
    page_parts:   ['content']
  }]

  theme.custom_pages = [{
    name:           'homepage',
    title:          'Homepage',
    deletable:      false,
    view_template:  'homepage'
  }]

end
```

Right now, the default theme is applying a title to the page, with a simple text div below it. Go to `/admin` on your app and have a look. Edit the textbox and go to preview the page.

Spina represents each building block of your page, called a 'page part,' as a hash inside the `page_parts` array. If we look at the default setup we can see there is one hash inside the array representing the one textbox we see on our page.

Let's say I wanted to add another text box below this called `portfolio`. First I would add another hash to the `self.page_parts` array like so:

```ruby
theme.page_parts = [{
  name:             'content',
  title:            'Content',
  partable_type:    'Spina::Text'
}, {
  name:             'portfolio', # added this hash
  title:            'Portfolio',
  partable_type:    'Spina::Text'
}]
```

#### Add it to the view template

Now, we need to update the `self.view_templates` hash next. These view templates provide customization for the different views you might want. For example, you may have a 'blog' view or an 'about' view which add different page parts. For this example we will add the portfolio part into the 'Default' view template.

```ruby
theme.view_templates = [{
  name:       'homepage',
  title:      'Homepage',
  page_parts: ['content']
}, {
  name:         'show',
  title:        'Default',
  description:  'A simple page',
  usage:        'Use for your content',
  page_parts:   ['content', 'portfolio'] # added 'portfolio'
}]
```

#### Add it to the view

Finally, let's go to `views/default/pages/show.html.erb` and add the following:

```ruby
<h1><%= @page.title %></h1>

<%= @page.content(:text).try(:html_safe) %>
<%= @page.content(:portfolio).try(:html_safe) %> # added this line
```

We have successfully added another textbox! Restart your server and load up the admin section again. You should see another text box below the content box.

## Layout parts

Sometimes you need editable content that's not specific to a view template but to your theme as a whole. You can use the following parts in your layout.

- `Spina::Line`

## View templates

Each theme typically has a few different view templates which make up your website. By default Spina generates a *homepage* and *show* template.

The views for these templates are stored in `app/views/default/pages`.

## Navigations

Usually managing a single list of pages is enough for most use cases. Sometimes however, you need a little more flexibility. This is where navigations come in. You can create multiple navigations which are basically different collections of your pages. You can choose to include all or just a few of your pages. You can also edit the order of pages per navigation.

You define navigations in your theme's config file:

```ruby
::Spina::Theme.register do |theme|
  # ...

  theme.navigations = [{
    name: 'main',
    label: 'Main navigation',
    auto_add_pages: true
  }, {
    name: 'mobile',
    label: 'Mobile'  
  }]

  # ...
end
```

`auto_add_pages` ensures that each page that you create automatically gets added to this navigation.

Besides navigations there's always a single overview of all pages. Your sitemap and friendly URLs are generated based on this overview.

Creating navigations is optional.

## Custom pages

You can define custom pages for your theme that will be generated when bootstrapping your website. You can define whether or not they're deletable. By default Spina creates a custom page named Homepage which is not deletable.

# Contributing

Check our [Contributing Guide](CONTRIBUTING.md) for instructions on how to help the project.

# License

Spina is released under the [MIT license](LICENSE.md).

# Credits

Some parts of Spina are heavily influenced by the wonderful Refinery CMS. Credits to [the Refinery  team](http://www.refinerycms.com/about).

All icons in Spina were made by Brent Jackson [Geomicons](http://jxnblk.com/geomicons-wired/).
