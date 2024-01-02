# Updating ruby version

```sh
ruby-install ruby
```

Update version string in `.ruby_version`, `Dockerfile` & `Gemfile`.

Reinstall gems
```sh
gem pristine --all
gem install rails
```

Rebundle

```sh
bundle update
```