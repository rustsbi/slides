# Contributing to RustSBI

-----------------------------------------------------

## Code of Conduct

Set community standards, with contributions welcome.

## Commit Message Guidelines

A good *commit* should solve only one problem, making the function complete, even if there is only one line of code. And a good *commit message* is a benchmark for the project, it's a specification for future contributions to the project from other contributors. They will follow your *commit message* to write their contributions, from the formatting of the title to the level of detail in the content. That's why it's so important to write a good *commit message*. Follow the project's style guide to ensure consistency in *commit messages*, and make the title of commit message clear and concise accurately reflecting the content of the change.

Note that the RustSBI community uses *commit messages* written entirely in **English**. Also, all the `rustsbi/rustsbi` modules have their own CHANGELOG files, and we use the *commit messages* to generate the `CHANGELOG.md` document. We check for changes to any file in this module's folder via *CI*, **must be accompanied by a CHANGELOG entry for the corresponding `CHANGELOG.md`**. Or the check fails, which may result in your contribution not appearing in the CHANGELOG of the next release.

Second, each commit needs signoff, and each *PR* or *push* is checked by the *CI* to see if the updated *commit* adds the `Signed-off-by` line.

### Commit Message Format

After the title, a detailed description of the change should be provided. If you are a new contributor, here is a recommendation to organize your *commit message* along the lines of what, why, and how.

- **What**: You can start by describing the background of the change, what kind of problem you are trying to solve.
- **Why**: Explain the impact of the change and compare it with the previous one, such as what bugs are fixed, what features are added, or what performance improvements are made.
- **How**: Describe the specific changes you make.

At this point, if you have clearly organized your *commit message* or have a general idea of what you should to do, congrats on moving on to the format section below.

A *commit message* must contain a header and footer, and the body is optional. If you are a contributor submitting a *commit* directly to the project, then the body is required:

```
<header>

[optional <body>]

<footer>
```

> Any line of the commit message cannot be longer 100 characters!  
> This allows the message to be easier to read on GitHub as well as in various Git tools.

#### header

As the first line of the commit message, `<header>` has a strict format requirement, usually:

```
<scope or type>: <subject>
```

You can also use both `<type>` and `<scope>` if you are used to **conventional commits**:

```
<type>(<scope>): <suject> 
```

> `<scope or type>/<type>(<scope>)` followed by a `!` , or include `BREAKING CHANGE:` in the `<footer>` to alert to a **BREAKING CHANGE**. A BREAKING CHANGE can be part of a commit of any `<type>`.

#### scope

The `<scope>` is the name of the submodule which is mainly changed. And changes to multiple submodules and submodules with renames can take the upper level module as the prefix to `<header>`. The `<scope>` is used when a commit covers more than one `<type>` or is not suitable to be defined by one `<type>`.

Examples:

