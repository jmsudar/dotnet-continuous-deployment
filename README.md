# .NET Continuous Deployment GitHub Action

This GitHub Action is designed to provide a simple and effective way to build, test, and publish .NET libraries to NuGet directly within your GitHub workflows. It supports .NET projects that require building, running tests, and publishing packages, offering both default settings for quick setup and extensive options for customization.

## Features

- .NET Version Flexibility: Uses any specified version of the .NET SDK.
- Configurable Build and Test Commands: Allows for custom build configurations and test verbosity levels.
- Automated NuGet Publishing: Automatically packs and publishes your library to NuGet.
- Version Management: Automatically calculates and bumps version numbers.
- Release Creation: Creates GitHub releases with changelogs.

## Inputs

| Input                      | Description                                          | Required | Default  |
|----------------------------|------------------------------------------------------|----------|----------|
| `dotnet-version`           | The .NET SDK version to use.                         | No       | `6.0`    |
| `build-configuration`      | Configuration to use for building the project.       | No       | `Release`|
| `test-verbosity`           | Set the verbosity of test results.                   | No       | `normal` |
| `solution-path`             | The path to the solution file                       | No       | `'.'` |
| `additional-build-arguments`| Any additional arguments to include with your build command | No | `''` |
| `additional-test-arguments`| Any additional arguments to include with your test command | No | `''` |
| `package-id`               | ID of the NuGet package to create.                   | Yes      | N/A      |
| `version-bump`             | Version bump type for the release (major, minor, patch). | No   | `minor`  |
| `author`                   | Author of the repository.                            | No       | `Your Name` |
| `package-tags`             | Comma-separated list of tags to apply to the NuGet package. | No | `''` |
| `repository-url`           | URL of the repository for the NuGet package.         | No       | `''`     |
| `license`                  | License for the NuGet package.                       | Yes      | N/A      |
| `implicit-usings`          | Enable implicit usings in the project.               | No       | `disable`|
| `nullable`                 | Enable nullable reference types in the project.      | No       | `enable` |
| `package-readme-file`      | Path to the README file to use as the package description. | No | `README.md` |
| `project-file-location`    | Location of the .csproj file to build and pack.      | No       | `''`     |
| `version-prefix`           | Prefix to use for the version number.                | No       | `v`      |

## Usage

### Basic Usage

To use this action with the basic settings, create a folder containing a file in your repository with a descriptive name such as `.github/workflows/dotnet-cd.yaml` and populate it with the contents below:

```yaml
on:
  push:
    branches:
      - main
  workflow_dispatch:
    inputs:
      update_version:
        description: 'Update version (creates a new release)'
        required: false
        type: boolean
        default: false
      custom_tags:
        description: 'Custom package tags (comma-separated)'
        required: false
        default: ''
      custom_description:
        description: 'Custom package description'
        required: false
        default: ''

jobs:
  build-and-test:
    # Skip if push event with [skip cd] in commit message
    if: "github.event_name == 'workflow_dispatch' || !contains(github.event.head_commit.message, '[skip cd]')"
    permissions:
      contents: write
    runs-on: ubuntu-latest
    
    steps:
      - name: Run .NET CI Action
        uses: jmsudar/dotnet-continuous-deployment@jms-integrate-test-repo
        with: 
          package-id: '${{ github.repository_owner }}.${{ github.event.repository.name }}'
          author: ${{ github.repository_owner }}
          repository-url: 'git@github.com:${{ github.repository }}.git'
          package-tags: ${{ github.event.inputs.custom_tags || '' }} 
          license: 'GPL-3.0-or-later'
          update_version: ${{ github.event_name == 'push' && true || github.event.inputs.update_version }}
```

### Customized Usage

Update the `with:` block with any changes necessary for your use case.


## Extending the Action

This action is designed to be forked and modified as needed. You can add new functionalities, such as different publishing targets or more detailed setup and teardown processes.

## Contribution

If you have suggestions or encounter any issues, please open an issue in the repository to discuss what you would like to change.

## License

The scripts and documentation in this project are released under the [GPL-3.0 License](./LICENSE).

## Support

If you encounter any issues or have suggestions, please file an issue on the GitHub repository where this action is hosted.
