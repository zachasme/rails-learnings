# Updating ruby version

```sh
ruby-install ruby
```

Update version string in `.ruby_version`, `Dockerfile` & `Gemfile`.

Open a new terminal (to refresh chruby)

Reinstall gems
```sh
gem pristine --all
gem install rails
```

Rebundle

```sh
bundle
```