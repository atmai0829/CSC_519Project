# CSC519_Project

# Problem Statement & Description

Currently, the coffee ordering application is under development while also being used at the same time. The process of building, testing, and deploying updates is currently manual and inefficient. Each time a developor makes a change such as updating the menu items, fixing bugs, or implementing suggested features there are a few steps that are long and tedious. These steps are manually rebuilding the code, verifying that it runs correctly, and redeploy it to the live system. This manual workflow often leads to inconsistent environments, delayed updates, and the potential for human error. When an issue occurs in production, developers or operators must intervene directly, sometimes at inconvenient time such as when everyone is rushing to get their morning coffee. This lack of automation slows down the delivery of features and impacts the reliability of the system that both customers and staff depends on.

To solve this problem, I have developed a CI/CD pipeline that automates the entire process of our coffee-ordering system. The pipeline automatically builds and tests the applications whenever new changes are pushed to the repository. This will ensure that new updates are verified before deployment. This will minimize downtime, reduce the risk of human errors, and enable faster delivery of features and bug fixes. By using a CI/CD pipeline, we ensure that developers can focus on improving the application while operations remain stable and consistent.

# User Story

As a developer on the coffee application team, I want to have an automated CI/CD pipeline that builds, tests, and deploys new changes whenever code is pushed, so I can ensure reliable updates, minimize manual work, and deliver new features without having to shut down the system.

# Use Case

## Deployment of new feature from feature branch to release branch

- 1 Preconditions
  - GitHub Actions workflow set to run linting, testing, building, and deployment steps.
  - A ansible playbook for deployment is properly configured and has access to the target server
  - Target deployment environment has Docker installed and is reachable from the CI/CD pipeline
  - Secrets and credentials are safely stored in the repo's secrets
  - Testing framework is implemented and functional
  - A feature branch exists.
- 2 Main Flow
  - A developer creates a PR to release branch from dev branch [S1]
  - The Github Actions pipeline triggers automatically [S2][E1]
  - Code quality and style compliance check [S3][E2]
  - Tests runs [S4][E3]
  - If all checks pass, the Ansible playbook runs to deploy the updated containerized application to the target environment[E4]
  - The application is redeployed using Docker[E5]
- 3 Subflows
  - [S1] User provides PR message and requests appropriate reviewers.

  - [S2] Github Actions pipeline triggers even on commits on feature branch

  - [S3] A linting job is ran to ensure code quality and style is correct

  - [S4] GitHub Actions creates test environment to execute unit tests to verify functionality

- 4 Alternative Flows
  - [E1] Compilation fails

  - [E2] Code has syntax errors

  - [E3] Automated Tests fails

  - [E4] Ansible playbook can't find target server

  - [E5] Docker container is already running

# Pipeline Design

## Diagram

![Pipeline Diagram](https://github.ncsu.edu/atmai/CSC519_Project/blob/main/Screenshot%202025-10-27%20164149.png)

## Architecture Components

All source code and infracture playbooks and GitHub actions workflow is stored on a private Github repository. Branch protection rules are enabled to ensure that PRs pass linting, testing, and build checks before merging into the release branch.

For Linting, ESLint is used to check the code quality and syntax of JavaScript and HTML source code. Ansible-Lint is used for Ansible playbook for its best practices and syntax.

For Testing, unit and integration testing is done to ensure all endpoints are covered. Testing is done with Mocha, Chai, and Supertest.

A Docker image is built to ensure that the environment is reproducible.

Once tests pass, Ansible connects to the targeted VM via SSH. It stops the current running container, pulls the latest image, and redeploys the updated version. The Coffee Ordering app will be live again with minimal downtime.

Any environment variables will be kept and managed through Github Secrets.

## Contraints and Guidelines

New changes are added to the release branch by PRs
All environment variables (secrets) are stored by Github Secrets
All deployments automated by Ansible playbooks
Application will run inside Docker container for reproduciblity
Code must pass ESLint and ansible lint checks before merge
Mocha, Chai, and Supertest will test endpoints

# Deploying with a Self-Hosted GitHub Actions Runner

These are the general steps to stand up a self-hosted runner and deployment target on any server (VCL, EC2, on-prem VM, etc.).

1. Connect to the target server:

   ```bash
   ssh <user>@<server-ip>
   ```

2. Install Docker on the server:

   ```bash
   curl -fsSL https://get.docker.com | sudo sh
   sudo usermod -aG docker <user>
   ```

3. Install Ansible:

   ```bash
   sudo apt install -y python3-pip
   pip3 install ansible
   ```

4. Set up an SSH key pair for the Ansible deploy step (needed even if the server deploys to itself):

   ```bash
   ssh-keygen -t ed25519 -C "deploy-key" -f ~/.ssh/deploy_key -N ""
   cat ~/.ssh/deploy_key.pub >> ~/.ssh/authorized_keys
   chmod 600 ~/.ssh/authorized_keys
   cat ~/.ssh/deploy_key
   ```

   Copy the printed private key into the GitHub repository secret `SSH_PRIVATE_KEY`.

5. Register the server as a self-hosted GitHub Actions runner (Settings → Actions → Runners → New self-hosted runner for the exact download URL and token):

   ```bash
   mkdir actions-runner && cd actions-runner
   curl -o actions-runner-linux-x64.tar.gz -L <runner download URL>
   tar xzf actions-runner-linux-x64.tar.gz
   ./config.sh --url <repository URL> --token <TOKEN>
   ```

6. Run the runner as a persistent service so it survives reboots and disconnects:

   ```bash
   sudo ./svc.sh install
   sudo ./svc.sh start
   ```

   Group membership changes (like adding the runner's user to `docker`) only take effect for new processes, so restart the runner service after making them.

7. Set the following GitHub repository secrets (Settings → Secrets and variables → Actions):
   - `SSH_HOST` = the server's IP or hostname
   - `SSH_USER` = the SSH user on the server
   - `SSH_PRIVATE_KEY` = contents of `~/.ssh/deploy_key`

8. Push a commit to `main`, `dev`, or a `release*` branch. The workflow runs `lint-javascript` → `lint-ansible` → `test` → `build` → `deploy` on the self-hosted runner, and the `deploy` job runs the Ansible playbook to stop/remove the old container and start the new `coffee-project:latest` image.

9. Verify the deployment:

   ```bash
   docker ps                     # confirm 0.0.0.0:3000->3000/tcp
   curl http://localhost:3000    # confirm page content
   ```

   Then load `http://<server-ip>:3000` in a browser to confirm the site is reachable, opening the relevant firewall/security group rule for port 3000 if needed.

   Then load `http://152.7.179.116:3000` in a browser to see the live site.

# Changed to Test Workflow
