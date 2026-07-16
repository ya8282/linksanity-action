# Broken fixture

This fixture intentionally contains one broken link and one valid relative
link, so the self-test workflow can prove the action detects failures.

- Broken link (guaranteed 404): [this does not exist](https://github.com/this-path-does-not-exist-linksanity-selftest-404)
- Valid relative link: [other page](./other.md)
