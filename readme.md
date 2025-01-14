<!--lint disable no-html first-heading-level maximum-line-length no-shell-dollars-->

<h1 align="center">
  <img width="400" src="https://raw.githubusercontent.com/get-alex/alex/c30ec12/media/logo.svg?sanitize=true" alt="alex">
  <br>
  <br>
</h1>

> 📝 **alex** — Catch insensitive, inconsiderate writing.

[![Build Status][travis-badge]][travis]
[![Coverage Status][codecov-badge]][codecov]
[![First timers friendly][first-timers-badge]][first-timers]

Whether your own or someone else’s writing, **alex** helps you find gender
favouring, polarising, race related, religion inconsiderate, or other
**unequal** phrasing in text.

For example, when `We’ve confirmed his identity` is given to **alex**,
it will warn you and suggest using `their` instead of `his`.

> Suggestions, feature requests, and issues are more than welcome!

Give **alex** a spin on the [Online demo »][demo].

## Why

*   [x] Helps to get better at considerate writing
*   [x] Catches many possible offences
*   [x] Suggests helpful alternatives
*   [x] Reads plain-text, HTML, and markdown as input
*   [x] Stylish

## Install

Using [npm][] (with [Node.js][node]):

```sh
$ npm install alex --global
```

Using [yarn][]:

```sh
$ yarn global add alex
```

## Table of Contents

