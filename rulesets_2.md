## Recommendation for long-lived branches in the project

| Rule                                                                 |      Core branches       |   Strict core branches   |
|:---------------------------------------------------------------------|:------------------------:|:------------------------:|
| Bypass list                                                          |     Repository admin     |     Repository admin     |
| Target branches                                                      |        Long-lived        |        Long-lived        |
| Restrict creation                                                    | :heavy_multiplication_x: |    :white_check_mark:    |
| Restrict update                                                      | :heavy_multiplication_x: | :heavy_multiplication_x: |
| Restrict deletions                                                   |    :white_check_mark:    |    :white_check_mark:    |
| Require linear history                                               |  :small_orange_diamond:  |  :small_orange_diamond:  |
| Require merge queue *                                                |  :small_orange_diamond:  |  :small_orange_diamond:  |
| Require deployments to succeed before merging                        | :heavy_multiplication_x: | :heavy_multiplication_x: |
| Require signed commits                                               |    :white_check_mark:    |    :white_check_mark:    |
| Require a pull request before merging                                |    :white_check_mark:    |    :white_check_mark:    |
| Number of approvals                                                  |            1             |            2             |
| Dismiss stale pull request approvals <br>when new commits are pushed |    :white_check_mark:    |    :white_check_mark:    |
| Require review from Code Owners                                      |  :small_orange_diamond:  |    :white_check_mark:    |
| Require approval of the most recent <br>reviewable push              |    :white_check_mark:    |    :white_check_mark:    |
| Require conversation resolution before merging                       |  :small_orange_diamond:  |  :small_orange_diamond:  |
| Require pull request review from Copilot                             | :heavy_multiplication_x: | :heavy_multiplication_x: |
| Allowed merge methods                                                |          Squash          |          Squash          |
| Require status checks to pass before merging                         |    :white_check_mark:    |    :white_check_mark:    |
| Require branches to be up to date <br>before merging                 |    :white_check_mark:    |    :white_check_mark:    |
| Block force pushes                                                   |    :white_check_mark:    |    :white_check_mark:    |
| Require code scanning results                                        | :heavy_multiplication_x: | :heavy_multiplication_x: |
| Restrict commit metadata                                             | :heavy_multiplication_x: | :heavy_multiplication_x: |
| Restrict branch names                                                | :heavy_multiplication_x: | :heavy_multiplication_x: |

**Table Legend**:

- _Long-lived branch_: A branch that is part of the repository permanently or for a long time, and has a strategic importance
  like a default branch or other support branches. These kind of branches create the core of the project.
  - GitHub uses alias `~DEFAULT_BRANCH` for default branches in Ruleset JSON files.
