# Transitioning your code to a new edition

In general, to update your code to a new edition, the process
will look like this:

* Make sure your build is warning-free.
* Update or add the `edition` key in `Cargo.toml`
* Fix any warnings, possibly by running `rustfix`.

At this time, the `edition` key does not yet work, and `rustfix` is in a
larval state. This section will update as our story gets better here.