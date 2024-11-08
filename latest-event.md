- latest-event extension

Original RedisTimeSeries emits 'ts.add:dest' pub/sub event when a destination channel receives a new data from the source channels bucket.
The problem, that when timeseries get backlog timestamp - to a bucket earlier thant the last bucket -the bucket in the source and in the destination timeseries updated, but no message emitted.
In beam we have logics that should be triggered if a destination channel value is chaged - so we added a small patch to the source emitting message if value is changed.
Named it as latest-event, and added a binary value argument to the runtime arguments of the time-series library.

The original RedisTimeSeries library forked to beaml1ve as a public fork.
The feature/beam-latest-event branch is opened - containing one commit with the modifications.
The modification in the config.gh and config.c sources are to manage the argument, and the module.c contains the inserted ement emitting function.

To deploy a new release:

1. SYNC the fork
2. Select the commit would like to deploy - form example the v1.2.3 in the original repository (redis/RedisTimeSeries). Find the same commit in the forked repo, it will have different commit hash - for example "123456...".
3. Open a branch for the new release from the commit, name it as 1.2.3
4. Cherry pick the commit from the feature/beam-latest-event containing the modifications. It might conflict in the config.h or config.h - if the argumenbts of the original repo also extended, or in the module.c if the logic of the modle has been changed. The config conflicts can be resolved simply by merging the current and the incomming commit, that is simple. If module.c conflicting need to check what happened and change the code.
5. If there are bit modifications, than apply the modifications to the feature=RedisTimeSeries repo, and merge (rebase) the commints together to form a single commit for all modifications that can be used in the future.

The ecosystem rpositora redis-stack branch is building the source and packing it to the container used by the deploiyments.
