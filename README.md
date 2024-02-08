## How to use it:

### 1. **Add the Action to your Workflow**

In your GitHub workflow file (usually located in .github/workflows/), you can add the `artefacts-ci` action to a job. Here's an example:
    
```
jobs:
  artefacts-ci:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: art-e-fact/action-artefacts-ci@main
        with:
          artefacts-api-key: ${{ secrets.ARTEFACTS_API_KEY }}
          job-name: basic_tests
```

In this example, replace `basic_tests` with the name of your job that you already have in your `artefacts.yaml` file.

Optionally, you can add a [`ros test`](https://github.com/ros-tooling/setup-ros) job before the artefacts-ci to ensure everything is working before running the platform job:
    
```
jobs:
  ros2-test:
    runs-on: ubuntu-latest
    env:
      ROS_DISTRO: humble
    steps:
        - uses: ros-tooling/setup-ros@v0.6
          with:
            required-ros-distributions: ${{ env.ROS_DISTRO }}
        - uses: ros-tooling/action-ros-ci@v0.3
          with:
            import-token: ${{ secrets.GITHUB_TOKEN }}
            package-name: dolly_tests
            target-ros2-distro: ${{ env.ROS_DISTRO }}
            skip-tests: true

  artefacts-ci:
    needs: ros2-test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: art-e-fact/action-artefacts-ci@main
        with:
          artefacts-api-key: ${{ secrets.ARTEFACTS_API_KEY }}
          job-name: basic_tests
```




### 2. **Set up Secrets**

The artefacts-api-key input requires a secret. In your GitHub repository, go to "Settings" -> "Secrets" and add a new secret named ARTEFACTS_API_KEY with your Artefacts API key as the value.

### 3. **Run the Workflow**

Once you've set up the workflow and the secret, you can run the workflow by making a commit to the branch where the workflow file is located.


## Badge

You can also add a badge to your README to show the status of your last platform job. Replace dolly-demo with the name of your project and job name:

[![artefacts_ci](https://app.artefacts.com/api/dolly-demo/badges/basic_tests.png)](https://app.artefacts.com/dolly-demo/tests)

```
[![artefacts_ci](https://app.artefacts.com/api/dolly-infra/badges/basic_tests.png)](https://app.artefacts.com/dolly-demo/tests)
```