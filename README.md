# Running XCUITests on iOS Simulators using Docker Compose

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

Navigate to the "containers" directory:
`cd containers`

Build the node-server container:
`docker buildx build -t node-server:0.0.1 --platform=linux/amd64 node-server/`

Push the Docker image:
`docker push node-server:0.0.1`

Build the xcuitest container:
`docker buildx build -t xcuitest:0.0.1 --platform=linux/amd64 xcuitest-saucectl/`

Push the Docker image:
`docker push xcuitest:0.0.1`
