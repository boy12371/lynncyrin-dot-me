At dayjob we run a requirements bot from a website called [requires.io/](http://requires.io/). This bot checks pypi for the latest versions of all our dependencies, and automatically creates a pull request with the dependency updates.

So for example, assume `django` is at `version 1.20.3`. The django team finds a bug, and creates bugfix release `version 1.20.4`. Requires.io will create a pull request for us, updating the django version in our source code like so

```diff
- version 1.20.3
+ version 1.20.4
```

### why use a requirements bot?

There are a few reasons why requirements bots are **useful**, and a few reasons why they are **critically important**. I'll go over them:

- **useful**: being able to automatic fixing small bugs in your code. nobody wants bugs!
- **useful**: requirements bumps often contain new features, and if you bump your requirements proactively the new features will be primed to use in your codebase before you even think to use them.
- **useful**: when writing software, its always safest to update by the smallest increment possible. requirements bots alert you to the smallest possible version bumps, whereas checking them manually you would likely miss a few versions here and there.
- **critically important**: minor version bumps will often contain security updates, which you always want to get merged as quickly as possible. With requirements bots those updates are presented to you proactively, as opposed to finding out about them several days later via a CVE posted on social media.
- **critically important**: requirements bots play a strong role in keeping technical debt low via prompting developers to update dependencies via the smallest increment possible. This is in contrast to checking the dependencies manually, which would likely result in you missing some. It's undoubtable the case that there's less risk in updating from `version 1.20.3` => `version 1.20.4` [...] => `version 1.20.15` one step at a time, than in updating straight from `version 1.20.3` => `version 1.20.15`.

### fixing requirements bot pull requests

Requirements bot differ slightly in their implementations, but most of them will automatically create pull requests for you. Usually this is painless, and you can merge the pull request without incident. But sometimes the tests will fail on that pull request and you have to investigate. The pattern I use for that is:

1.  Checkout the failing branch locally, for example `requires-io-master` (for requires.io)
1.  Create a new branch for your fixes (ex `requires-io-master__fixes`), this is important because the requirements bot may push to its own branch in the middle of your work - potentially deleting it!
1.  Investigate the changelog / history / etc of all the updates packages. If you've been keeping up with your updates, this won't take long at all!
1.  Update any relevant apis / fix and relevant bugs
1.  Create a new pull request for your changes, merge it when tests pass
1.  Update the old requirements bot pull request. This should result in it having not diff against master, and you can close it.
1.  Celebrate, your code is better now! Yay progress!!!

### some example requirements bots

These are some requirements bots I've seen in action:

- https://dependabot.com/ (nodejs)
- https://requires.io/ (python)
- https://dependabot.com/ (ruby, etc)
