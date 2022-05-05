# Contributing

Issues and PRs are always welcome!

## Code coverage

[HGInsights](https://github.com/HGInsights) shoots for 100% coverage (via
[`excoveralls`](https://github.com/parroty/excoveralls)), but we always allow
ignoring lines or blocks of code that would either be impractical to test
or would consume too much of your time to do so (your time is valuable :).
In those cases, we strongly prefer using comment-style ignores, like so:

```elixir
# To get coverage on this block, we'd have to brutally kill the HTTP
# server, which we need to be running for other tests to pass
# coveralls-ignore-start
def handle_event(%ServerDisconnect{}, state) do
  {:stop, {:shutdown, :disconnected}, state}
end
# coveralls-ignore-stop
```

With a helpful comment explaining why this case is too tough to test.

### Please do not use coveralls' skip_files feature

Coveralls allows ignoring by adding file patterns to the `coveralls.json` file:

```json
{
  "coverage_options": {
    "minimum_coverage": 100,
    "treat_no_relevant_lines_as_covered" : true
  },
  "skip_files": [
    "^test",
    "^deps",
    "^lib/my_app/my_file.ex"
  ]
}
```

But in our experience, this only leads to confusion over why a file is not
being reported in the coverage detail. If an entire module needs to be ignored,
we prefer wrapping the entire `defmodule/2` call in ignore comments.

### Why we shoot for 100% coverage

Shooting for 100% code coverage is a bit controversial. Proponents sometimes
claim: "if the line isn't covered why do you need it?" Critics often accuse
the practice of being overly perfectionist and typically impractical, forcing
developers to write tests simply for the sake of coverage instead of
meaningfulness. Certainly there is a balance between the time spent writing a
test and the time saved by that test catching a regression.

HGInsights tries to take a practical and center-line approach. Truly,
**we do not require 100% lines covered**. Through usage of coveralls-ignore
comments, we artificially inflate our numbers to 100% so a change can be
immediately and naively validated as "not having changed coverage." If we
required a lower percentage, say 80%, adding new features or otherwise code
could change whether or not a project meets the requirement, even assuming
all _new_ code is merged at 100% coverage, simply because the denominator of
`covered_lines / total_lines` changes.


## Formatting

We use the Elixir formatter introduced in Elixir 1.6 in check-mode as a part
of CI. Some editors have supported formatting before _the_ formatter was
introduced, and ignore configuration or differ in their methods of formatting.
In particular, we set our line-length option to 80 columns and make sparing
use of the `locals_without_parens` option, which some formatting extensions
ignore. A simple run of `mix format` on the command line before committing
is always wise.

## Linting

We use two tools for linting: `mix compile --warnings-as-errors` and
[`credo`](https://github.com/rrrene/credo). Either of these checks failing to
pass will fail the CI, even for test files.

### Why so stringent about test files?

It may seem overzealous to care so much about the linting and formatting of
test files. They're just tests, right?

We believe that more often than not, the testing suite of a library or service
most clearly explains how that library or service works. Test suites are
also integration-points between developers: a well-written suite attracts
a community of fast-moving feature implementations and quick maintenance.
Therefore we treat the readability and consistency of tests with paramount
concern.


## Bless

To pull all of these checks together for development, we the
[`bless`](https://github.com/NFIBrokerage/bless) library which runs all commands
in serial and fails if any check fails. A successful run of `mix bless`
before committing usually translates into a green check mark from the CI.
If you cannot get a good bless run, submit the PR anyways! We'll be happy to
help you get that green check mark.
