# 32521

## Current behavior

Renovate is configured to monitor both all the files in a GitHub repository to identify and create pull requests (PRs) for dependency vulnerabilities. When Renovate identifies a vulnerability in both files for a specific dependency (e.g., Jinja2 < 3.1.4 with a known CVE), only the requirements.txt file is included in the generated PR. The poetry.lock file, though flagged as vulnerable in the logs, is not included in the PR upgrades section.

The logs confirm that poetry.lock has been identified as containing the vulnerable dependency, but no updates are applied to this file in the resulting PR. This issue persists even though enabledManagers includes both poetry and pip_requirements.

## Expected behavior

When vulnerabilities are detected in dependencies across both requirements.txt and poetry.lock, Renovate should create a PR that includes updates for both files, ensuring that all vulnerable dependencies are addressed in a single PR. This behavior would ensure that vulnerabilities in poetry.lock are patched alongside requirements.txt in each PR, as defined in the configuration.

## Logs

Here’s a relevant section from the logs showing that the poetry.lock file for jinja2 (with version <3.1.4) is being identified correctly along with requirements.txt:
```
   {
           "matchDatasources": ["pypi"],
           "matchPackageNames": ["jinja2"],
           "matchFileNames": ["test/poetry.lock"],
           "matchCurrentVersion": "< 3.1.4",
           "vulnerabilityFixVersion": "3.1.4",
           "prBodyNotes": [
             "### GitHub Vulnerability Alerts",
             "#### [CVE-2024-22195](https://github.com/pallets/jinja/security/advisories/GHSA-h5c8-rqwp-cp95)\n\nThe xmlattr filter in affected versions of Jinja accepts keys containing spaces. XML/HTML attributes cannot contain spaces, as each would then be interpreted as a separate attribute. If an application accepts keys (as opposed to only values) as user input, and renders these in pages that other users see as well, an attacker could use this to inject other attributes and perform XSS. Note that accepting keys as user input is not common or a particularly intended use case of the xmlattr filter, and an application doing so should already be verifying what keys are provided regardless of this fix.",
             "#### [CVE-2024-34064](https://github.com/pallets/jinja/security/advisories/GHSA-h75v-3vvj-5mfj)\n\nThe xmlattr filter in affected versions of Jinja accepts keys containing non-attribute characters. XML/HTML attributes cannot contain spaces, /, >, or =, as each would then be interpreted as starting a separate attribute. If an application accepts keys (as opposed to only values) as user input, and renders these in pages that other users see as well, an attacker could use this to inject other attributes and perform XSS. The fix for the previous GHSA-h5c8-rqwp-cp95 CVE-2024-22195 only addressed spaces but not other characters.\n\nAccepting keys as user input is now explicitly considered an unintended use case of the xmlattr filter, and code that does so without otherwise validating the input should be flagged as insecure, regardless of Jinja version. Accepting _values_ as user input continues to be safe."
           ],
           "isVulnerabilityAlert": true,
           "force": {
             "groupName": null,
             "schedule": [],
             "dependencyDashboardApproval": false,
             "minimumReleaseAge": null,
             "rangeStrategy": "update-lockfile",
             "commitMessageSuffix": "[SECURITY]",
             "prCreation": "immediate",
             "vulnerabilityFixStrategy": "lowest",
             "enabled": true
           }
         },
                 {
           "matchDatasources": ["pypi"],
           "matchPackageNames": ["jinja2"],
           "matchFileNames": ["test/requirements.txt"],
           "matchCurrentVersion": "< 3.1.4",
           "vulnerabilityFixVersion": "3.1.4",
           "prBodyNotes": [
             "### GitHub Vulnerability Alerts",
             "#### [CVE-2024-22195](https://github.com/pallets/jinja/security/advisories/GHSA-h5c8-rqwp-cp95)\n\nThe xmlattr filter in affected versions of Jinja accepts keys containing spaces. XML/HTML attributes cannot contain spaces, as each would then be interpreted as a separate attribute. If an application accepts keys (as opposed to only values) as user input, and renders these in pages that other users see as well, an attacker could use this to inject other attributes and perform XSS. Note that accepting keys as user input is not common or a particularly intended use case of the xmlattr filter, and an application doing so should already be verifying what keys are provided regardless of this fix.",
             "#### [CVE-2024-34064](https://github.com/pallets/jinja/security/advisories/GHSA-h75v-3vvj-5mfj)\n\nThe xmlattr filter in affected versions of Jinja accepts keys containing non-attribute characters. XML/HTML attributes cannot contain spaces, /, >, or =, as each would then be interpreted as starting a separate attribute. If an application accepts keys (as opposed to only values) as user input, and renders these in pages that other users see as well, an attacker could use this to inject other attributes and perform XSS. The fix for the previous GHSA-h5c8-rqwp-cp95 CVE-2024-22195 only addressed spaces but not other characters.\n\nAccepting keys as user input is now explicitly considered an unintended use case of the xmlattr filter, and code that does so without otherwise validating the input should be flagged as insecure, regardless of Jinja version. Accepting _values_ as user input continues to be safe."
           ],
           "isVulnerabilityAlert": true,
           "force": {
             "groupName": null,
             "schedule": [],
             "dependencyDashboardApproval": false,
             "minimumReleaseAge": null,
             "rangeStrategy": "update-lockfile",
             "commitMessageSuffix": "[SECURITY]",
             "prCreation": "immediate",
             "vulnerabilityFixStrategy": "lowest",
             "enabled": true
           }
         },
```
Here is an excerpt of the PR creation output showing only requirements.txt included in the upgrades section:
```
"branchesInformation": [
{
"branchName": "jinja2-vulnerability",
"prNo": 1,
"prTitle": "Jinja2",
"result": "done",
"upgrades": [
{
"datasource": "pypi",
"depName": "Jinja2",
"packageFile": "test/requirements.txt",
"newVersion": "3.1.4",
...
}
]
}
]
```

## Link to the Renovate issue or Discussion

https://github.com/renovatebot/renovate/discussions/32521
