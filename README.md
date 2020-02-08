# RVM Cloud Native Buildpack

The RVM [Cloud Native Buildpack](https://buildpacks.io/) install rubies using
RVM. It is intented for use in combination with tools provided by the
[buildpacks.io](https://buildpacks.io/) project.

It also installs [Bundler](https://bundler.io/) and the
[Puma](https://puma.io/) gem. It then runs "bundle install" to install all Gems
required by the application.

## Detection

The RVM Cloud Native Buildpack expects a `Gemfile` to exist in the root
directory of the application.

## Build

### RVM

The default RVM version installed currently is: **1.9.29**.

If the same version of RVM was installed in a previous build run, it is
restored from the cache.

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

The RVM layer provided by installing the RVM version specified above and the
"Bundler" layer are cached.

The RVM layer is restored from cache if the installed version is equal to the
default version specified above.

In the Bundler layer, bundler installs the Gems specified in `Gemfile.lock`.
Between two build runs, a SHA256 hash is computed from the `Gemfile.lock`. If
there have been any changes to the `Gemfile.lock` file, the cached layer is
discarded and the Gems are installed from scratch by running `bundle install`.

## Launch

The RVM layer and the Bundler layer are available in the launch phase.

By default the following command is used for launching the application:

```shell
$ bundle exec rake db:create && bundle exec rake db:migrate && bundle exec rake assets:precompile && bundle exec rails server -b 0.0.0.0 -p 8080

...
```

### Procfile support

If a Procfile is present, **no launch command is provided** and it is expected
that a different CNB like the [Procfile CNB](https://github.com/cloudfoundry/procfile-cnb)
provides the launch command for the image.

## Authors

* [Khaled Blah](kblah@anynines.com), anynines GmbH

## License

This buildpack is released under version 2.0 of the [Apache License][a].

[a]: https://www.apache.org/licenses/LICENSE-2.0
