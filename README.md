# rabit e2e SFDX fixtures

SFDX (source-format) Git fixtures for rabit's end-to-end CI job tests
(`com.rabit.integration.cijob.triggerbuild` in rabit). rabit registers this repository as SFDX-enabled, which the
SFDX job types (SFDXVC2SF, UPVC2SF, CUPVC2SF) require; a Package from VC job on it builds through aragent's
source-format path, so the build options are tested on it too. Metadata-format fixtures live in `rabit-e2e-fixtures`.
Each branch is one fixture; tests point at it through `application-it.yaml`.

| Branch | Layout | Used by |
|---|---|---|
| `sfdxvc2sf-source` | SFDX project: `sfdx-project.json` + package directory `force-app` | `SfdxVc2SfStartBuildET` |
| `sfdxvc2sf-destructive` | `sfdxvc2sf-source` plus `ApexClassTest3` (a copy of the class in the e2e org), which the next commit deletes while changing `RabitE2ESfdxClassA`, for Run destructive changes | `SfdxVc2SfStartBuildET` |
| `sfdxvc2sf-visibility` | `vc-sfdx-build`'s first commit, with the profile also granting access to `RabitE2EPhantomClass`, a class no org or package has, for Ignore missing visibility settings | `SfdxVc2SfStartBuildET` |
| `vc-sfdx-build` | `sfdxvc2sf-source` plus a permission set and a profile (class access, login IP range, user permission), then a class added and deleted again in the next commit, for the Package from VC build options and the SFDXVC2SF Search and Substitute test | `VcPackageStartBuildET`, `SfdxVc2SfStartBuildET` |
| `vc-sfdx-sca-new` | `sfdxvc2sf-source` plus one commit that modifies `RabitE2ESfdxClassB` and adds `RabitE2ESfdxClassE`, for Apex PMD on newly added types | `VcPackageStartBuildET` |
| `upvc2sf-install` | Unlocked package `RabitE2EInstallPkg` (`force-app`) with two promoted versions in `packageAliases`, oldest first, both protected with the installation key in `application-it.yaml` | `UpVc2SfStartBuildET` |
| `cupvc2sf-create` | Unlocked package `RabitE2ECreatePkg` (`force-app`); each run adds its new version to `packageAliases` | `CupVc2SfStartBuildET` |

Conventions:
- One fixture per branch, named after the job type and the variant it exercises.
- Keep each fixture small and self-contained, so it validates against a clean Developer org.
- The SFDX job tests validate only; the package tests really install their versions into the org on every run.
- `cupvc2sf-create` gets a commit from AutoRABIT on every run, so pull before changing it and never force-push it.
- Baseline revisions in `application-it.yaml` are commit hashes on these branches, so never rewrite their history.
- When a branch's contents change, update the fixture keys in rabit's `application-it.yaml`.
