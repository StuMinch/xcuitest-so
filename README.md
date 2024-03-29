# Running XCUITests on iOS Simulators using Docker Compose

## Docker Version

This example is based on the following versions:

- Docker Desktop = 4.26.1 (131620)
- Docker Engine = 24.0.7
- Docker Compose = v2.23.3-desktop.2

## Docker & Sauce Credentials

Set your Docker & Sauce Labs credentials as environment variables in your .zshrc or .bashrc file:

```shell
export SAUCE_USERNAME=your_sauce_username
export SAUCE_ACCESS_KEY=123-abc-345-def
export DOCKER_USERNAME=your_docker_username
export DOCKER_PASSWORD=Your_d0cker_Password!
```

Run "source ~/.zshrc" or "source ~/.bashrc" for the changes to take effect.

## Upload the test apps

Upload the the two ZIP files located in the "apps" directory to the [App Management](https://app.saucelabs.com/app-management) section of your Sauce Labs account.

## Create the Docker images

Build the node-server container:
`docker buildx build -t $DOCKER_USERNAME/node-server:0.0.1 --platform=linux/amd64 containers/node-server/`

Push the Docker image:
`docker push $DOCKER_USERNAME/node-server:0.0.1`

Build the xcuitest container:
`docker buildx build -t $DOCKER_USERNAME/xcuitest:0.0.1 --platform=linux/amd64 containers/xcuitest-saucectl/`

Push the Docker image:
`docker push $DOCKER_USERNAME/xcuitest:0.0.1`

## Run the containers

To run the containers use the following command: `docker compose up`

You should see all three containers get successfully created and then the Sauce Connect container should initiate first:

```
➜  xcuitest-so git:(main) ✗ docker compose up
[+] Running 3/0
 ✔ Container xcuitest-so-node-server-1    Created                                                                                                                0.0s
 ✔ Container xcuitest-so-sauce-connect-1  Created                                                                                                                0.0s
 ✔ Container xcuitest-so-xcuitest-1       Created                                                                                                                0.0s
Attaching to node-server-1, sauce-connect-1, xcuitest-1
sauce-connect-1  | 2024/03/29 05:41:17.692558 [control] [INFO] Sauce Connect Proxy version=5.0.1
```

If you see the following output, then the node web server container successfully spun up:

```
node-server-1    | Server running on port 8080
```

Lastly, you should see the container responsible for running the XCUITest spin up and run `saucectl` to initiate the test on an iOS Simulator in Sauce:

```
xcuitest-1       | Running version 0.174.0
xcuitest-1       | 05:41:49 INF Running XCUITest in Sauce Labs
xcuitest-1       | 05:41:49 INF Performing tunnel readiness check... timeout=30s tunnel=xcuitest-so-tunnel
xcuitest-1       | 05:41:49 INF Tunnel is ready! tunnel=xcuitest-so-tunnel
xcuitest-1       | 05:41:49 INF Launching workers. concurrency=20
xcuitest-1       | 05:41:49 INF Starting suite. region=us-west-1 suite="WebKitBrowser to Localhost" tunnel=xcuitest-so-tunnel
xcuitest-1       | 05:41:59 INF Suites in progress: 1
xcuitest-1       | 05:42:09 INF Suites in progress: 1
xcuitest-1       | 05:42:19 INF Suites in progress: 1
xcuitest-1       | 05:42:28 INF Suite started. browser= platform=iOS suite="WebKitBrowser to Localhost" url=https://app.saucelabs.com/tests/5490c447abe54548a254399c3eab4fb3
```

A successful execution will show the following output:

```
xcuitest-1       | 05:44:14 INF Suite finished. passed=true suite="WebKitBrowser to Localhost" url=https://app.saucelabs.com/tests/5490c447abe54548a254399c3eab4fb3
xcuitest-1       |
xcuitest-1       |
xcuitest-1       |   Results:
xcuitest-1       |
xcuitest-1       |
xcuitest-1       |        Name                              Duration    Status    Platform    Device                 Attempts
xcuitest-1       | ────────────────────────────────────────────────────────────────────────────────────────────────────────────
xcuitest-1       |   ✔    WebKitBrowser to Localhost           2m24s    passed    iOS 16.2    iPhone 14 Simulator           1
xcuitest-1       | ────────────────────────────────────────────────────────────────────────────────────────────────────────────
xcuitest-1       |   ✔    All suites have passed               2m26s
xcuitest-1       |
xcuitest-1       |   Build Link: https://app.saucelabs.com/builds/vdc/59ffa69e9b14392a90e94a776aa4c5bf
xcuitest-1       |
xcuitest-1 exited with code 0
```
