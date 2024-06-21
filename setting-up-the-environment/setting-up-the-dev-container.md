# Setting Up The Dev Container

### Connecting via VS Code

Now that SSH is set up on our system, let's connect to our system via VS Code.

1.  Create SSH keys for the Main System. Run the following commands:

    ```bash
    # Run in Main System command prompt or terminal.
    ssh-keygen
    ```
2.  Copy the SSH keys to the LXC for easier access.

    ```bash
    cd .ssh
    scp id_ed25519.pub <username>@dev-debian:~/.ssh/authorized_keys
    ```
3.  Try SSHing into the LXC.

    ```bash
    ssh <usernama>@dev-debian

    # If the username of Main System is same as the new user made in LXC.
    ssh dev-debian
    ```



    <figure><img src="../.gitbook/assets/image (23).png" alt=""><figcaption></figcaption></figure>
4. Open up VS Code and install the following extensions:
   1. Dev Containers
   2. Docker
   3. HashiCorp Terraform
   4. Remote Development
   5. Remote Explorer
   6. Remote - SSH: Editing Configuration Files
5.  To add configuration for SSH connection to LXC, create a file named "config" in the \~/.ssh/ directory.

    ```
    Host dev-debian
      HostName dev-debian
      User <username>
    ```

    Or create the config using VS Code, follow the [Docs](https://code.visualstudio.com/docs/remote/ssh).
6.  Connect to LXC via VS Code.

    <figure><img src="../.gitbook/assets/image (25).png" alt=""><figcaption></figcaption></figure>
7.  Here's what your VS Code connection to LXC over SSH should look like.

    <figure><img src="../.gitbook/assets/image (26).png" alt=""><figcaption></figcaption></figure>
8.  Create a directory for your project called "terraform-webserver" using the command:

    ```bash
    mkdir terraform-webserver
    ```
9.  Open this directory in VS Code using the "Open Folder..." button and select your directory in the drop down and click on "OK".

    <figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

### Setting Up Git & (Optional) GitHub

Now that your project directory is set up, let's turn it into a Git repository and create a GitHub repository to push all your code to.

1.  Open up the terminal in VS Code and to create a Git repository run the following command:

    ```bash
    # Make sure you are inside the "terraform-webserver" directory
    git init

    # If Git is not installed:
    sudo apt update && sudo apt install git -y 
    ```
2. (Optional) If you don't have a GitHub account yet, create one. Then, make a new repository in GitHub with the same name, "terraform-webserver". Remember, GitHub isn't necessary for this project to function.
3. (Optional) Connect to your GitHub from your VS Code terminal. Follow the following docs:
   1. [Generating a new SSH key.](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent?platform=linux)
   2. [Adding a new SSH key.](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account)
   3. [Testing your SSH connection.](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/testing-your-ssh-connection)
4.  Connect your local Git repository to your GitHub repository using the command:

    ```bash
    git remote add origin <GitHub Repo SSH Link>
    ```

### Creating a Terraform Dev Container&#x20;

Now that we have our Git repository set up, let's configure a Docker container for our development environment. This way, we can replicate this environment on any machine.

1. Make sure you have SSHed into your development machine and navigate to the repository we created.
2.  Open the VS Code terminal and run the following commands to create a new directory and file:

    ```bash
    mkdir .devcontainer
    touch .devcontainer/devcontainer.json
    ```

    \
    The changes will be visible in your VS Code Panel.\


    <figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>
3.  Open the "devcontainer.json" file and copy the following contents:

    ```json5
    {
        "name": "Terraform",
        "image": "mcr.microsoft.com/vscode/devcontainers/base:ubuntu-22.04",
        "features": {
            "ghcr.io/devcontainers/features/terraform:1": {
                "version": "latest",
                "tflint": "latest",
                "terragrunt": "latest"
            },
            "ghcr.io/dhoeric/features/terraform-docs:1": {
                "version": "latest"
            },
            "ghcr.io/devcontainers/features/node:1": {
                "version": "lts"
            },
            "ghcr.io/devcontainers/features/docker-in-docker:2": {
                "version": "latest",
                "dockerDashComposeVersion": "v2"
            }
        },
        "customizations": {
            "vscode": {
                "extensions": [
                    "hashicorp.terraform"
                ],
                "settings": {
                    "terraform.languageServer": {
                        "enabled": true,
                        "args": []
                    }
                }
            }
        },
        "remoteEnv": {
            "TF_LOG": "'info",
            "TF_LOG_PATH": "'./terraform.log"
        }
    }
    ```
4. Open the VS Code Command Palette with the shortcut "Ctrl+Shift+P" and write "Rebuild and Reopen in Container" and select the option "Dev Containers: Rebuild and Reopen in Container".
5. Wait till the Dev Container creation is complete.

### Connecting to AWS

Now that our development container is set up, it's time to install AWS CLI and connect it to our AWS account.

1. Make sure you are inside the Dev Container and are in the home directory of the container.
2. Download AWS CLI in the container using the [AWS Docs](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html#getting-started-install-instructions).
3.  Configure AWS CLI using the command:

    ```
    aws configure 
    ```

With this, we've completed setting up our development environment. Now, let's dive into Terraform and get our hands dirty.
