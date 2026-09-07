---
on:
  workflow_dispatch:
permissions:
      contents: read
engine: copilot
network: defaults
---

# daily-digest

Every weekday, create a GitHub issue that summarises all open issues
and pull requests in this repository. Group them by label. Include the
total count, the title, the author, and how long each item has been
open. Title the issue "Daily Digest – <date>".


<!--
## TODO: Customize this workflow

The workflow has been generated based on your selections. Consider adding:

- [ ] More specific instructions for the AI
- [ ] Error handling requirements
- [ ] Output format specifications
- [ ] Integration with other workflows
- [ ] Testing and validation steps

## Configuration Summary

- **Trigger**: Manual trigger
- **AI Engine**: copilot
- **Network Access**: defaults

## Next Steps

1. Review and customize the workflow content above
2. Remove TODO sections when ready
3. Run `gh aw compile` to generate the GitHub Actions workflow
4. Test the workflow with a manual trigger or appropriate event
-->
