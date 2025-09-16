---
date: '2025-09-16T18:14:34+05:30'
draft: false
title: 'Forgejo: Running Playwright Tests On Docker Container'
summary: "Playwright does not support non-Debian Linux distributions directly, so I created a container to run only Forgejo test cases, mounting the directory instead of copying it."
tags:
- Docker
- Forgejo
- Playwright
params:
    author: "Yashwanth Rathakrishnan"
    ShowReadingTime: true
    ShowCodeCopyButtons: true
    ShowToc: true
    TocOpen: true
    ShowBreadCrumbs: true
    ShowCodeCopyButtons: true
---
# Overview
The Forgejo project uses Playwright for testing its codebase. However, Playwright does not work on non-Debian-based Linux distributions. The usual workaround is to spin up a VM, but this consumes many resources and slows down your machine. 

I couldn't find any Docker image that solves this problem. Although the solution is simple, no one seems to have addressed it yet. So, I created a Docker image based on the official Playwright image specifically for testing Forgejo. 

I created a Docker image based on Playwright's official image with all prerequisites installed, so we can mount the project directory at runtime to run Forgejo test cases without copying the project into the image.

## Playwright Setup
> Checkout the official [**README.md**](https://codeberg.org/forgejo/forgejo/src/branch/forgejo/tests/e2e/README.md) written for `e2e` testing.

1. Pull the Docker Image:
```sh
docker pull iamyaash8/playwright-forgejo:v1.54.2.1-noble
```
2. Change directory into the Forgejo project and run the container in interactive mode:
```sh
cd forgejo/
```
```sh
docker run -it --rm -v .:/home/forgejo-tester iamyaash8/playwright-forgejo:v1.54.2.1-noble
```

---
### Inside the Container
Make sure to clean frontend build, before running any tests and install `playwright`:
```sh
make clean frontend && npx playwright install-deps
```
<!-- - Set the environment variable for `TAGS`:
```sh
TAGS="sqlite sqlite_unlock_notify" >> ~/.bashrc
source ~/.bashrc
``` -->
---

**Interactive Testing** _(recommended)_:
> _Ensure you completed the above mentioned setup_
```sh
make test-e2e-debugserver
```
This command runs a forgejo instance locally, which already comes with dummy data to run tests on it. Playwright will be running tests on the local Forgejo instance that is currently running.

In case you see this error:
```sh
sed -e 's|{{REPO_TEST_DIR}}||g' \
        -e 's|{{TEST_LOGGER}}|test,file|g' \
        -e 's|{{TEST_TYPE}}|e2e|g' \
                tests/sqlite.ini.tmpl > tests/sqlite.ini
GITEA_ROOT="/home/iamyaash/Projects/codeberg/forgejo" GITEA_CONF=tests/sqlite.ini ./e2e.sqlite.test -test.run TestDebugserver -test.timeout 24h
Could not find gitea binary at /home/iamyaash/Projects/codeberg/forgejo/gitea
make: *** [Makefile:697: test-e2e-debugserver] Error 1
```
It's looking for a binary named `gitea`, which would have been built during the previously required build step. However, you can skip that step by using this **workaround**:
```sh
touch gitea && sudo chmod 744 gitea
```

### Running Test

- **You can run the "full test suite"**:
    ```sh
    make test-e2e-sqlite
    ```
    ```sh
    make test-e2e-mysql # when using mySQL database
    ```
    ```sh
    make test-e2e-pgsql # when using PostgreSQL database
    ```

- **You can run individual tests**:
    ```sh
    npx playwright test tests/e2e/pr-review.test.e2e.ts
    npx playwright test tests/e2e/* # run e2e tests only
    # it's flexible to use it the way you want
    ```
