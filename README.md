# CSC519_Project

# Problem Statement & Description

Currently, our coffee ordering application is under development while also being used at the same time. The process of building, testing, and deploying updates is currently manual and inefficient. Each time a developor makes a change such as updating the menu items, fixing bugs, or implementing suggested features there are a few steps that are long and tedious. These steps are manually rebuilding the code, verifying that it runs correctly, and redeploy it to the live system. This manual workflow often leads to inconsistent environments, delayed updates, and the potential for human error. When an issue occurs in production, developers or operators must intervene directly, sometimes at inconvenient time such as when everyone is rushing to get their morning coffee. This lack of automation slows down the delivery of features and impacts the reliability of the system that both customers and staff depends on.

To solve this problem, we have developed a CI/CD pipeline that automates the entire process of our coffee-ordering system. The pipeline automatically builds and tests the applications whenever new changes are pushed to the repository. This will ensure that new updates are verified before deployment. Once the tests pass, the pipeline deploys the latest version to the production environment without requiring manual review. This will minimize downtime, reduce the risk of human errors, and enable faster delivery of features and bug fixes. By using a CI/CD pipeline, we ensure that developers can focus on improving the application while operations remain stable and consistent.

# Use Case

## Deployment of new feature from feature branch to release branch
* 1 Preconditions
- GitHub Actions workflow set to run linting, testing, building, and deployment steps.
- A ansible playbook for deployment is properly configured and has access to the target server
- Target deployment environment has Docker installed and is reachable from the CI/CD pipeline
- Secrets and credentials are safely stored in the repo's secrets
- Testing framework is implemented and functional
- A feature branch exists.
* 2 Main Flow
- A developer creates a PR to release branch from dev branch [S1]
- The Github Actions pipeline triggers automatically [S2][E1]
- Code quality and style compliance check [S3][E2]
- Tests runs [S4][E3]
- If all checks pass, the Ansible playbook runs to deploy the updated containerized application to the target environment[E4]
- The application is redeployed using Docker[E5]
* 3 Subflows
[S1] User provides PR message and requests appropriate reviewers.
[S2] Github Actions pipeline triggers even on commits on feature branch
[S3] A linting job is ran to ensure code quality and style is correct
[S4] GitHub Actions creates test environment to execute unit tests to verify functionality
* 4 Alternative Flows
[E1] Compilation fails
[E2] Code has syntax errors
[E3] Automated Tests fails
[E4] Ansible playbook can't find target server
[E5] Docker container is already running

# Pipeline Design

## Diagram
[Design HERE]

## Architecture Components

All source code and infracture playbooks and GitHub actions workflow is stored on a private Github repository. Branch protection rules are enabled to ensure that PRs pass linting, testing, and build checks before merging into the release branch.

For Linting, ESLint is used to check the code quality and syntax of JavaScript and HTML source code. Ansible-Lint is used for Ansible playbook for its best practices and syntax.

For Testing, unit and integration testing is done to ensure all endpoints are covered. Testing is done with Mocha, Chai, and Supertest.

A Docker image is built after linting and testing.

Once tests pass, Ansible connects to the targeted VM via SSH. It stops the current running container, pulls the latest image, and redeploys the updated version. The Coffee Ordering app will be live again with minimal downtime.

Any environment variables will be kept and managed through Github Secrets.

## Contraints and Guidelines

New changes are added to the release branch by PRs
All environment variables (secrets) are stored by Github Secrets
All deployments automated by Ansible playbooks
Application will run inside Docker container for reproduciblity
Code must pass ESLint and ansible lint checks before merge
Mocha, Chai, and Supertest will test endpoints