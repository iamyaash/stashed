---
date: '2025-12-23'
draft: false
title: 'Forgejo: Getting Started with Integration Tests & E2E Tests'
summary: "A beginner-friendly guide to running integration tests in Forgejo. Learn how to execute individual tests, use tools like 'make' and 'go', and explore the essential basics to help you get started with integration testing."
tags:
- Forgejo
- Test
params:
    author: "Yashwanth Rathakrishnan"
    ShowReadingTime: true
    ShowCodeCopyButtons: true
    ShowToc: true
    TocOpen: true
    ShowBreadCrumbs: true
    ShowCodeCopyButtons: true
---
<!-- # Overview
This guide will focus on looking through the errors and finding ways to debug and fix it instead of vaguely mentioning how to work with it. -->

Checkout this [documentation page](https://codeberg.org/forgejo/forgejo/src/branch/forgejo/tests/integration/README.md) which explains working with integration tests, in case you want to look at the official documentation.

# Running Integration Tests
Running integrations tests are simple in forgejo, as it uses `make` to run tests. 
Ensure you run this command before executing any tests:
```sh
make clean build
```
### Full-Suite Test
As [explained here](https://codeberg.org/forgejo/forgejo/src/branch/forgejo/tests/integration/README.md#how-to-run-the-tests), we can use `make` to run tests for each database:
```sh
make test-sqlite #runs tests using SQLite
make test-pgsql  #runs tests using PostgreSQL
make test-mysql  #runs tests using MySQL
```
This runs the full-suite test using the respective database, in case you want to cover all tests.
### Individual Test
As [explained here](https://codeberg.org/forgejo/forgejo/src/branch/forgejo/tests/integration/README.md#running-individual-tests), we can run individual integration tests that cover specific parts of the test instead of running the full test suite.

```sh
make test-sqlite#GPG
```

The part that starts with `#` is used to run isolated tests. We need to know the name of the function used in the test case.

For example, if you encounter an error in a specific part of the code, you should ensure that the error occurs within a defined test function:
```sh
func doAutoPRMerge(baseCtx *APITestContext, dstPath string) func(t *testing.T) {
	...
}
```

If the error originates within this function, you can use its name to run the individual test:

```sh
make test-sqlite#doAutoPRMerge
```

{{< collapse summary="`OUTPUT: make test-sqlite#doAutoPRMerge | tail`" >}}
```sh
make test-sqlite#doAutoPRMerge | tail
testing: warning: no tests to run
??? [TestLogger] 2025/09/24 17:34:15 modules/ssh/ssh.go:332:Listen() [I] Adding SSH host key: /home/iamyaash/Projects/codeberg/forgejo-official/tests/integration/gitea-integration-sqlite/data/ssh/gitea.rsa
??? [TestLogger] 2025/09/24 17:34:15 modules/ssh/init.go:28:Init() [I] SSH server started on localhost:2203. Cipher list ([chacha20-poly1305@openssh.com aes128-ctr aes192-ctr aes256-ctr aes128-gcm@openssh.com aes256-gcm@openssh.com]), key exchange algorithms ([mlkem768x25519-sha256 curve25519-sha256 ecdh-sha2-nistp256 ecdh-sha2-nistp384 ecdh-sha2-nistp521 diffie-hellman-group14-sha256 diffie-hellman-group14-sha1]), MACs ([hmac-sha2-256-etm@openssh.com hmac-sha2-256 hmac-sha1])
??? [TestLogger] 2025/09/24 17:34:15 ...s/graceful/server.go:50:NewServer() [I] Starting new SSH server: tcp:localhost:2203 on PID: 1060294
??? [TestLogger] 2025/09/24 17:34:15 ...er/issues/indexer.go:154:func2() [I] Issue Indexer Initialization took 10.028496ms
??? [TestLogger] 2025/09/24 17:34:15 ...exer/code/indexer.go:231:func4() [I] Repository Indexer Initialization took 9.958005ms
??? [TestLogger] 2025/09/24 17:34:15 ...exer/code/indexer.go:257:populateRepoIndexer() [I] Populating the repo indexer with existing repositories
??? [TestLogger] 2025/09/24 17:34:15 ...er/cleanup_sha256.go:27:CleanupSHA256() [I] Start to cleanup dangling images with a sha256:* version
??? [TestLogger] 2025/09/24 17:34:15 ...er/cleanup_sha256.go:106:cleanupSHA256() [I] Nothing to cleanup
??? [TestLogger] 2025/09/24 17:34:15 ...er/cleanup_sha256.go:29:CleanupSHA256() [I] Finished to cleanup dangling images with a sha256:* version
PASS
```

{{< /collapse >}}

> Note: Running a `make` commad to run test case will always initiate a `debugserver` in background and runs on tests  on top of the running server.
---
### Running Integration Test on Docker Container
1. Pull this Docker Image:
```sh
docker pull iamyaash8/playwright-forgejo:v1.54.2.1-noble
```
2. Run the container while inside the Forgejo directory:
```sh
docker run -it --rm -v .:/home/forgejo-tester/forgejo iamyaash8/playwright-forgejo:v1.54.2.1-noble
```
3. Install Dependencies
```sh
npx playwright install-deps && npx playwright install
```

> By now, you should have all the required dependencies to run the integration test inside the container.
Ensure you run this command before executing any tests:
```sh
make clean build
```
Run individual test using this command:
```sh
make test-sqlite#GPG
```
Or run full-suite test using these commands:
```sh
make test-sqlite #runs tests using SQLite
make test-pgsql  #runs tests using PostgreSQL
make test-mysql  #runs tests using MySQL
```

# Running E2E Tests
1. **Clean** cached data & **Build** the binary:
```sh
make clean     #delete backend and integration files
make clean all #delete backend, frontend and integration files
```
```sh
make build          #build everything
make build frontend #build frontend files
make build backend  #build backend files
```
Run these commands in case you run into any functional or visual error, while making changes or updating the code.


2. Run **Debug Server**:
```sh
make test-e2e-debugserver
```
Debug server runs on port `3003` and it must run in the background to execute e2e tests.

3. **Running Playwright** E2E Tests:
```sh
npx playwright test --ui
```
It runs **Playwright in UI mode** which uses Chromium to simulate navigating and executing test on real time in browser and it shows the screen-shot as well.

### Running Playwright Tests on Terminal
```sh
npx playwright test tests/e2e/pr-review.test.e2e.ts
npx playwright test tests/e2e/* # run e2e tests only
# it's flexible to use it the way you want
```

### Running Playwright Tests on UI
```sh
npx playwright test --ui
```
