# Day 24 - git-reflow - A Git Workflow Automation CLI For Guiding Team Development From The Command Line

Written by [Valentino Stoll](https://twitter.com/thecodenamev) from [Doximity](https://grnh.se/5b1db38f1) {% avatar codenamev %}

## DRY'ing up Git-Flow

Working with teams on software should be painless and fun!  Git-Reflow can solve some of the following pain points:

- "Who's working on the some-crazy-branch-name? What is it?  Do we still need it?"
- _Checks out new branch_ "Ugh, let me open up Pivotal Tracker and start this story"
- _Commits last change on feature branch and pushes to remote_ "Ok, now I'll just switch contexts a few times to create a PR with links to project tracking."... _10 minutes later_... "Done!"
- _Switches to browser, looking for tab with open PR... got it! Tests passing?  Nope, I'll come back in a bit."
- _Switches to browser, looking for tab with open PR... got it! Tests passing?  Yep, let's merge this!"... _More context switching while merging, deploying, updating tracking, cleaning up feature branches_...

## Establishing Pre-Production Checklist

Git-Reflow was built for teams with a well-defined path to production.  Similar to the checklists of [surgeons](https://www.nytimes.com/2020/01/15/health/surgeons-checklists.html) and [pilots](https://www.aopa.org/training-and-safety/students/presolo/skills/before-takeoff-checklist), software teams can benefit from a checklist that outlines the path a set of changes will go through to make it to a production environment with minimal defects.  This checklist helps to:

- [ ] Standardize code flow
- [ ] Minimize context switching at each phase of development
- [ ] Ensure proper Pull Request documentation
- [ ] Prevent merging with unsuccessful checks
- [ ] Cleanup stale feature branches

## Custom Git Commands.  Easy!

Creating new commands, or overriding existing ones, is dead simple.  Create a `Workflow` file in the root of your project and you can do some amazing things:

```ruby
# Do something before the `start` command
before :start do
  say "Booya! Started this bad girl."
end

# Do something after the `deliver` command
after :deliver do
  run "say 'Well done!'"
end

# Override the `start` command to auto-name feature branches
command :start, arguments: { feature_branch: nil }, flags: { base: 'dev' }, switches: { loud: false } do |**params|
  base_branch    = params[:base]
  feature_branch = params[:feature_branch] || "feature-#{Time.now.strftime("%Y-%m-%d")}"

  run_command_with_label "git checkout #{base_branch}"
  run_command_with_label "git pull origin #{base_branch}"
  run_command_with_label "git push origin #{base_branch}:refs/heads/#{feature_branch}"
  run_command_with_label "git checkout --track -b #{feature_branch} origin/#{feature_branch}"
end

# Create a new command
command :boom do
  GitReflow.say "Boom."
end
```

### References

- Home :: [github.com/reenhanced/gitreflow](https://github.com/reenhanced/gitreflow)
- Gem :: [rubygems.org/gems/git_reflow](https://rubygems.org/gems/git_reflow)
– Sponsorship :: [Like what I do? Sponsor me on GitHub 💖](https://github.com/sponsors/codenamev)
