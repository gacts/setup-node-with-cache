<p align="center">
  <img src="https://user-images.githubusercontent.com/7326800/201543463-8ea68d4b-3eca-4b43-9b1b-e881bef75edd.png" alt="Logo" width="100" />
</p>

# Setup Node environment with a Cache

![Release version][badge_release_version]
[![Build Status][badge_build]][link_build]
[![License][badge_license]][link_license]

Composite GitHub Action which combines the perfect pairing of [actions/setup-node](https://github.com/actions/setup-node) with [actions/cache](https://github.com/actions/cache) for the caching.

Reducing all these workflow steps:

```yaml
steps:
  - name: Setup NodeJS
    uses: actions/setup-node@v3
    with:
      node-version: 19

  - name: Determine npm cache directory path
    id: npm-cache-dir
    shell: bash
    run: echo "dir=$(npm config get cache)" >> $GITHUB_OUTPUT

  - uses: actions/cache@v3
    id: npm-cache # use this to check for `cache-hit` ==> if: steps.npm-cache.outputs.cache-hit != 'true'
    with:
      path: ${{ steps.npm-cache-dir.outputs.dir }}
      key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
      restore-keys: ${{ runner.os }}-node-

  - name: Determine yarn cache directory path
    id: yarn-cache-dir
    shell: bash
    run: echo "dir=$(yarn cache dir)" >> $GITHUB_OUTPUT

  - uses: actions/cache@v3
    id: yarn-cache # use this to check for `cache-hit` (`steps.yarn-cache.outputs.cache-hit != 'true'`)
    with:
      path: ${{ steps.yarn-cache-dir.outputs.dir }}
      key: ${{ runner.os }}-yarn-${{ hashFiles('**/yarn.lock') }}
      restore-keys: ${{ runner.os }}-yarn-
```

Down to this:

```yaml
steps:
  - uses: gacts/setup-node-with-cache@v1
    with: {node-version: 19}
```

Or using `node-version-file` for version selection:

```yaml
steps:
  - uses: gacts/setup-node-with-cache@v1
    with: {node-version-file: .node-version}
```

Output values can be used on your choice, for example:

```yaml
steps:
  - uses: gacts/setup-node-with-cache@v1
    id: setup-node
    with: {node-version: 19}

  - if: steps.setup-node.outputs.npm-cache-hit != 'true' # or `yarn-cache-hit`
    run: npm ci
```

> Tip: Use [Dependabot][use_dependabot] to maintain your `gacts/setup-node-with-cache` version updated in your GitHub workflows.

## Support

[![Issues][badge_issues]][link_issues]
[![Issues][badge_pulls]][link_pulls]

If you find any action errors - please, [make an issue][link_create_issue] in the current repository.

## License

This is open-sourced software licensed under the [MIT License][link_license].

[badge_build]:https://img.shields.io/github/workflow/status/gacts/setup-node-with-cache/tests?maxAge=30
[badge_release_version]:https://img.shields.io/github/release/gacts/setup-node-with-cache.svg?maxAge=30
[badge_license]:https://img.shields.io/github/license/gacts/setup-node-with-cache.svg?longCache=true
[badge_issues]:https://img.shields.io/github/issues/gacts/setup-node-with-cache.svg?maxAge=45
[badge_pulls]:https://img.shields.io/github/issues-pr/gacts/setup-node-with-cache.svg?maxAge=45

[link_build]:https://github.com/gacts/setup-node-with-cache/actions
[link_license]:https://github.com/gacts/setup-node-with-cache/blob/master/LICENSE
[link_issues]:https://github.com/gacts/setup-node-with-cache/issues
[link_create_issue]:https://github.com/gacts/setup-node-with-cache/issues/new
[link_pulls]:https://github.com/gacts/setup-node-with-cache/pulls

[use_dependabot]:https://docs.github.com/en/code-security/supply-chain-security/keeping-your-dependencies-updated-automatically/keeping-your-actions-up-to-date-with-dependabot
