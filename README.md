# Summary

I'm trying to simplify a GitHub action for a project.

It's purpose is to pull down a set of npm packages from one Artifactory repo, reversion them and then push them back up to another Artifactory repo.

I wanted to understand how npm publish works under the hood, because for a particular legacy version of our software (node 6 and npm 3) we were having a lot of trouble with this job.

# Understanding NPM Publish and NPM Install

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

# Approach Decided

The URL for the cloud Artifactory is kepy within a Github variable.

Then there are two textboxes (with default values) on the Action itself that hold the names of the repos in Artifactory that the job will pull from and then publish to.

Then in the action yaml, we use the approach of specifying the URL and the repo name within the npm i and npm publish commands inline.

e.g.
npm install package-name@1.0.0 --registry=http://some-registry
npm publish --registry=http://some-registry

# Benefits of this approach

It will work for both our legacy version (npm 3) as well as our later versions
It simplifies the pipelines action yaml file
It does not store hardcoded URLs for our Artifactory within our codebase

# Verdaccio

I first wanted some sort of application I could use locally that emulates having a private npm repo, such as Artifactory, but was actually free. I went with Verdaccio.

https://www.verdaccio.org/

https://www.verdaccio.org/docs/setup-npm

It was pretty simple to setup.

Once installed you can just run the command verdaccio in your terminal, and after a little while it's up and running.

You can view it in the browser then using http://localhost:4873/

Then I was ready to start publishing packages to my local npm repo

I tested it using npm publish --registry=localhost:4873 and it worked as expected
