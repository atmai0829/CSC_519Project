# Accomplishments
- Set up the workflow up to the bulid stage. The workflow now goes through an initial linting stage that checks for any syntax errors. Currently, it has linting for the coffee-project as well as ansible files. If both steps passes, then the coffee-project has its tests ran. Once the tests passes, a docker image is built.
- Enabled branch protection for all branches when code changes occur. In the workflow file, the workflow when any changes are made to main, dev, and release*.
- Set up Github Runner. To setup runner, I ssh'd into my given VM and did the steps from Github. I ran the runner in detached mode to ensure that it still runs even without the VM having to be ssh'd into.
- Fixed all linting issues reported by the workflow.
- https://github.ncsu.edu/atmai/CSC519_Project/commit/80f66b5c0d91be5cd4fab8824ab4fee81953a5cb

# Next Steps
To continue the project here are a list of next steps:
- Setup Ansible inventory file to target the VM's IP and SSH configuration
- Add a Deployment Job to the workflow
- Write an Ansible playbook to stop current running containers, pull the latest Docker image, and start the new container with environment variables loaded
- Set up a security layer before the Ansible playbook runs

# Retrospective
For this first checkpoint, what I found to work the best was going back to past workflows and view what I did for them. Many elements of the workshop can be incorporated in my project, and that has been true thus far with the Github runner setup. For what did not work, I would say that trying to face the project as a whole rather than in individual pieces. For the future, I plan to work on each part to try to achieve perfection on each individual part.