# GitHub Actions

see [GH docs on actions](https://docs.github.com/en/actions)

- [python black formatter check](https://github.com/Beomus/AIML-Review/actions/workflows/black.yml)

  ```yaml
  name: Lint

  on: [push, pull_request]

  jobs:
    build:
      runs-on: ubuntu-latest

      steps:
        - uses: actions/checkout@v2

        - name: Set up Python
          uses: actions/setup-python@v2

        - uses: psf/black@stable
          with:
            options: "--check --verbose"
            src: "."
  ```

- [python unittest](https://github.com/Beomus/AIML-Review/actions/workflows/unittest.yml)
  
  ```yaml
  # This workflow will install Python dependencies, run tests and lint with a
  # variety of Python versions
  # For more information see: https://help.github.com/actions/language-and-framework-guides/using-python-with-github-actions

  name: Unittest

  on: [push]

  jobs:
    build:

      runs-on: ubuntu-latest
      # understand strategy: https://docs.github.com/en/actions/using-jobs/using-a-matrix-for-your-jobs
      strategy:
        fail-fast: false # does not fail the entire job on 1 failure
        # matrix of vars to test on multiple versions on multiple OS
        matrix:
          python-version: [3.8]

      steps:
      - uses: actions/checkout@v2
      - name: Set up Python ${{ matrix.python-version }}
        uses: actions/setup-python@v2
        with:
          python-version: ${{ matrix.python-version }}
      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          python -m pip install flake8 pytest
          if [ -f requirements.txt ]; then pip install -r requirements.txt; fi
      - name: Run unittest
        run: |
          python -m unittest discover -p "*_test.py"
  ```

- [mkdocs](https://www.mkdocs.org/getting-started/) or [mkdocs-material](https://squidfunk.github.io/mkdocs-material/getting-started/)
  
  I personally prefer mkdocs-material because it looks better
  
  see [official docs](https://squidfunk.github.io/mkdocs-material/publishing-your-site/#with-github-actions)

  ```yaml
  name: 'gh page deploy'
  on:
    push:
      branches:
        - main
  jobs:
    deploy:
      runs-on: ubuntu-latest
      steps:
        - uses: actions/checkout@v3
        - uses: actions/setup-python@v4
          with:
            python-version: 3.x
        - run: echo "cache_id=$(date --utc '+%V')" >> $GITHUB_ENV 
        # optional: add cache to build quicker
        # we built a hefty page with over 70 md files in less than 10 sec
        # https://docs.github.com/en/actions/using-workflows/caching-dependencies-to-speed-up-workflows
        - uses: actions/cache@v3
          with:
            key: mkdocs-material-${{ env.cache_id }}
            path: .cache
            restore-keys: |
              mkdocs-material-
        - run: pip install mkdocs-material # and other plugins 
        - run: mkdocs gh-deploy --force
  ```
