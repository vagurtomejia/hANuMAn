



rbenv install 2.4.0
rbenv global 2.4.0
gem install rails -v 5.2.0
rbenv rehash
rails -v




Every Rails project has the version of Rails in the Gemfile that it will use separately from every other app. There's nothing you need to do to manage two different Rails versions.

The only thing you'll want to do is
`gem install rails`
to get the latest version and create your new project with
`rails new myapp`.
That will make sure the new project starts with Rails 5.1 (or whatever is the latest at the time).

You can also create apps with older versions of rails using
`rails _4.2.0_ new myapp`

Rbenv and Rvm have pretty much nothing to do with this because they are for Ruby versions, not Rails. All of this is taken care of by Bundler.



source: https://gorails.com/forum/running-multiple-rails-versions





once you get rbenv installed, and you use it to install a specific ruby version, you can install multiple versions of rails to for that ruby.

STEP 1. Install whatever version(s) of rails you want per ruby version

% RBENV_VERSION=1.9.2-p290 rbenv exec gem install rails --version 3.0.11

By using the "RBENV_VERSION=1.9.2-p290" prefix in your command line, you're specifying which ruby rbenv should be concerned with.

Then following that with the "rbenv exec" command, you can install rails. Just use the version flag as in the example to specify which version you want. Not sure if you can install multiple versions in one shot, but I just run this command as many times as needed to install each version I want.

Note: This will all be managed within your rbenv directory, so it's perfectly safe and contained.

STEP 2. Build a new rails project by specifying the rails version you want.

% RBENV_VERSION=1.9.2-p290 rbenv exec rails _3.0.11_ new my_project


STEP 3. Don't forget to go into that project and set the local rbenv ruby version.

% cd my_project
% rbenv local 1.9.2-p290

Now if you want to delete this project, just delete it as normal.

If you want to delete / manage a rails version from rbenv gems, you can use regular gem commands, just prefix your command line with:

% RBENV_VERSION=1.9.2-p290 rbenv exec gem {some command}

And of course, you can delete a complete ruby version and all its shims, etc that are managed within rbenv pretty easily. I like how self contained everything is.

https://stackoverflow.com/questions/8877772/how-do-you-use-multiple-rails-versions-with-rbenv/9659619#9659619



