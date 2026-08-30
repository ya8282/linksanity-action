# Baselined fixture

This fixture contains one link that is genuinely broken (a guaranteed 404),
but the link is pre-baked into `baseline.json` in this same directory. The
self-test job that scans this directory passes `baseline: baseline.json` and
must still succeed, because a baselined link is known breakage, not new
breakage.

- Known-broken link (guaranteed 404): [this does not exist](https://github.com/this-path-does-not-exist-linksanity-selftest-404)
