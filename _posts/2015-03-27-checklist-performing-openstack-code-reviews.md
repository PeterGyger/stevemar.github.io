---
title: "Checklist for performing OpenStack code reviews"
excerpt: "Best practices for getting reviewing OpenStack patches"
tags: 
  - software
  - openstack
  path: /images/checklist.jpg
  thumbnail: /images/checklist.jpg
---

_Originally posted on [https://developer.ibm.com/opentech/2015/03/27/checklist-performing-openstack-code-reviews](https://developer.ibm.com/opentech/2015/03/27/checklist-performing-openstack-code-reviews)_

Reviewing code is essential to open source development. Code reviews are at the heart of the OpenStack community; code reviews must be performed by Core Reviewers to merge patches within OpenStack projects. A solid code review can be as beneficial as the idea being proposed. The following post may not be for everyone, it's just a guide that I use when reviewing code.

## Where to start

Pick a project that has familiar concepts. Know django and web development? Check out Horizon. Want to learn about security? Check out Keystone. There are so many OpenStack and Stackforge projects now available, pick one that interests you. It's a good idea to know the projects that are related to your primary focus. For instance, Keystone also has: keystone-specs, pycadf, python-keystoneclient and keystonemiddleware. To make searching through Gerrit easier, create bookmarks, in the form of:

```
https://review.openstack.org/#/q/status:open+project:{repo}/{projectName},n,z
```

[Sean Dague](https://twitter.com/sdague) has a great write up about how to leverage [Gerrit queries to avoid OpenStack review overload](https://dague.net/2013/09/27/gerrit-queries-to-avoid-openstack-review-overload/).

## Picking the right patch

* Patches related to blueprints will have a lot of code. Pro: Better chance to provide helpful reviews. Con: Lots of code, could be a bit complicated for a newcomer.
* Patches related to bugs, are usually smaller changes. Pro: Easier to understand whats going on. Con: Might limit your feedback.
* If a patch has a +A (+1 for workflow), then it's already approved, choose another patch.
* If a patch is marked as WIP (-1 for workflow), then it's a Work In Progress, choose another patch (this depends on the author and project, but usually it's not ready for review).

## Reviewing

This is a checklist that I try to follow, this is by no means complete, or suitable for everyone. _**The golden rules**_

* Good quality code reviews produce good quality code.
* Don't be biased, treat colleagues (from the same company) the same as you would anyone else; or better yet, review their patches even harder.
* Avoid rubber stamping. Rubber stamping is +1'ing with no comments. Obviously this depends on the patch, if it's a simple fix then it's probably fine. But try to let the author know you've actually read and understood the changes, leave an inline comment or general comment.

### Commit messages

* It goes title (on one line), then description (many lines).
* The title should be a summary of what's happening: "Do x y z".
* The description should explain WHY. Is the change a refactoring? Is it a bug? A new feature? What's the motivation behind the change? It might be complicated, but it should be clear.
* Know the keywords and their syntax:

  * Closes-Bug: 123456, Partial-Bug: 123456, Related-Bug: 123456
  * Implements bp blueprint-name
  * Co-Authored-By: Name <e-mail>
  * Is there a DocImpact, APIImpact or SecurityImpact?

OpenStack Wiki link for [GitCommitMessages](https://wiki.openstack.org/wiki/GitCommitMessages).

### Tests

* Is there an accompanying test that covers the new behaviour?
* Is there sufficient test coverage? A single test may not be enough.
* Are there negative tests that cover failure scenarios, and assert exceptions are raised?
* This topic had a [memorable thread](http://lists.openstack.org/pipermail/openstack-dev/2013-October/017958.html) on the mailing list.

### Libraries

* Is there a library that an author could leverage?
* If the author is adding a new library, is it appropriately licensed?
* Is the import order correct? It should go: standard lib, third party libs, then local to the project.
* Is the author importing a library or making a new utility function when they can be leveraging Oslo?

  * Importing json instead of using oslo.serialization?
  * Importing datetime instead of oslo.utils?

A list of [all the Oslo projects](https://wiki.openstack.org/wiki/Oslo)

### Messages

* Is the author properly enclosing messages with _()? These indicate that they are marked for translation.
* Don't mark messages within tests for translation.
* Don't mark debug messages for translation.
* In the event of an exception, is the author leaving the user with enough information? Exception messages should be helpful.
* Is the author using the right logging level, `DEBUG, INFO, WARNING`?

### Docstrings and comments

* Docstring – How to use the code
* If the author creates a function, and it's doing something non-obvious then ask for a docstring. It'll help future contributors understand what's going on.
* A docstring should summarize what the function does in a few lines, and document the input parameters, return type and return value.
* Comment - Why (rationale) and how the code works
* Comments should be properly formatted and helpful.
* Use the right format: `# TODO(stevemar)` or `# NOTE(stevemar)`
* Comments should be helpful, not blatantly obvious. Don't do the following:

  ```
  # We assert that result is true
  assertTrue(result)
  # Initiate a counter
  counter = 0
  ```

### Being Pythonic

Read this [slide deck](http://chrisarndt.de/talks/rupy/2008/output/slides.html). No really, go read it.

* `foo.get('bar', None)` This is pointless since .get() will default to None anyway.
* `foo['bar']` Can be dangerous, will result in KeyError if the `bar` doesn't exist. Use `if 'bar' in foo:`.
* Avoid \'s when line wrapping, use ()'s
* Casing:
  * `joined_lower` for functions, methods, attributes
  * `ALL_CAPS` for constants
  * `CamelCaps` for classes

### Code smells

* Duplicated code: Similar functions can usually be consolidated into one.
* Long method: A method or function that is too large, can usually be broken up into smaller pieces.
* Contrived complexity: Could a simpler design work?
* Excessively long identifiers: `headers_of_http_auth_token_response = resp['headers']` might be a bit too long.
* Excessively short identifiers: `a = self.create_auth_plugin()` might be a bit too short.
* Excessive use of literals: Try to use constants when possible.
* Complex conditionals: No one wants to try and figure out why your branch needs to check six things.

[Learn more about code smells](http://en.wikipedia.org/wiki/Code_smell#Common_code_smells).

### Other tips

* Does the patch align with the overall architecture?
* Got a question? Ping the author on IRC, or make an inline comment.
* Checkout the change, add a debug breakpoint and run a test.
* Don't try to review everything, pick as many patches are you are comfortable with. Folks will reply to your comments and expect you to review the new change set again, and again, and again...