- :white_check_mark: : Required rule for the level of Ruleset.
- :small_orange_diamond: : Optional rule for the level of Ruleset. It is not required but recommended.
- :heavy_multiplication_x: This rule is neither recommended nor required.
- \* : In case the rule **Require merge queue** is active, the rule **Require branches to be up to date before merging**
  needs to be disabled. More about this situation in [GitHub Documentation](https://docs.github.com/…ue).

The description of each rule can be found in the official documentation: [GitHub - About rules](https://docs.github.com/…es)

### Reasons

List of reasons why the Protection Rules are recommended for CPS projects.

- _Bypass list:_
  - According to the basic security [rule of least privilege](/GLOSSARY.md#least-privilege-principle),
    only repository admins are authorized to bypass the settings.
- _Restrict creation:_
  - In the ruleset, for Core branches, everyone is allowed to create matching refs.
  - In the Strict ruleset, only Repository Admins can create matching refs.
- _Restrict update:_
  - Everyone is allowed to update matching refs.
  - See the use of push restrictions for actors to the branch using this rule [below](#restrict-update).
- _Restrict deletions:_
  - For core branches, deleting matching refs is **restricted**. Only Repository admins can do it.
- _Require linear history:_
  - This is optional.
  - There is no need to force a linear history if the development reserves the process through Pull Requests with
    mandatory checks, and the Squash&Merge method is required.
- _Require merge queue:_
  - This is optional.
  - It can be helpful in projects with many PRs merging each day or similar.
  - If one of the PRs in the queue fails to merge, the entire queue is paused. This ensures that PRs are merged in
    a deterministic and validated order, reducing the risk of integration issues caused by last-minute changes
    or failed builds.
- _Require deployments to succeed before merging:_
  This is not mandatory for CPS projects, as the CPS CI/CD workflow is set differently.
- _Require signed commits:_
  - **Mandatory check**.
  - It must be set because it verifies who created the commit and that the code has not been changed since
    it was committed. This ensures easier auditability and trustworthiness of the code.
- _Require a pull request before merging:_
  - **Mandatory check**.
  - To prevent unapproved and unchecked changes to core branches.
- _Number of approvals:_
  - **Mandatory number of approvers is 1**.
  - At least one other person sees the changes before merging from the less-protected branch.
- _Dismiss stale pull request approvals when new commits are pushed:_
  - **Mandatory check**.
  - It is mandatory to approve the latest changes delivered to Pull Request.
- _Require review from [Code Owners](https://docs.github.com/…):_
  - For the Core branches ruleset, it is optional.
  - **Mandatory check** for Strict Ruleset.
  - The one responsible for the code is always one of the approvers.
- _Require approval of the most recent reviewable push:_
  - **Mandatory check**.
  - Someone other than the change author must approve the latest commits.
- _Require conversation resolution before merging:_
  - This is optional.
  - This is not required for the CPS projects' low impact on work safety.
- _Require pull request review from Copilot_
  - We don't allow automatic reviews from Copilot.
  - Copilot can be a good assistant for discovering and detecting missing tests, unchecked inputs, or duplications.
- _Allowed merge methods:_
  - The **required** method is Squash&Merge.
  - Without noise in the commit history. Easy auditability and searching in the history.
- _Require status checks to pass before merging:_
  - **Mandatory check**
  - Set checks that must be passed before merging into a protected branch are required.
  - See path sensitive usage of this rule [below](#required-status-checks-with-path-filtering-usage).
- _Require branches to be up to date before merging:_
  - **Mandatory check**
  - This ensures pull requests targeting a matching branch have been tested with the latest code version.
- _Block force pushes:_
  - **Mandatory check**
  - This is required to block force pushes. It prevents users from rewriting commit history, which helps maintain
    auditability, ensures consistent collaboration and protects against accidental or malicious code removal.
- _Require code scanning results:_
  - We do not have defined scanning tools for CPS projects.
- _Restrict commit metadata:_
  - We have not defined any commit metadata for CPS projects.
- _Restrict branch names:_
  - We do not have any restrictions for branch names in CPS projects.

## Recommendation for **all branches** of the project

Implement a minimal security Ruleset on GitHub that will automatically apply to **all branches** in all repositories.

- **Intent**
  - Increase repositories security.
  - Do not restrict developers more than necessary.
  - Easy to implement, ideally without manual work on the developer's part.

- **Minimal Ruleset content**
  - Required signed commits:
    - Increases trustworthiness and traceability.
    - Low impact on developers if GPG or SSH signing is set up correctly.
    - It ensures auth integrity: authenticity of information and the integrity of data.

> [!WARNING]
> If your repository uses a bot that creates commits (e.g., GitHub Actions bot), it is essential to configure
> the correct `user.name` and `user.email` in the YAML file of the GitHub workflow. Without this, commits may not be
> properly signed or recognized, potentially causing issues with Ruleset that requires signed commits.<br>
> Here is the [Usage documentation](https://github.com/…ge) of the bots.

- **Benefits**
  - Ensures a basic level of security.
  - Preserves developer freedom.
  - Low implementation and maintenance costs.

## Protection Rulesets Adoption

1\. Choose the level of strictness (Core branches/Strict core branches) for your long-lived branches.

If you are migrating from Branch protection rules, compare your actual settings with the [table](#recommendation-for-long-lived-branches-in-the-project)
above and choose the level.

2\. Open rulesets

Rulesets settings are under the Settings tab in the main repository list.

![ruleset-path](assets/rulesets-path.png)

**3\.** Import protection rules JSON

Click the `New ruleset` button and choose `Import a ruleset` to import protection rulesets JSON file.

**3.1** For all branches import the all_branches_rules.json
to ensure a basic level of security.

**3.2** Choose the `Core branches` or `Strict core branches` solution for your long-lived branch:

- If you want this Ruleset for other branches than the default, update the JSON file before import or add branch names
  manually after import.

```json
"conditions": {
      "ref_name": {
        "exclude": [],
        "include": [
          "~DEFAULT_BRANCH",
          "refs/heads/support/0.1.0"
       ]
      }
 }
```

>- `refs/heads/**` is a GitHub convention for starting branch names in a Ruleset JSON file.
>- `~DEFAULT_BRANCH` is a GitHub alias for the default (main) branch in the repository.

- Import core_branches_rules.json
- Import strict_core_branches_rules.json

```bash
python3 ./scripts/import_protection_ruleset.py --repo org/repository-name1 org/repository-name2 --ruleset all
```

**4\.** Register required status checks manually.

**4.1** Open the Ruleset aims to Long-lived branches, and choose `Require status checks to pass.`

**4.2** Use the `+ Add checks` button to upload the required Status checks.<br>
If you are migrating, you can look into the Branch protection rules for a list of required checks.

> If there is no visible status check suggestion in the drop-down menu, write the first few characters
> of the status check name.

![staus-check-path](assets/status_check_path.png)

**5\.** Set optional Ruleset rules manually

- If you want to set some optional Ruleset rules to your Long-lived branches, you must add them manually.
  Prepared JSON files contain only a necessary set of rules.

**6\.** Validate Rulesets with Branch protection rules.

- If you are migrating, open Branch protection rules and compare them with the imported Ruleset. If stricter rules
  are used in branch protection rules, you can change the Ruleset manually.

**7\.** Delete branch protection rules

- If you are migrating, after validation, delete Branch protection rules.