# Proposed Revised Repo Model and Release Plan

The Node.js TSC has recently been discussing and hammering out the details
for a revised general release plan. The fundamental details of the plan are:

<img src="https://camo.githubusercontent.com/675f052419c56a583c19644d4da30cc0ff4e02bb/68747470733a2f2f636c6475702e636f6d2f6c4d48746245334e72782d3330303078333030302e706e67"/>

The `master` branch remains as the target of primary development. The majority of commits generally land there. The overall goal is to keep master as stable as possible, with periodic, numbered `*-next.x` releases being cut at fairly regular intervals.

Approximately every six months, a new *stable branch* is cut off master with an immediate `vX.0.0` release cut off that stable. Immediately after, the *semver-major* is bumped in `master`. In other words, if `master` is currently on `v3.0.0-next.x`, a new `v3.0.0` stable branch is cut off `v3.0.0-next.x` and `master` is bumped to `v4.0.0-next.x`. Six months later, a new `v4.0.0` stable branch is cut off `master` then `master` is bumped to `v5.0.0-next.x`.

As mentioned, there will be fairly regular `next.x` releases off `master`. Developers can use these releases but ought to expect things to break once in a while. Breaking changes *will* land in `master` so it *is* possible (and *likely*) that breaking changes will occur from one `next.x` release to another. In particular, tools such as `NAN` may not yet have been updated to support the changes within any particular `next.x` release, and it's *likely* that native modules will not work.

There will be *two* stable branches cut off `master` each year, overlapping each other by some period of time. From these stable branches, there will be a regular cadence of stable *beta* releases cut. Developers can (and *should*) use these and can be assured that there will *be no breaking changes* outside of the rare breaking security fix. A stable is *guaranteed* not to have *semver-major* bumps. Tools such as `NAN` *should* support all active stable releases. (Note: In practice, the expectation will be that `NAN` will fully support all active stable releases. However, there is a very slim possibility for `NAN` updates to lag slightly behind the cut of a new stable. This should be rare if it happens at all.)

Of the two stables cut each year, the second will transition into the LTS at it's six month mark. The releases cut off this LTS-bound stable are essentially the LTS *release candidates*. Tools such as `NAN` *must* support all active LTS releases.

In general, commits will land in master. Once landed, they are cherry picked out of master to each of the stable/LTS branches. Cherry-picked commits will "trickle down" through stable branches moving from *newer* branches to *older* (that is, assuming a `master` that's at `v6.0.0-next.x`, commits that land in `master` would cherry pick into stable branch `v5.x.x`; commits into `v5.x.x` would cherry pick into stable branch `v4.x.x`; and so on). This is opposed to the current joyent/node approach of landing first in the *oldest* and then moving forward towards master.

It's important to note that *not all* commits will land in `master`. Fixes will land first in the *most recent* branch in which the issue is relevant, and will be cherry-picked *back* to the older branches.

Semver breaking critical security fixes are handled as special cases. Since the major cannot be bumped in either the stable or LTS branches, breaking security fixes *will land as semver-minor bumps* with a *mandatory* documentation requirement. This isn't ideal, but there's really no other option.

Once a stable branch moves into LTS, the documented LTS plan (http://github.com/nodejs/LTS) takes over and oversight of the LTS branch falls under the responsibility of the LTS working group.

## Landing V8 in master

*Stable* V8 releases will land in `master` as frequently as is feasible. The current V8 release schedule has a new stable being cut every six weeks. The idea then, would be to have this stable being pushed into `master` every six weeks. There are, however, a number of practical limitations that could alter the schedule.

Because the six week V8 release cycle does not line up perfectly with the the six month stable branch detailed above, there will be times when a new stable V8 is released *too close* to when the new stable branch would be cut off `master`. In such cases, landing the new stable V8 will be pushed off until immediately *after* the stable branch cut. This is to ensure that there is plenty of time allowed for testing the new stable V8 and ensuring that breaking changes are known and well reviewed before moving into stable.

It is possible for *semver-major* and *semver-minor* updates to V8 to be cherry picked to stable branches, but in general, *no breaking V8 changes* can land in stable branches. This means that when the annual LTS release is cut, the version of V8 included will have had at least six months of time to stabilize.

## Versioning

The Node.js semver versioning will be rooted in the *stable* branches *instead* of `master`. Every *stable* branch represents exactly one *semver-major*. Releases from `master` are essentially *pre-release* versions of the next master.

For instance, suppose the *next* upcoming stable will be `v5.0.0`. The `next.x` releases cut from `master` would be tagged using a version like `v5.0.0-next.x`, where `x` is a monotonically increasing release number for the current major in `master`.
