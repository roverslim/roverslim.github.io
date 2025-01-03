---
layout: post
title:  "The Accrued Cost of Friction"
date:   2025-01-02
categories: software development process
---
As someone with a pedantic mindset, I tend to notice inefficiencies in processes.

Take this example: implementing a simple, one-line change that reduces the permissions required on an endpoint. A middleware that checks for a specific set of user flags is replaced by another middleware that performs fewer checks. Functional change achieved. Now, let's add a test to verify that a lower-privileged user can access the endpoint. But here comes the first friction point: the endpoint is untested. Fine, let's add that test.
Then, the second friction point: the testing framework for endpoint routing is hardwired to a single, high-privileged user. To fix this, we’d need to refactor the test and add new functionality. That’s a bit more involved, so let’s file a backlog ticket for this change and address it when we have time.

At this point, the effort required doesn’t quite match the simplicity of the one-liner we’re working on. We’ve added a test to the route that didn’t exist before—it verifies that we haven’t broken any existing functionality, even if it currently only runs for a high-privileged user. Time to commit the changes and push them to origin so that CI picks them up.

Then comes the third friction point: CI depends on code coverage rules to pass successfully. And you can probably guess what happens next. The code coverage rules don’t account for the test file we just modified but do account for the routing file we changed. Since there’s no threshold configured, coverage fails because we’ve added code but not tests. This reduces overall code coverage.

At this point, we have two options: force-merge the changes (after passing all other CI tests and completing a peer review) or resolve the coverage issue if it’s a recurring problem. In this case, a small 0.01% coverage threshold fixes the issue.

Now, with our change in place, a test that ensures we haven’t broken anything, a record of necessary test suite enhancements, a green CI run, and a pending peer review, are we ready to go? We should click through our change to make sure it works as expected. This would be ideal, but unfortunately, here comes the fourth and final friction point: the current setup requires deploying the code to a sandbox environment. From there, we have to create a low-privileged user and manually impersonate them to test the endpoint.

Once that’s done, we can finally ship the "trivial" change to production. Success!

This is a story of resilience. When these friction points are rare, the manual effort might seem worth it for a one-off change. However, when these issues recur regularly, investing time in streamlining processes becomes crucial to improving productivity. Tracking these events can help identify "tripwires" that signal when a process or protocol needs revisiting or refining. Quantifying such events can make their impact clearer and help prioritize improvements.

![teamwork](/images/cogs.jpg)