- **`rt`**: `sbi-rt/src/legacy.rs` changed ([cfbb772](https://github.com/rustsbi/rustsbi/commit/cfbb7724d0b1a9ab37d2d18177db778aef5f04a3))
- **`spec`**: `sbi-spec/CHANGELOG.md` and `sbi-spec/Cargo.toml` changed ([0837da8](https://github.com/rustsbi/rustsbi/commit/0837da8fc325fca23c5ac7423f087e1d4d54783e))
- **`binary`**: `sbi-spec/src/binary.rs` file changed ([f87e9fc](https://github.com/rustsbi/rustsbi/commit/f87e9fc2cdb5575d49146fd3b5b25dc0d279236c))
- **`lib`**: `src/cppc.rs`、`src/hsm.rs`、`src/rfence.rs` files changed ([c7ce73b](https://github.com/rustsbi/rustsbi/commit/c7ce73bb84eef7cf3aa1683b1d15094eb081ed73))
- **`tests`**: `tests/build-full.rs`、`tests/build-rename.rs`、`tests/build-skip.rs` files changed ([2753980](https://github.com/rustsbi/rustsbi/commit/2753980672348c745ac84a074695b96363d22d71))
- **`deps`**: the `[dependencies]` in `Cargo.toml`、`sbi-testing/Cargo.toml` changed ([44b6aea](https://github.com/rustsbi/rustsbi/commit/44b6aea92cb807397108629e852354683e9b2943))
- **`crate`**: `Cargo.toml` changed ([a3b2fc4](https://github.com/rustsbi/rustsbi/commit/a3b2fc49bea59b12a77f912f304126d887351fa4))
- **`readme`**: `README.md` changed ([84a2876](https://github.com/rustsbi/rustsbi/commit/84a287617a23612e17278396b5e8505cb0b21465))

#### type

As a supplement to `<scope>`, follows the **conventional commits**, which must be one of the following along with examples:

- **`feat`**: add a new feature ([5baa946](https://github.com/rustsbi/rustsbi/commit/5baa946c4036bccba760be36c049ad1626b8d5e0))
- **`fix`**: fix a bug ([293db69](https://github.com/rustsbi/rustsbi/commit/293db697b39daf69eae1315cb85996e134d0d0b7))
- **`build`**: changes to the manifest, such as dependencies, target and crate versions ([3b5daf8](https://github.com/rustsbi/rustsbi-d1/commit/3b5daf8e81f6eab4f2c00f734c096036b8ff4219))
- **`refactor`**: code changes about structure, variable and function without changing logic function (e0f842c)
- **`docs`**: modify documents only ([204ca9e](https://github.com/rustsbi/rustsbi/commit/204ca9eafa45b91642536356e19edb4bef0e53d6))
- **`ci`**:  changes to Continuous Integration configuration ([52c4c55](https://github.com/rustsbi/allwinner-hal/commit/52c4c553437f913b9d4677683c4fdbca963af317))
- **`test`**: add test case

#### subject

Summarize the change in one short line of description:

- be entirely in lowercase with the exception of proper nouns, acronyms and the words that refer to code, like function/variable names
- use the imperative present tense, such as "add", "fix", "update"
- no period `.` at the end
- preferably 50 characters or less, and no more than 72 characters

#### body

Keep the second line blank, separating `<header>` from `<body>` by a blank line. Here is where you expand on description of the change in detail, and I hope the way to organize your thought above helps. Just as in `<subject>`, use the imperative present tense: "change" not "changed" nor "changes".

#### footer

Keep another line blank between `<body>` and `<footer>`.
The `<footer>` contains the sign-off and other references related to this commit:

- **`Signed-off-by:`** use `git commit` command with `-s` parameter.
- **`Fix:`** add a reference to the open issue if your commit fix it.
  - Examples: `Fix: https://github.com/rustsbi/rustsbi/issues/63` or `Fix: #63`
- **`Refs:`** for other references.
  - Examples: `Refs: https://github.com/riscv-non-isa/riscv-sbi-doc/releases/download/vv3.0-rc1/riscv-sbi.pdf` ([fbbab51](https://github.com/rustsbi/rustsbi/commit/fbbab51bd9fdf6ca9b459f19714c8fd9a75240cc))
- **`BREAKING CHANGE:`** introduce this commit may not be compatible with code that relies on the original fuctionality. These changes require community users to modify their code after updating to a version that contains this commit. In parallel, the following actions are performed:
  - **Update Documentation:** update relevant documentation to clearly indicate the content of the change, its impact, and possible migration steps.
  - **Upgrade Version:** release a new version, clearly label the `BREAKING CHANGE` in the release notes.
  - **Notification:** notify users and developers or team members of upcoming changes in advance so that they can prepare for it.

### Commit Messages Template

```
rt: refine constant declaration and document on legacy extensions

Modify links on document; do not expose legacy extension EID constants, 
developers should use from `sbi-spec` crate. 

Signed-off-by: Zhouqi Jiang <luojia@hust.edu.cn>
```

```
main: add embedded-cli based serial command line console support

- Add embedded-cli based serial command line console support for bouffaloader, 
which features the command-line menu with autocomplete, history, and help command. 
- Add flash guide for bouffaloader on Windows in `README.md`, with which 
we can have a test on M1s Dock. 

Refs: https://github.com/rustsbi/bouffalo-hal/pull/4
Refs: https://github.com/rustsbi/bouffalo-hal/pull/5
Signed-off-by: DongQing <placebo27@hust.edu.cn>
```

```
binary: enhance `SbiRet` structure functions to match `core::result::Result` APIs

- binary: change `SbiRet::and` signature to `fn and<U>(self, res: Result<U, Error>) -> Result<U, Error>` 
- binary: add function `is_ok_and`, `is_err_and`, `inspect` and `inspect_err` for `SbiRet` structure 

Signed-off-by: Zhouqi Jiang <luojia@hust.edu.cn>
```

When you finish reading this guideline, I believe you can also independently complete an excellent *commit message*. After a number of practically writing *commit message*, you will gradually have your own ideas and style, becoming more free and flexible.