*   [Command Line](#command-line)
*   [API](#api)
    *   [alex(value, config)](#alexvalue-config)
    *   [alex.markdown(value, config)](#alexmarkdownvalue-config)
    *   [alex.html(value, config)](#alexhtmlvalue-config)
    *   [alex.text(value, config)](#alextextvalue-config)
*   [Integrations](#integrations)
*   [Support](#support)
*   [Ignoring files](#ignoring-files)
*   [.alexignore](#alexignore)
    *   [Control](#control)
*   [Configuration](#configuration)
    *   [Ignoring messages](#ignoring-messages)
    *   [Configuring Profanities](#configuring-profanities)
*   [Workflow](#workflow)
*   [FAQ](#faq)
    *   [Why is this named alex?](#why-is-this-named-alex)
    *   [Alex didn’t check “X”!](#alex-didnt-check-x)
*   [Contributing](#contributing)
*   [License](#license)

## Command Line

![Example of how alex looks on screen][screenshot]

Let’s say `example.md` looks as follows:

```markdown
The boogeyman wrote all changes to the **master server**. Thus, the slaves
were read-only copies of master. But not to worry, he was a cripple.
```

Now, run **alex** on `example.md`:

```sh
$ alex example.md
```

Yields:

```txt
example.md
   1:5-1:14  warning  `boogeyman` may be insensitive, use `boogey` instead                       boogeyman-boogeywoman  retext-equality
  1:42-1:48  warning  `master` / `slaves` may be insensitive, use `primary` / `replica` instead  master-slave           retext-equality
  1:69-1:75  warning  Don’t use “slaves”, it’s profane                                           slaves                 retext-profanities
  2:52-2:54  warning  `he` may be insensitive, use `they`, `it` instead                          he-she                 retext-equality
  2:61-2:68  warning  `cripple` may be insensitive, use `person with a limp` instead             gimp                   retext-equality

⚠ 5 warnings
```

See `$ alex --help` for more information.

> When no input files are given to **alex**, it searches for files in the
> current directory, `doc`, and `docs`.
> If `--html` is given, it searches for `htm` and `html` extensions.
> Otherwise, it searches for `txt`, `text`, `md`, `mkd`, `mkdn`, `mkdown`,
> `ron`, and `markdown` extensions.

## API

[npm][]:

```sh
$ npm install alex --save
```

**alex** is also available as an AMD, CommonJS, and globals module,
[uncompressed and compressed][releases].

### `alex(value, config)`

### `alex.markdown(value, config)`

###### Example

```js
alex('We’ve confirmed his identity.').messages
```

Yields:

```js
[ { [1:17-1:20: `his` may be insensitive, when referring to a person, use `their`, `theirs`, `them` instead]
    message: '`his` may be insensitive, when referring to a person, use `their`, `theirs`, `them` instead',
    name: '1:17-1:20',
    reason: '`his` may be insensitive, when referring to a person, use `their`, `theirs`, `them` instead',
    line: 1,
    column: 17,
    location: { start: [Object], end: [Object] },
    source: 'retext-equality',
    ruleId: 'her-him',
    fatal: false } ]
```

###### Parameters

*   `value` ([`VFile`][vfile] or `string`) — Markdown or plain-text
*   `config` (`Object`, optional) — See [Configuration](#configuration) section below

###### Returns

[`VFile`][vfile].  You’ll probably be interested in its
[`messages`][vfile-message] property, as demonstrated in the example
above, as it holds the possible violations.

### `alex.html(value, config)`

Works just like [`alex()`][alex-api] and [`alex.text()`](#alextextvalue-config), but parses it as HTML.
It will break your writing out of its HTML-wrapped tags and examine them.

###### Example

```js
alex.html('<p class="black">He walked to class.</p>').messages
```

Yields:

```js
[ { [1:18-1:20: `He` may be insensitive, use `They`, `It` instead]
    message: '`He` may be insensitive, use `They`, `It` instead',
    name: '1:18-1:20',
    reason: '`He` may be insensitive, use `They`, `It` instead',
    line: 1,
    column: 18,
    location: { start: [Object], end: [Object] },
    source: 'retext-equality',
    ruleId: 'he-she',
    fatal: false } ]
```

###### Parameters

*   `value` ([`VFile`][vfile] or `string`) — HTML content
*   `config` (`Object`, optional) — See [Configuration](#configuration) section below

### `alex.text(value, config)`

Works just like [`alex()`][alex-api], but does not parse as markdown
(thus things like code are not ignored)

###### Example

```js
alex('The `boogeyman`.').messages // => []

alex.text('The `boogeyman`.').messages
```

Yields:

```js
[ { [1:6-1:15: `boogeyman` may be insensitive, use `boogey` instead]
    message: '`boogeyman` may be insensitive, use `boogey` instead',
    name: '1:6-1:15',
    reason: '`boogeyman` may be insensitive, use `boogey` instead',
    line: 1,
    column: 6,
    location: Position { start: [Object], end: [Object] },
    source: 'retext-equality',
    ruleId: 'boogeyman-boogeywoman',
    fatal: false } ]
```

###### Parameters

*   `value` ([`VFile`][vfile] or `string`) — Text content
*   `config` (`Object`, optional) — See [Configuration](#configuration) section below

## Integrations

*   Atom — [`get-alex/atom-linter-alex`](https://github.com/get-alex/atom-linter-alex)
*   Sublime — [`sindresorhus/SublimeLinter-contrib-alex`](https://github.com/sindresorhus/SublimeLinter-contrib-alex)
*   Visual Studio Code — [`shinnn/vscode-alex`](https://github.com/shinnn/vscode-alex)
*   Gulp — [`dustinspecker/gulp-alex`](https://github.com/dustinspecker/gulp-alex)
*   Slack — [`keoghpe/alex-slack`](https://github.com/keoghpe/alex-slack)
*   Ember — [`yohanmishkin/ember-cli-alex`](https://github.com/yohanmishkin/ember-cli-alex)
*   Probot — [`swinton/linter-alex`](https://github.com/swinton/linter-alex)
*   Vim — [`w0rp/ale`](https://github.com/w0rp/ale)
*   Browser Extension — [`skn0tt/alex-browser-extension`](https://github.com/skn0tt/alex-browser-extension)

## Support

**alex** checks for many patterns of English language, and generates warnings for:

*   Gendered work-titles, for example warning about `garbageman` and suggesting
    `garbage collector` instead
*   Gendered proverbs, such as warning about `like a man` and suggesting
    `bravely` instead, or suggesting `courteous` for `ladylike`.
*   Blunt phrases, such as warning about `cripple` and suggesting
    `person with a limp` instead
*   Intolerant phrasing, such as warning about using `master` and `slave`
    together, and suggesting `primary` and `replica` instead
*   Profanities, the least of which being `butt`

See [**retext-equality**][equality] and [**retext-profanities**][profanities]
for all checked rules.

**alex** ignores words meant literally, so `“he”`, `He — ...`, and [the
like][literals] are not warned about

## Ignoring files

**alex** CLI searches for files with a markdown or text extension when given
directories (e.g., `$ alex .` will find `readme.md` and `foo/bar/baz.txt`).
To prevent files from being found by **alex**, add an
[`.alexignore`][alexignore] file.

## `.alexignore`

The **alex** CLI will sometimes [search for files][ignoring-files].  To prevent
files from being found, add a file named `.alexignore` in one of the
directories above the current working directory.  The format of these files is
similar to [`.eslintignore`][eslintignore] (which is in turn similar to
`.gitignore` files).

For example, when working in `~/alpha/bravo/charlie`, the ignore file can be
in `charlie`, but also in `~`.

The ignore file for [this project itself][.alexignore]
looks as follows:

```txt
# `node_modules` is ignored by default.
example.md
```

### Control

Sometimes, **alex** makes mistakes:

```markdown
A message for this sentence will pop up.
```

Yields:

```txt
readme.md
  1:15-1:18  warning  `pop` may be insensitive, use `parent` instead  dad-mom  retext-equality

⚠ 1 warning
```

**alex** can silence message through HTML comments in markdown:

```markdown
<!--alex ignore dad-mom-->

A message for this sentence will **not** pop up.
```

Yields:

```txt
readme.md: no issues found
```

`ignore` turns off messages for the thing after the comment (in this
case, the paragraph).
It’s also possible to turn off messages after a comment by using
`disable`, and, turn those messages back on using `enable`:

```markdown
<!--alex disable dad-mom-->

A message for this sentence will **not** pop up.

A message for this sentence will also **not** pop up.

Yet another sentence where a message will **not** pop up.

<!--alex enable dad-mom-->

A message for this sentence will pop up.
```

Yields:

```txt
readme.md
  9:15-9:18  warning  `pop` may be insensitive, use `parent` instead  dad-mom  retext-equality

⚠ 1 warning
```

Multiple messages can be controlled in one go:

```md
<!--alex disable he-her his-hers dad-mom-->
```

...and all messages can be controlled by omitting all rule identifiers:

```md
<!--alex ignore-->
```

## Configuration

### Ignoring messages

**alex** can silence messages through `.alexrc` configuration:

```json
{
  "allow": ["boogeyman-boogeywoman"]
}
```

...or the `alex` field in `package.json`:

```txt
{
  ...
  "alex": {
    "allow": ["butt"]
  },
  ...
}
```

The `allow` field is expected to be an array of rule identifier strings.

All `allow` fields in all `package.json` and `.alexrc` files are
detected and used when processing.

Next to `allow`, `noBinary` can also be passed.  Setting it to true
counts `he and she`, `garbageman or garbagewoman` and similar pairs
as errors, whereas the default (`false`), treats it as OK.

### Configuring Profanities

The profanity checker in **alex** can be configured to define the level of
“sureness” to warn for.  The underlying library uses
[cuss](https://github.com/words/cuss) which has a dictionary of words that have
a rating between 0 and 2 of how “sure” it is a profanity.  Here is the table from
[the cuss documentation](https://github.com/words/cuss/blob/master/readme.md#cuss)

| Rating | Use as a profanity | Use in clean text | Example |
| ------ | ------------------ | ----------------- | ------- |
| 2      | likely             | unlikely          | asshat  |
| 1      | maybe              | maybe             | addict  |
| 0      | unlikely           | likely            | beaver  |

You can define what level of profanity you want **alex** to warn for in the
`.alexrc` configuration:

```json
{
  "profanitySureness": 1
}
```

...or the `alex` field in `package.json`:

```txt
{
  ...
  "alex": {
    "profanitySureness": 1
  },
  ...
}
```

The `profanitySureness` is a number that _includes_ the level of profanity you
want to check for.  For example, if you set it to 1 then it will warn for level 1
and 2 profanities, but not for level 0 (unlikely).

## Workflow

The recommended workflow is to add **alex** to `package.json`
and to run it with your tests in Travis.

You can opt to ignore warnings through [alexrc](#configuration) files and
[control comments][control].  For example, with a `package.json`.

A `package.json` file with [npm scripts][npm-scripts],
and additionally using [AVA][] for unit tests, could look
as follows:

```json
{
  "scripts": {
    "test-api": "ava",
    "test-doc": "alex",
    "test": "npm run test-api && npm run test-doc"
  },
  "devDependencies": {
    "alex": "^1.0.0",
    "ava": "^0.1.0"
  }
}
```

Alternatively, if you’re using Travis to test, set up something
like the following in your `.travis.yml`:

```diff
 script:
 - npm test
+- alex --diff
```

Make sure to still install alex though!

If the `--diff` flag is used, and Travis is detected, unchanged
lines are ignored.  Using this workflow, you can merge PRs
with warnings, and not be bothered by them afterwards.

## FAQ

<!--lint disable no-heading-punctuation-->

### Why is this named alex?

It’s a nice androgynous/unisex name, it was free on npm, I like it!  :smile:

### Alex didn’t check “X”!

See
[`contributing.md`][contributing] on how to get “X” checked by alex.

<!--lint enable no-heading-punctuation-->

## Contributing

**alex** is built by people just like you!  Check out
[`contributing.md`][contributing] for ways to get started.

This project has a [Code of Conduct][coc].  By interacting with this repository
or community you agree to abide by its terms.

## License

[MIT][license] © [Titus Wormer][author]

<!-- Definitions. -->

[first-timers-badge]: https://img.shields.io/badge/first--timers--only-friendly-blue.svg

[first-timers]: https://www.firsttimersonly.com/

[travis-badge]: https://img.shields.io/travis/get-alex/alex.svg

[travis]: https://travis-ci.org/get-alex/alex

[codecov-badge]: https://img.shields.io/codecov/c/github/get-alex/alex.svg

[codecov]: https://codecov.io/github/get-alex/alex

[demo]: http://alexjs.com/#demo

[npm]: https://docs.npmjs.com/cli/install

[node]: https://nodejs.org/en/download/

[screenshot]: screenshot.png

[releases]: https://github.com/get-alex/alex/releases

[vfile]: https://github.com/vfile/vfile

[vfile-message]: https://github.com/vfile/vfile#vfilemessages

[alex-api]: #alexvalue-config

[literals]: https://github.com/syntax-tree/nlcst-is-literal#isliteralparent-index

[alexignore]: #alexignore

[control]: #control

[ignoring-files]: #ignoring-files

[eslintignore]: http://eslint.org/docs/user-guide/configuring.html#ignoring-files-and-directories

[.alexignore]: https://github.com/get-alex/alex/blob/master/.alexignore

[npm-scripts]: https://docs.npmjs.com/misc/scripts

[ava]: http://ava.li

[contributing]: contributing.md

[coc]: code-of-conduct.md

[license]: LICENSE

[author]: http://wooorm.com

[profanities]: https://github.com/retextjs/retext-profanities/blob/master/rules.md

[equality]: https://github.com/retextjs/retext-equality/blob/master/rules.md

[yarn]: https://yarnpkg.com/
