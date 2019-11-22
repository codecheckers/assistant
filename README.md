# assistant

A CLI tool to assist codecheckers

## Feature ideas

- run "lint" - style checks to discover common errors in submissions to a CODE CHECK process
- probably using a lot of regexes?
- rule ideas
  - detect file names in code files and see if the file exists
  - detect full paths
  - detect paths not within subdirectories of the workspace
  - detect hidden directories (`.git`, ..)
  - `LICENSE` file
  - code file syntax
  - file validy (parse XML?, load a CSV?)
- rules for default workflow/implementation
  - `MANIFEST` file
  - `Makefile`
  - ...
- "code smell" concepts could be useful to explore
- CLI
  - `codecheck https://github.com/codecheckers/Hopfield-1982`
  - `codecheck .`
  - `codecheck --rules=all (/)full/or/relative/path`
  - `codecheck --implementation=default git+https://git.universitry.org/repo`
- Browser-UI
  - simple, like BinderHub
  
**Similar/related tools**

- https://github.com/ropenscilabs/checkers

## Implementation ideas

- Python
  - pro: common in Open Science domain, cross platform
  - con: requires Python
  - resources
    - https://docs.python-guide.org/scenarios/cli/
    - https://medium.com/@trstringer/the-easy-and-nice-way-to-do-cli-apps-in-python-5d9964dc950d
- Go
  - pro: compiles to a single static binary, runs everywhere
  - con: lack of dev experience
  - resources
    - https://dzone.com/articles/how-to-create-a-cli-in-go-in-few-minutes
    - https://blog.alexellis.io/5-keys-to-a-killer-go-cli/
    - https://github.com/urfave/cli
    - https://binx.io/blog/2018/11/25/go-cli-apps/
    - https://towardsdatascience.com/how-to-create-a-cli-in-golang-with-cobra-d729641c7177
    - linters, e.g. https://github.com/ropenscilabs/checkers
    - can probably be wrapped in Python and R packages
      - https://www.nathanvangheem.com/posts/2017/06/03/embedding-golang-in-python-with-groupcache.html
      - https://medium.com/learning-the-go-programming-language/calling-go-functions-from-other-languages-4c7d8bcc69bf
      - https://hackernoon.com/extending-python-3-in-go-78f3a69552ac
      - https://github.com/go-python/gopy
      - https://github.com/nchern/go-R
- R
  - resources
    - https://github.com/r-lib/cli
    - https://colinfay.me/create-r-cli-npm/
