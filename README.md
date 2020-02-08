# RVM Cloud Native Buildpack

The RVM [Cloud Native Buildpack](https://buildpacks.io/) install rubies using
RVM. It is intented for use in combination with tools provided by the
[buildpacks.io](https://buildpacks.io/) project.

It also installs [Bundler](https://bundler.io/) and the
[Puma](https://puma.io/) gem. It then runs "bundle install" to install all Gems
required by the application. It re-uses the "Bundler layer" if there have not
been any changes between two build runs to speed up the creation of the final
image.

A container created using this CNB uses the following command for launching the
application:

```shell
$ bundle exec rake db:create && bundle exec rake db:migrate && bundle exec rake assets:precompile && bundle exec rails server -b 0.0.0.0 -p 8080
```

## Detection

The RVM Cloud Native Buildpack expects a `Gemfile` to exist in the root
directory of the application.

## Build

### RVM

The RVM CNB installs the latest RVM version on the "RVM layer".

### Ruby version

The default Ruby version installed currently is: **2.6.5**

During build the RVM CNB probes for a `.ruby-version` file and if it exists
it installs the Ruby version specified in it instead.

### Bundler version

The RVM CNB searches within the `Gemfile.lock` file (if it exists) for the line
`BUNDLED WITH` and if it exists the version of Bundler specified in the line
below will be installed. Otherwise, the latest version of bundler will be
installed.

## Caching

Currently, on the layer provided by "Bundler" is cached. In this layer, bundler
installs the Gems specified in `Gemfile.lock`. Between two build runs, a SHA256
hash is computed from the `Gemfile.lock`. If there have been any changes to the
`Gemfile.lock` file, the previous cached layer is discarded and the Gems are
installed from scratch.

## Authors

* [Khaled Blah](kblah@anynines.com), anynines GmbH

## License

This buildpack is released under version 2.0 of the [Apache License][a].

[a]: https://www.apache.org/licenses/LICENSE-2.0
