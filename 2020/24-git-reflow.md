# Day 24 - git_reflow Gem - Automate Your Git Workflow for You and Your Team. Build Your Own Git (Command Line) Commands

Written by {% avatar codenamev %} [Valentino Stoll](https://github.com/codenamev)

_Codes up a storm of bits. Blogs about Ruby on the Raspberry Pi, LEDs, and more at [Code Name V](https://blog.codenamev.com/). Works at [Doximity](https://workat.doximity.com/positions)._



## DRY'ing up Git Flow

Working with teams on software should be painless and fun!  Git-Reflow can solve some of the following pain points:

- "Who's working on the some-crazy-branch-name? What is it?  Do we still need it?"
- _Checks out new branch_ "Ugh, let me open up Pivotal Tracker and start this story"
- _Commits last change on feature branch and pushes to remote_ "Ok, now I'll just switch contexts a few times to create a PR with links to project tracking."... _10 minutes later_... "Done!"
- _Switches to browser, looking for tab with open PR... got it! Tests passing?_ "Nope, I'll come back in a bit."
- _Switches to browser, looking for tab with open PR... got it! Tests passing?_ "Yep, let's merge this!"... _More context switching while merging, deploying, updating tracking, cleaning up feature branches_...


## Establishing Pre-Production Checklist

Git-Reflow was built for teams with a well-defined path to production.  Similar to the checklists of [surgeons](https://www.nytimes.com/2020/01/15/health/surgeons-checklists.html) and [pilots](https://www.aopa.org/training-and-safety/students/presolo/skills/before-takeoff-checklist), software teams can benefit from a checklist that outlines the path a set of changes will go through to make it to a production environment with minimal defects.  This checklist helps to:

- Standardize code flow
- Minimize context switching at each phase of development
- Syncing in-progress work with upstream changes
- Ensure proper Pull Request documentation
- Prevent merging with unsuccessful checks
- Cleanup stale feature branches

## A Typical Checklist

Before diving into the details, let's take a quick peek at what a typical _pre-production checklist_ looks like:

- [x] Start a feature branch from the main base branch
- [x] Update in-progress work as you make changes
- [x] Submit changes for peer-review
- [x] Check on the status of the review
- [x] Verify CI checks are passing
- [x] Merge changes into the main base branch
- [x] Cleanup local and remote feature branches
- [x] Deploy changes to production

Not all teams are the same, which is why git-reflow has [an easy way to customize this process](https://github.com/reenhanced/gitreflow/wiki/Custom-Workflows).

With our checklist at the ready, let's walk through how git-reflow helps to guide and automate many of these steps.

### Starting a feature branch

```bash
git reflow start vs-branch-name
```

`git reflow start` takes in the name of the new branch name that you want to create your feature on.  You can also pass it an optional flag (`--base`) with the name of a branch you want to start from. By default, it will look up the `reflow.base-branch` git configuration or default to `master`.

Running this will switch to your base branch, pull the latest changes from your remote git server, create the new branch you provided and switch to it.

**Protip:**  Use your initials at the beginning of each branch so your team knows
  who is responsible for each. My initials are `V.S.`, so all of my branches start with `vs-`


### Update in-progress changes

```bash
git reflow refresh
```

This command updates your **feature-branch** and **base-branch** according to the **remote-location** and then merges your **base-branch** into your **feature-branch**. This is just a handy command to keep your branches up to date at any point in time if someone else has committed to the base-branch or the remote.  For convenience, some extra options are available to pull in changes from non-default sources:
```
git reflow refresh -r <remote-location> -b <base-branch>
```

### Reviewing changes

```
git reflow review
```

To help spread knowledge to multiple parties and keep code quality consistent, it is a good practice to have changes peer-reviewed.

The `review` step creates a pull request for the currently checked out feature branch against the default base branch.  After making commits to your branch, run `review`. Didn't push it up? No problem, git-reflow will do it for you.

```
git reflow review -t <title> -m <message> <base-branch>
```

**Note:** `-t` and `-m` are optional, as is the `base-branch` argument. If no
base-branch is provided, then git-reflow will look for a `reflow.base-branch` git
configuration and fallback to `master` as the default.


If you do not pass the title or message options to the review command, you will be given an editor to write your PR request commit message, similar to `git commit`. The first line is the title, the rest is the body.

```
$ git reflow review

Review your PR:
--------
Title:
rj_209_test

Body:
[lib] updates review command to address issues
--------
Submit pull request? (Y): <enter>
git fetch origin master
From github.com:meesterdude/gitreflow
 * branch            master     -> FETCH_HEAD

git push origin rj_test
Everything up-to-date

Successfully created pull request #6: rj_test
Pull Request URL: https://github.com/meesterdude/gitreflow/pull/6
Would you like to push this branch to your remote repo and cleanup your feature branch? y<enter>
```

### Checking your branch status

```
git reflow status
```

Sometimes you start working on a branch and can't get back to it for a while. It happens. Use `status` to check on the status of your work.

```
$ git reflow status

Here's the status of your review:
  branches:     reenhanced:nh-readme-update -> reenhanced:master
  number:       35
  reviewed by:
  url:          https://github.com/reenhanced/gitreflow/pull/35

[notice] No one has reviewed your pull request.
```

This gives you details on who's reviewed your pull request. If someone has participated in reviewed, but not given you an approval, this will tell you. `status` prevents you from having to open a browser to find out where your pull request is at.

### Delivering approved code

```
git reflow deliver
```

You've got your code reviewed and now you're ready to merge it down to your base branch and deploy. Git-Reflow's `deliver` command will run through your checklist to help you take care of all of the steps needed to make this happen.

Git-reflow's `deliver` requires you to have a pull request, so you'll be protected on those mornings when the coffee isn't working yet.  This was built **to get in your way and make you follow the process**. If you don't like it, do it by hand. You already know what you're doing.

You'll be presented with a pre-filled commit message based on the body of your pull request with references to the pull request and reviewers.

Want to clean up your feature branch afterwards? You'll be prompted after you edit your commit message if you want to clean up your **feature-branch** on Github. If you answer `no`, then your **feature-branch** will exist for you to clean it up later.

This is what it looks like:
```
$ git reflow deliver
Here's the status of your review:
    branches:     simonzhu24:test1234 -> simonzhu24:master
    number:       51
    reviewed by:  @codenamev
    url:          https://github.com/simonzhu24/test/pull/51

This is the current status of your Pull Request. Are you sure you want to deliver? yes

Merging pull request #51: 'last commit message', from 'simonzhu24:test1234' into 'simonzhu24:master'
git checkout master
Switched to branch 'master'
Your branch is ahead of 'origin/master' by 1 commit.
  (use "git push" to publish your local commits)

[success] Pull Request successfully merged.
Would you like to cleanup your feature branch? yes
git pull origin master
remote: Counting objects: 1, done.
remote: Total 1 (delta 0), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (1/1), done.
From https://github.com/simonzhu24/test
 * branch            master     -> FETCH_HEAD
   0d8f5e0..f853efa  master     -> origin/master
Updating 0b6782f..f853efa
Fast-forward
 README.md | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)

git push origin :test1234
To https://github.com/simonzhu24/test.git
 - [deleted]         test1234

git branch -D test1234
Deleted branch test1234 (was e130c7a).
Nice job buddy.
```

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

# Create a new command to list open PRs for your team on GitHub
# This allows you to run `git reflow teamwork`
command :teamwork, arguments: { member: nil } do |**params|
  gh_team_slug = git_config.get("github.team-slug", local: true)
  gh_team_slug = ask("Enter a GitHub team slug to look up open PRs for:").strip if gh_team_slug.empty?

  if gh_team_slug.empty?
    say "A GitHub team slug is required to lookup PullRequests.", :error
    exit 1
  else
    git_config.set("github.team-slug", gh_team_slug, local: true)
  end

  team_prs = git_server.connection.search.issues(
    q: "type:pr state:open org:#{remote_user} team-review-requested:#{remote_user}/#{gh_team_slug}",
    sort: "updated",
    auto_pagination: true
  ).body.items

  say "Open Pull Requests for the #{remote_user}/#{gh_team_slug} team:".colorize(:yellow)
  say "-------------------------------------------------------"
  team_prs.each do |pr|
    say "##{pr.number.to_s.colorize(:yellow)} by @#{pr.user.login}: #{pr.title.slice(0, 80).colorize(:yellow)}"
    say "➥  #{pr.html_url}"
  end
end
```

For full details on how you can customize workflows, see [the custom workflows wiki](https://github.com/reenhanced/gitreflow/wiki/Custom-Workflows).

## That's a wrap!

I use git-reflow everyday and I can't stress enough how much time and repetitive agony it's saved me.  There are some great things planned on the horizon as well!  The `1.0` launch plans to bring support for Gitlab, Pivotal Tracker, Trello, and an entirely new Workflow to make it easier to work with open source projects.



## Find out more

### References

- Home :: [github.com/reenhanced/gitreflow](https://github.com/reenhanced/gitreflow)
- Gem :: [git_reflow](https://rubygems.org/gems/git_reflow)
- Sponsorship :: [Like what I'm working on? Sponsor me on GitHub](https://github.com/sponsors/codenamev)

