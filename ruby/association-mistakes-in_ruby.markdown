---
title: 5 Relationship Assignment Mistakes in Ruby
date: 2014-05-28
tags:
author: Patrick Lowry 
- relationships
- assignments
---

# 5 Common Relationship Assignment Mistakes in Ruby

1. Plan your Associations
2. Don't Forget Your belongs_to Foreign Key!
3. Singular or Plural Naming Convention 
4. Adding and Dropping Table Column Commands
5. `accepts_nested_attributes` for Your model

## Plan your Associations
Before Starting a new Ruby on Rails project it is important to write down exactly what you want to accomplish. Write down your initial table structure on a piece of paper to help you visualize the future outcome of your project.

## Don't Forget the belongs_to Foreign Key!
While it's not always completely necessary, it is definitely proper convention to assign your `belongs_to` associations with the appropriate foreign keys to connect your tables. With your associations properly set using foreign keys, you can call between the tables correctly. 

### Example
Let's say you have you have two tables, Client and Photo. Each `Client` `has_many` `photos` and `Photos` `belongs_to` `Client`. The Client table should contain the column `photo_id` to connect the two tables. 

## Singular or Plural Naming Convention
At the beginning of your ruby career you can definitely find yourself wondering why associations are not working or why there is a big red error on your computer screen. Many times this can result from naming your tables, models, or associations improperly. Models are always singular, tables are always plural, `belongs_to` associations are always singular, and `has_many` are always plural.

### Example 
As seen below, the Photo model has a photos table. It `belongs_to` `:user` and `has_many` `:posts`. The correct example below is the standard Rails convention. 

#### Incorrect         
    class Photos < ActiveRecord::Base
      belongs_to :user
      has_many :post
    end

#### Correct!
    class Photo < ActiveRecord::Base
      belongs_to :user
      has_many :posts
    end 

Example 1: `rails g migration Photo`

Example 2: `rails g controller Photos`

Example 3: `rails g migration photos name:string description:string`

### Side Note
When creating names for your columns, be careful not to name them something that could potentially conflict with `ActiveRecord::Base`

If you name a column the same as a standard ruby method in ActiveRecord::Base, it will override the built in method and things will start to break :) 

## Adding and Dropping Table Column Commands
When creating tables and forming relationships between your models, you will often need to add and remove columns from your database. If you are just starting, this task could be overwhelming! Don't worry though, there are a few easy terminal commands that will get you back on track. 

### Example 1 
Say you forgot to add the column `title` to the photos table in your database, the command would look like this. 

1. In your terminal run

    rails g migration AddTitleToPhotos title:string

2. Your migration file in the db/migrate folder would then look like this:

    class AddTitleToPhotos < ActiveRecord::Migration
      def change
        add_column :photos, :date_time, :string
      end
    end

3. Run `rake db:migrate` in your terminal and the updated column title will now be in the database

### Example 2 
Let's say you added a title column to your Photo table and realized you don't need it. Instead of kicking yourself for being stupid, just drop the column, it's that easy. 

1. In your terminal run

        rails g migration RemoveTitleFromPhotos title:string

2. Ensure your migration file is correct

        class DropTitleToPhotos < ActiveRecord::Migration
          def change
            remove_column :photos, :date_time, :string
          end
        end

3. Run `rake db:migrate` in your terminal and the unwanted column will be removed

# Nest Forms within Models
Are you trying to nest your forms and getting that terrible "Uninitialized Constant Error" on your computer screen? This could be from not using the `accepts_nested_attributes_for` method inside your model.

### Example 
Let's look at two Models, Client and Photo. Each `Client` `has_many` `photos` and `Photos` `belongs_to` `Client`. The relationships are correct, but you want to have a form where the new client fills out their name, address, and adds a photo avatar. This form would fail because ActiveRecord doesn't know the attributes inside of the Photo table. To remedy this, you must place `accepts_nested_attributes_for :photos` inside the Client model.

#### Will not accept nested forms:
    class Client < ActiveRecord::Base
      has_many :photos
    end

    class Photo < ActiveRecord::Base
      belongs_to :client
    end
  
#### Will accept nested forms:
    class Client < ActiveRecord::Base
      has_many :photos
      accepts_nested_attributes_for :photos
    end

    class Photo < ActiveRecord::Base
      belongs_to :client
    end