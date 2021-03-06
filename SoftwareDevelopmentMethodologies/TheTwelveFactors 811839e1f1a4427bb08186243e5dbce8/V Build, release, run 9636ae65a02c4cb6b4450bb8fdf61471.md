# V. Build, release, run

### Strictly separate build and run stages

A [codebase](I%20Codebase%202b059202958543419888e3e678b44fc5.md) is transformed into a (non-development) deploy through three stages:

- The *build stage* is a transform which converts a code repo into an executable bundle known as a *build*. Using a version of the code at a commit specified by the deployment process, the build stage fetches vendors [dependencies](II%20Dependencies%2051ed07fe144d4619b2597e06ca3bf96e.md) and compiles binaries and assets.
- The *release stage* takes the build produced by the build stage and combines it with the deploy’s current [config](III%20Config%205b5606111aee443b801e040561320667.md). The resulting *release* contains both the build and the config and is ready for immediate execution in the execution environment.
- The *run stage* (also known as “runtime”) runs the app in the execution environment, by launching some set of the app’s [processes](VI%20Processes%20b60f1b06f8364a1388aa44f32f417c5e.md) against a selected release.

![release-2.png](V%20Build,%20release,%20run%209636ae65a02c4cb6b4450bb8fdf61471/release-2.png)

**The twelve-factor app uses strict separation between the build, release, and run stages.** For example, it is impossible to make changes to the code at runtime, since there is no way to propagate those changes back to the build stage.

Deployment tools typically offer release management tools, most notably the ability to roll back to a previous release. For example, the [Capistrano](https://github.com/capistrano/capistrano/wiki) deployment tool stores releases in a subdirectory named `releases`, where the current release is a symlink to the current release directory. Its `rollback` command makes it easy to quickly roll back to a previous release.

Every release should always have a unique release ID, such as a timestamp of the release (such as `2011-04-06-20:32:17`) or an incrementing number (such as `v100`). Releases are an append-only ledger and a release cannot be mutated once it is created. Any change must create a new release.

Builds are initiated by the app’s developers whenever new code is deployed. Runtime execution, by contrast, can happen automatically in cases such as a server reboot, or a crashed process being restarted by the process manager. Therefore, the run stage should be kept to as few moving parts as possible, since problems that prevent an app from running can cause it to break in the middle of the night when no developers are on hand. The build stage can be more complex, since errors are always in the foreground for a developer who is driving the deploy.