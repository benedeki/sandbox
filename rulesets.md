## 📜 Included Features

### ✅ Rulesets

The Rulesets are designed to standardize the entire organization and are based on JSON format.
They primarily focus on clearly managing standards in each CPS project and subsequent easy implementation.
You can find more about CPS Repositories protection in the [CPS-Handbook](https://github.com/absa-group/cps-handbook/blob/master/qa/testing/security/repository-security.md).

- Rulesets
  - [all_branches_rules.JSON](/rulesets/all_branches_rules.json)
  - [core_branches_rules.JSON](/rulesets/core_branches_rules.json)
  - [strict_core_branches_rules.JSON](/rulesets/strict_core_branches_rules.json)

<!-- - Required labels
- Code ownership expectations
- Other policy configurations -->

These rulesets can be used for validation and templating across other repositories.

---

### Usage of Rulesets

Individual branch protection Rulesets can be layered; if multiple rules are applied to one branch,
the strictest rule is always used. This also applies to the Branch protection rules functionality,
with which Rulesets are compatible.

More about Ruleset API you can find in [GitHub documentation](https://docs.github.com/en/rest/repos/rules).

Set [**All branches rules**](/rulesets/all_branches_rules.json) in your repository.

- Required for each CPS repository.
- Importing this JSON file will protect all current and future branches of the repository on a fundamental level.
- The only required rule is the `signed commits` one.

Set [**Core branches rules**](/rulesets/core_branches_rules.json)

- Required for all strategic **Long-lived branches** in CPS repositories.
- If the project uses multiple long-lived branches, you need to add them manually after Ruleset import
  or [edit the JSON file](#how-to-update-json-file-for-more-branches) before import.

Set [**Strict core branches rules**](/rulesets/strict_core_branches_rules.json)

- If the project needs to use a more strict level of ruleset, use this Strict JSON file, add the current branch after
import manually or [edit it](#how-to-update-json-file-for-more-branches) before import.

#### How to update JSON file for more branches

```json
{
  "conditions": {
    "ref_name": {
      "exclude": [],
      "include": [
        "~DEFAULT_BRANCH",
        "refs/heads/support/**/*",
        "refs/heads/master"
        // You can use the name of a specific branch or a naming convention.
      ]
    }
  }
}
```
>- `refs/heads/**` is a GitHub convention for a start naming a branch in a Ruleset JSON file.
>- `~DEFAULT_BRANCH` is a GitHub alias for the default (main) branch in the repository.
>
>



### [Rulesets](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/about-rulesets)

You can create rulesets to control how people interact with selected branches and tags in a repository.
You can control things like who can push commits to a specific branch or who can delete or rename a tag.

It is a newer feature that, in many ways, copies the Branch protection rules. It is mainly characterized
by a more straightforward
settings and the ability to layer rules on each other.

The file [ruleset.json](assets/ruleset.json) provides an example of using a ruleset.

