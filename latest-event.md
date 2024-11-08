- latest-event extension

Original RedisTimeSeries emits 'ts.add:dest' pub/sub event when a destination channel receives a new data from the source channels bucket.
The problem, that when timeseries get backlog timestamp - to a bucket earlier thant the last bucket -the bucket in the source and in the destination timeseries updated, but no message emitted.
In beam we have logics that should be triggered if a destination channel value is chaged - so we added a small patch to the source emitting message if value is changed.
Named it as latest-event, and added a binary value argument to the runtime arguments of the time-series library.

The original RedisTimeSeries library forked to beaml1ve as a public fork.
The feature/beam-latest-event branch is opened - containing one commit with the modifications.
The modification in the config.h and config.c sources are to manage the argument, and the module.c contains the inserted event emitting function.

To deploy a new release:

1. SYNC the fork
2. Select the commit would like to deploy - form example the v1.2.3 in the original repository (redis/RedisTimeSeries). Find the same commit in the forked repo, it will have different commit hash in the fork.
3. Open a new branch in the fork from the selected commit for the new release, name it as 1.2.3 (semver).
4. Cherry pick the commit from the feature/beam-latest-event containing the modifications. It might conflict in the config.h or config.h - if the arguments of the original library extended, or in the module.c if the logic of the library module has been changed. The config conflicts can be resolved simply by merging the current and the incomming commit, that is simple. If module.c conflicting need to check what happened and change the code according.
5. If the patch is modified, than apply the modifications to the feature/RedisTimeSeries repo, and merge (rebase) the commits together to form a single commit for all modifications that can be used (in cherry-pick) in the future releases.

The ecosystem rpository contains a redis-stack branch. This branch is building the redis-stack container using the modified (forked) sources.

TODO: revise the built-in github actions, tests, etc
TODO: apply own CI/CD tasks is required.
