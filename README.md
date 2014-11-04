
Assignment
----------

1. Get your code from yesterday (Day-31) to use CarrierWave and Fog to store on
   S3

### How to do that?

Before you do anything:

```bash
brew install imagemagick
```

Add to your Gemfile:

```ruby
gem 'carrierwave'
gem 'fog'
gem 'dotenv-rails'
gem "mini_magick", "~> 4.0.0.rc"
gem 'rails_12factor'
```

Run the carrierwave install

```bash
rails g uploader Photo
```

Configure Fog (create the following file)

`config/initializers/carrierwave.rb`

```ruby
CarrierWave.configure do |config|

  config.storage = :fog
  config.fog_credentials = {
    :provider               => 'AWS',
    :aws_access_key_id      => ENV['AWS_KEY_ID'],
    :aws_secret_access_key  => ENV['AWS_SECRET'],
    :region                 => 'us-east-1'
  }
  config.fog_directory = ENV['AWS_BUCKET']
end

```

1. Create .env file with each ENV listed above
1. Sign up with Amazon S3.
1. Go to the S3 area, and create two buckets (note: these are globally unique. so you might need to get cray-cray)
  * APPNAME_DEV
  * APPNAME_LIVE
2. fill in with values from S3

```
AWS_KEY_ID=
AWS_SECRET=
AWS_BUCKET=
```

In your model, assuming it has a column "photo"

```ruby
class Sweet < ActiveRecord::Base

  ## use the PhotoUploader for our "photo" column
  mount_uploader :photo, PhotoUploader
end

```

In your uploader, make sure to:

1. Change storage to fog
2. use minimagick

```ruby
# encoding: utf-8

class PhotoUploader < CarrierWave::Uploader::Base

  # Include RMagick or MiniMagick support:
  # include CarrierWave::RMagick
  include CarrierWave::MiniMagick

  # Choose what kind of storage to use for this uploader:
  storage :fog
  # storage :fog

  # Override the directory where uploaded files will be stored.
  # This is a sensible default for uploaders that are meant to be mounted:
  def store_dir
    "uploads/#{model.class.to_s.underscore}/#{mounted_as}/#{model.id}"
  end

  # Provide a default URL as a default if there hasn't been a file uploaded:
  def default_url
    # For Rails 3.1+ asset pipeline compatibility:
    "candy.jpg"
    #{}"/images/fallback/" + [version_name, "default.png"].compact.join('_')
  end

  # Process files as they are uploaded:
  # process :scale => [200, 300]
  #
  # def scale(width, height)
  #   # do something
  # end

  # Create different versions of your uploaded files:
  version :thumb do
    process :resize_to_fill => [75, 75]
  end

  version :large do
    process :resize_to_fill => [640, 480]
  end

  # Add a white list of extensions which are allowed to be uploaded.
  # For images you might use something like this:
  # def extension_white_list
  #   %w(jpg jpeg gif png)
  # end

  # Override the filename of the uploaded files:
  # Avoid using model.id or version_name here, see uploader/store.rb for details.
  # def filename
  #   "something.jpg" if original_filename
  # end

end
```

Create heroku app:

```
heroku create
heroku config:set AWS_KEY_ID=xyz AWS_SECRET=xyz AWS_BUCKET=xyz
git push heroku master
heroku run rake db:migrate db:seed
```




Videos
--------

To watch in class, and refer to later:

* [How to be an Awesome Junior Developer (and why to hire them)](http://confreaks.com/videos/4659-nickelcityruby2014-how-to-be-an-awesome-junior-developer-and-why-to-hire-them)
* [Real Software Engineering](http://confreaks.com/videos/282-lsrc2010-real-software-engineering)
* [Confident Code](http://confreaks.com/videos/763-rubymidwest2011-confident-code)
* [Mastering the Ruby Debugger](http://confreaks.com/videos/760-rubymidwest2011-mastering-the-ruby-debugger)
