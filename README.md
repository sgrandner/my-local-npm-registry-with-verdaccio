# Setup local npm registry with verdaccio

see
* https://blog.bitsrc.io/how-to-set-up-a-private-npm-registry-locally-1065e6790796
* https://blog.bitsrc.io/install-npm-private-packages-in-ci-cd-with-github-actions-746db95017cc

## setup verdaccio

### config

create verdaccio config (`config.yaml`) in folder conf


### run docker image of verdaccio

    docker run -it [--rm] --detach
        --publish 4873:4873
        --volume <working dir>/conf:/verdaccio/conf
        --volume <working dir>/storage:/verdaccio/storage
        --volume <working dir>/plugins:/verdaccio/plugins
        --name verdaccio verdaccio/verdaccio

### open in browser

    localhost:4873


### create registry user

    npm adduser --registry http://localhost:4873

*NOTE: Verdaccio default configuration uses `htpasswd` authentication plugin (other possible plugins: LDAP, Bitbucket, Gitlab, GitHub and more -> set in config.yaml)*

### login to registry (if user exists)

    npm login --registry http://localhost:4873


## publish to local registry

### publish

Run npm publish in root folder of the project you want to publish.

Two ways:
1) set registry on command line with publish

        npm publish --registry http://localhost:4873

1) set registry in package.json

        "publishConfig": {
            "registry": "http://localhost:4873"
        }

    and run publish

        npm publish

* package is published under the package name defined in package.json
* check on local verdaccio web page


### unpublish

Use with care !

    npm unpublish [--dry-run] --force --registry http://localhost:4873 <package>

* without `--force` it is rejected


## install package from local regsitry

There different possibilities for installation of public packages in a project.
Go to your poject.

1) call install with registry on command line

        npm i <package name> --registry http://localhost:4873

1) define a npmrc file (npm runtime config) globally, per project or per user
    * per project under `<project path>/.npmrc`

            <npm scope>:registry=http://localhost:4873
            [<further scope>]
            //localhost:4873
            [<further registry>]
            # always-auth=true

        * set registry for scope, where `<npm scope>` is the scope of your packages, e.g., `@mypackages`, usually user or company name
        * define registry on next line (without protocol !)
        * set further scopes and/or registries if needed

    * install the package

            npm i <package name>


## Install NPM Private Packages in CI/CD with GitHub Actions

...

see https://blog.bitsrc.io/install-npm-private-packages-in-ci-cd-with-github-actions-746db95017cc
