## 💡 The problem

<!-- What problem is being solved? -->
<!-- Include relevant context such as jira ticket, issue link or slack thread-->

## 🔧 The solution

<!-- Short description of the changes that are being made -->

## 💬 Additional notes

<!-- Other thoughts, concerns or relevant links -->

## 💣 Breaking changes 

<!-- Describe what breaks and how to migrate -->
<!-- ⚠️ A breaking change needs a new major version:
     - the PR title must be `type!: ...`, or the description must contain a `BREAKING CHANGE: ...` footer
     - without it, release-please only cuts a patch/minor and consumers get the change silently on the current major version -->
<!-- Remove the section if not relevant -->

## ✅ Checklist

<!-- Check off when the items are fulfilled -->

- [ ] The PR title follows [Conventional Commits](https://www.conventionalcommits.org/)
- [ ] Actions are pinned to a commit SHA, and workflow permissions are kept minimal
- [ ] `README-maven-publish.md` and `README-gradle-publish.md` are updated if relevant
- [ ] `.github/workflows/ci.yml` and `fixture` is updated with tests and test data if relevant
- [ ] No secrets, project IDs or other sensitive values are hardcoded
