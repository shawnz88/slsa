# Frequently Asked Questions

<!-- NOTE: This page is currently not currently listed in the navbar because it is light on content. We will add a link once we have more questions. -->

## Q: Why is SLSA not transitive?

SLSA is not transitive in order to make the problem tractable. If SLSA 4
required dependencies to be SLSA 4, then reaching SLSA 4 would require starting
at the very beginning of the supply chain and working forward. This is
backwards, forcing us to work on the least risky component first and blocking
any progress further downstream. By making each artifact's SLSA rating
independent from one another, it allows parallel progress and prioritization
based on risk. (This is a lesson we learned when deploying other security
controls at scale throughout Google.) We expect SLSA ratings to be composed to
describe a supply chain's overall security stance, as described in the case
study [vision](example.md#vision-case-study).

## Q: What about reproducible builds?

When talking about [reproducible builds](https://reproducible-builds.org), there
are two related but distinct concepts: "reproducible" and "verified
reproducible."

"Reproducible" means that repeating the build with the same inputs results in
bit-for-bit identical output. This property
[provides](https://reproducible-builds.org/docs/buy-in/)
[many](https://wiki.debian.org/ReproducibleBuilds/About)
[benefits](https://static.googleusercontent.com/media/sre.google/en//static/pdf/building_secure_and_reliable_systems.pdf#page=357),
including easier debugging, more confident cherry-pick releases, better build
caching and storage efficiency, and accurate dependency tracking.

For these reasons, SLSA 4 [requires](levels.md#level-requirements) reproducible builds
unless there is a justification why the build cannot be made reproducible.
[Example](https://lists.reproducible-builds.org/pipermail/rb-general/2021-January/002177.html)
justifications include profile-guided optimizations or code signing that
invalidates hashes. Note that there is no actual reproduction, just a claim that
reproduction is possible.

"Verified reproducible" means using two or more independent build systems to
corroborate the provenance of a build. In this way, one can create an overall
system that is more trustworthy than any of the individual components. This is
often
[suggested](https://www.linuxfoundation.org/en/blog/preventing-supply-chain-attacks-like-solarwinds/)
as a solution to supply chain integrity. Indeed, this is one option to secure
build steps of a supply chain. When designed correctly, such a system can
satisfy all of the SLSA build requirements.

That said, verified reproducible builds are not a complete solution to supply
chain integrity, nor are they practical in all cases:

-   Reproducible builds do not address source, dependency, or distribution
    threats.
-   Reproducers must truly be independent, lest they all be susceptible to the
    same attack. For example, if all rebuilders run the same pipeline software,
    and that software has a vulnerability that can be triggered by sending a
    build request, then an attacker can compromise all rebuilders, violating the
    assumption above.
-   Some builds cannot easily be made reproducible, as noted above.
-   Closed-source reproducible builds require the code owner to either grant
    source access to multiple independent rebuilders, which is unacceptable in
    many cases, or develop multiple, independent in-house rebuilders, which is
    likely prohibitively expensive.

Therefore, SLSA does not require verified reproducible builds directly. Instead,
verified reproducible builds are one option for implementing the requirements.

For more on reproducibility, see
[Hermetic, Reproducible, or Verifiable?](https://sre.google/static/pdf/building_secure_and_reliable_systems.pdf#page=357)
