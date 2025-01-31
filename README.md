# JUnit Annotate Buildkite Plugin [![Checks](https://github.com/juandiegopalomino/junit-annotate-local-buildkite-plugin/actions/workflows/ci.yaml/badge.svg)](https://github.com/juandiegopalomino/junit-annotate-local-buildkite-plugin/actions/workflows/ci.yaml)

A [Buildkite plugin](https://buildkite.com/docs/agent/v3/plugins) that parses junit.xml from local directories and creates a [build annotation](https://buildkite.com/docs/agent/v3/cli-annotate) listing the individual tests that failed.

## Example

The following pipeline will run `test.sh` jobs in parallel, and then process all the resulting JUnit XML files to create a summary build annotation.

```yml
steps:
  - command: test.sh
    parallelism: 50
    plugins:
      - junit-annotate#v2.0.2:
          directory: tmp
```

## Configuration

### `directory` (required)

The local directory holding the JUnit XML files.

Example: `tmp/junit-*.xml`

### `always-annotate` (optional, boolean)

Forces the creation of the annotation even when no failures or errors are found

### `job-uuid-file-pattern` (optional)
Default: `-(.*).xml`

The regular expression (with capture group) that matches the job UUID in the junit file names. This is used to create the job links in the annotation.

To use this, configure your test reporter to embed the `$BUILDKITE_JOB_ID` environment variable into your junit file names. For example `"junit-buildkite-job-$BUILDKITE_JOB_ID.xml"`.

### `failure-format` (optional)
Default: `classname`

This setting controls the format of your failed test in the main annotation summary.

There are two options for this:
* `classname`
  * displays: `MyClass::UnderTest text of the failed expectation in path.to.my_class.under_test`
* `file`
  * displays: `MyClass::UnderTest text of the failed expectation in path/to/my_class/under_test.file_ext`

### `fail-build-on-error` (optional)  
Default: `false`

If this setting is true and any errors are found in the JUnit XML files during
parsing, the annotation step will exit with a non-zero value, which should cause 
the build to fail.

### `context` (optional)
Default: `junit`

The buildkite annotation context to use. Useful to differentiate multiple runs of this plugin in a single pipeline.

### `report-slowest` (optional)
Default: `0`

Include the specified number of slowest tests in the annotation. The annotation will always be shown.

### `verbose` (optional)
Default: `false`

Run in verbose mode.

## Developing

To test the plugin hooks (in Bash) and the junit parser (in Ruby):

```bash
docker-compose run --rm plugin &&
docker-compose run --rm ruby
```

To test the Ruby parser locally:

```bash
cd ruby
rake
```

To test your plugin in your builds prior to opening a pull request, you can refer to your fork and SHA from a branch in your `pipeline.yml`.

```
steps:
  - label: Annotate
    plugins:
      - YourGithubHandle/junit-annotate#v2.0.2:
          ...
```

## License

MIT (see [LICENSE](LICENSE))
