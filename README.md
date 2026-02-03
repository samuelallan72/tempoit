# Tempoit

Simple timewarrior to tempo/jira worklog uploader.

## About

This is a small tool I use for my work time logging workflow.
My work tracks time using Tempo (a Jira plugin).
I track time locally using timewarrior, using tags to mark logs for work that require logging to tempo.
This tool filters and parses the timewarrior logs, converts them to tempo-compatible worklogs,
uploads them, and marks them as uploaded in timewarrior by modifying the tags.

## Installation

Install with cargo:

```
cargo install --git https://github.com/samuelallan72/tempoit.git tempoit
```

or clone this repository build and run with cargo:

```
git clone https://github.com/samuelallan72/tempoit.git
cargo build
cargo run
```

You should have a recent stable rust toolchain installed.

On first run, a default `tempoit/tempoit.toml` configuration file will be created in your systems config directory - see [etcetera `AppStrategy` docs](https://docs.rs/etcetera/latest/etcetera/#appstrategy) for more information.

You can find the configuration file using the following paths, based on which system you're using.
* Linux: `$HOME/.config/tempoit/tempoit.toml`
* Windows: `{FOLDERID_RoamingAppData}/tempoit/tempoit.toml`
* macOS: `$HOME/Library/Application Support/rs.tempoit/tempoit.toml`

Configure to suit - example below:

```
# your jira credentials
username = 'my_username'
password = 'my_password'
# base url of the jira instance
base_url = 'https://tasks.opencraft.com'
# be careful with backslashes; use single quoted string to avoid needing double backslashes in the regex
ticket_regex = '^(?i:STAR|FAL|SE|BB|OC|MNG|BIZ|ADMIN)-\d+$'
```

## Usage

### Logging Work

In order to be able to upload your worklog, each timewarrior entry has to have:
- [Tags](src/timew.rs#L136):
  - `log`
  - `oc`

- Annotation

Therefore, the required format is:
```
timew start log oc <Jira ticket>
timew ann "Some annotation"

# or tags can be added later while timer running
timew start log oc
timew tag <Jira ticket>
```

Here's an example:
```
timew start log oc SE-3197

timew annotate "Setting up Tempoit to log work"
```

### Uploading Worklog

Run `tempoit` to upload your time logs to tempo/jira worklog. For example:

```
❯ tempoit
:: Ready to upload worklogs:
   @2    2020-08-01 0h 8m   [SE-1] 'test description'
   @1    2020-08-01 0h 5m   [SE-2] 'work on something else'
:: Total time: 0h 13m
:: Confirm upload [y/N]
```

It will print the worklogs it will upload, asking for confirmation.
On confirmation, it will upload the worklogs and mark them as logged locally in timewarrior.

> In case your timew entries don't show up when you run `tempoit`, then you would have probably have forgotten to enter one of the neceessary tags that need to be used with timewarrior.
>
> [See source code for the tags used with timewarrior in `src/timew.rs`.](src/timew.rs#L136)
>
> The source code can be changed if different tags are what you need or desire
>
> Pull requests are accepted and encouraged, especially if you would like to make tags configurable via the config file

## Dev

This is a standard cargo project, where you can use all the standard `cargo run|build|test|...` commands.
The api is also documented; generate the html docs with `cargo doc`.

Future work may include making the timewarrior tags, etc. that it uses configurable,
and supporting other time tracking tools (toggl, etc.).
The code is also structured in a way that makes it possible to use the timewarrior parsing tools and jira/tempo client in other projects, by importing this crate as a library.
For now, it works for me, so I probably won't do much more work on it apart from fixing bugs or keeping it up to date with my workflow.
If you're interested in using this tool/library and have requests, feel free to open an issue and/or a pull request! :)

## Contributing

Please feel free to open an issue or submit a pull request. :)

## License

Copyright © 2020-2025 Samuel Allan and contributors

    This program is free software: you can redistribute it and/or modify
    it under the terms of the GNU General Public License as published by
    the Free Software Foundation, version 3 of the License.

    This program is distributed in the hope that it will be useful,
    but WITHOUT ANY WARRANTY; without even the implied warranty of
    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
    GNU General Public License for more details.

    You should have received a copy of the GNU General Public License
    along with this program.  If not, see <https://www.gnu.org/licenses/>.
