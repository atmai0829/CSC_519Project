# Accomplishments

- Setup Ansible inventory file to use GitHub secrets. These screts keep the VM's sensitive information.
- Worked through SSH configuration with Ansible and GitHub Secrets
- Wrote an Ansible playbook which now checks if there is a current running container. If there is a running container, then it stops it and removes the container. It then pulls the latest image and deploys the container.
- Added a Deployment Job to GitHub Workflow
- Fixed all Ansible Lint errors.
- Configured workflow to run the testing step on a different port as to not cause conflict if there is running container on the same port.
- https://github.ncsu.edu/atmai/CSC519_Project/commit/6e7d3dd4538a7e0bc4855512cdaf45ffe1ba7d48

# Next Steps

To continue the project here are a list of next steps:

- Setup a Security Layer before the ansible playbook.
- Check with teaching staff to ensure that my project falls within the criteria of the project.
- Continue to stress test the system.

# Retrospective

For this checkpoint, I had many problems with getting Ansible to work correctly. The most difficult part was how to get ansible to work with using docker with community.docker. Most of the issues that I was having was solved using the console output of the error messages. This fixed most of the issues that I was having. What wasn't well was changing one thing then commiting to see if the issue would actually resolve itself. The issue was mostly rooted with community.docker, so this didn't really resolve much. For next time, I think I will be spending more time reading on documentation of the technology to have a better understanding of what something can or cannot do.
