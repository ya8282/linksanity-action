# Too-many-redirects fixture

This fixture contains one link that is guaranteed to redirect (any bare
`http://github.com/...` URL, since GitHub always 301s it to `https://`), and
no other links. The self-test job that scans this directory passes
`--max-redirects 0`, so that single guaranteed redirect exceeds the limit and
the link comes back `too_many_redirects` — never `broken` or `error` — proving
the action counts and annotates that status too (linksanity-jx1), not just
`broken`/`error`.

- Guaranteed one-hop redirect (http -> https): [redirect loop under --max-redirects 0](http://github.com/this-path-does-not-exist-linksanity-selftest-redirect)
