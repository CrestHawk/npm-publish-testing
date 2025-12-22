# npm-publish-testing

Testing the easiest method of pulling from one npm repository and then pushing to another

Understanding where the registry is specified.

When you run npm publish.. there is an order of precedence that determines which repository it uses.
The highest priority overrides everything.
The following lists them starting at the highest priority:

1. The registry flag in the npm publish command
   npm publish --registry=http://some-registry/

2. The publishConfig.registry in the package.json file
   "publishConfig": {
   "registry": "http://localhost:4873/"
   }

3. Scoped registry in .npmrc
   @internal:registry=http://localhost:4873/

4. Default registry in .npmrc
   registry=http://localhost:4873/

5. npm default (The global repository)
   registry=http://localhost:4873/

There is also an order of precedence for an npm i, but it works slightly differently

| Context                           | Precedence   |
| --------------------------------- | ------------ |
| Scoped registry in `.npmrc`       | 1            |
| Project `.npmrc` default registry | 2            |
| User `.npmrc` default registry    | 3            |
| Env var `NPM_CONFIG_REGISTRY`     | 4            |
| CLI `--registry`                  | 5 (highest)  |
| npm default registry              | 6 (fallback) |

The highest is in the cli command again.
e.g. npm install --registry=http://localhost:4873/
