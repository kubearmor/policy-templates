# Contribution Guide

KubeArmor maintainers welcome individuals and organizations from across the cloud security landscape (creators and implementers alike) to make contributions to the project. We equally value the addition of technical contributions and enhancements of documentation that helps us grow the community and strengthen the value of KubeArmor. We invite members of the community to contribute to the project!

To make a contribution, please follow the steps below.

1. Fork this repository (policy-templates)

   First, fork this repository by clicking on the Fork button (top right).

    ![fork button](https://github.com/kubearmor/KubeArmor/raw/main/.gitbook/assets/fork_button.png)  

   Then, click your ID on the pop-up screen.

   ![fork screen](https://github.com/kubearmor/KubeArmor/raw/main/.gitbook/assets/fork_screen.png)  

   This will create a copy of KubeArmor in your account.

   ![fork repo](https://github.com/kubearmor/KubeArmor/raw/main/.gitbook/assets/forked_repo.png)  

2. Clone the repository

   Now clone policy-templates locally into your development environment.

   ```shell
    $ git clone https://github.com/kubearmor/policy-templates.git
   ```

   This will clone a copy of policy-templates installed in your development environment.

3. Make changes

   First, go into the repository directory and make some changes.

   Please refer to the [development guide](https://github.com/kubearmor/KubeArmor/blob/main/contribution/development_guide.md) to set up your environment for policy-templates contribution.

4. Check the changes

   Before committing the changes, run "test_kubearmor.sh".

   ```shell
   cd policy-templates
   ~/policy-templates$ ./test_kubearmor.sh
   ```

   If you encounter any warnings or errors, please address them first.

5. Commit changes

   Use "git status" to see your changes and add them to the branch with "git add".

   ```shell
    $ cd policy-templates
    ~/policy-templates$ git status
    ~/policy-templates$ git add [changed file]
   ```

   Then, commit the changes using the "git commit" command.

   ```shell
    ~/policy-templates$ git commit -s -m "Add a new feature by [your name]"
   ```

   Ensure that your changes are properly tested on your machine.

6. Push changes to your forked repository

   Push your changes using the "git push" command.

   ```shell
    ~/policy-templates$ git push
   ```

7. Create a pull request with your changes:

   - Go to your repository on GitHub.

   ![commit ahead]https://github.com/kubearmor/KubeArmor/raw/main/.gitbook/assets/commit_ahead.png)  

   - Click the "Pull request" button.

   ![after pull request](https://github.com/kubearmor/KubeArmor/raw/main/.gitbook/assets/after_pull_request.png)  

   - After reviewing your changes, click 'Create pull request'.

   ![open pull request](https://github.com/kubearmor/KubeArmor/raw/main/.gitbook/assets/open_pull_request.png)  

   - A pull request should detail all commits as specifically as possible, including "Fixes: #issue_number".

   - Finally, click the "Create pull request" button.

   The changes will be merged after review by the respective module owners. Once merged, you will receive a notification, and the corresponding issue will be closed.

8. DCO Signoffs

   To ensure that contributors are only submitting work that they have rights to, we require everyone to acknowledge this by signing their work. Any copyright notices in this repo should specify the authors as "KubeArmor authors".

   To sign your work, add this line at the end of your commit message:

   ```
   Signed-off-by: FirstName LastName <email@address.com>
   ```

   You can use the `-s` or `--signoff` option with `git commit`.

   By doing this, you state that the source code being submitted originated from you (see https://developercertificate.org).