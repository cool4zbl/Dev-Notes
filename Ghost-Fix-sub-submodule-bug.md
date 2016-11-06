### Fix a sub-sub-module bug 

#### why `grunt init` fails when installing Ghost in dev mode

`core/client/` grunt init return error:

```shell

>> Ran "grunt init" in "core/client".

Running "clean:tmp" (clean) task
>> 0 paths cleaned.

Running "subgrunt:dev" (subgrunt) task
Running "shell:ember:dev" (shell) task

> ghost-admin@1.0.0-alpha.5 build /Users/zhangbinliu/my_web_dev/Cool4zbl.com/myDevGhost/core/client
> ember build

ENOENT: no such file or directory, open '../../.git/modules/core/client/commondir'
Error: ENOENT: no such file or directory, open '../../.git/modules/core/client/commondir'
    at Error (native)
    at Object.fs.openSync (fs.js:549:18)
    at Object.fs.readFileSync (fs.js:397:15)
    at findRepoHandleLinkedWorktree (/Users/zhangbinliu/my_web_dev/Cool4zbl.com/myDevGhost/core/client/node_modules/git-repo-info/index.js:27:32)
    at findRepo (/Users/zhangbinliu/my_web_dev/Cool4zbl.com/myDevGhost/core/client/node_modules/git-repo-info/index.js:47:14)
    at module.exports (/Users/zhangbinliu/my_web_dev/Cool4zbl.com/myDevGhost/core/client/node_modules/git-repo-info/index.js:145:21)
    at version (/Users/zhangbinliu/my_web_dev/Cool4zbl.com/myDevGhost/core/client/node_modules/git-repo-version/index.js:11:14)
    at CoreObject.module.exports.config (/Users/zhangbinliu/my_web_dev/Cool4zbl.com/myDevGhost/core/client/node_modules/ember-cli-app-version/index.js:10:46)
    at CoreObject.superWrapper [as config] (/Users/zhangbinliu/my_web_dev/Cool4zbl.com/myDevGhost/core/client/node_modules/core-object/lib/assign-properties.js:32:18)
    at /Users/zhangbinliu/my_web_dev/Cool4zbl.com/myDevGhost/core/client/node_modules/ember-cli/lib/models/project.js:224:29

npm ERR! Darwin 16.0.0
npm ERR! argv "/usr/local/bin/node" "/Users/zhangbinliu/my_web_dev/Cool4zbl.com/myDevGhost/core/client/node_modules/.bin/npm" "run" "build"
npm ERR! node v4.2.1
npm ERR! npm  v2.15.5
npm ERR! code ELIFECYCLE
npm ERR! ghost-admin@1.0.0-alpha.5 build: `ember build`
npm ERR! Exit status 1
npm ERR!
npm ERR! Failed at the ghost-admin@1.0.0-alpha.5 build script 'ember build'.
npm ERR! This is most likely a problem with the ghost-admin package,
npm ERR! not with npm itself.
npm ERR! Tell the author that this fails on your system:
npm ERR!     ember build
npm ERR! You can get information on how to open an issue for this project with:
npm ERR!     npm bugs ghost-admin
npm ERR! Or if that isn't available, you can get their info via:
npm ERR!
npm ERR!     npm owner ls ghost-admin
npm ERR! There is likely additional logging output above.

```



Ghost -> require('Ghost-admin') -> require('ember-cli') -> require('git-repo-info')

https://github.com/ember-cli/ember-cli/blob/master/lib/utilities/version-utils.js#L5

then, `git-repo-info` v1.3.0  breaks.



related issue:

https://github.com/TryGhost/Ghost/issues/7614

https://github.com/rwjblue/git-repo-info/issues/27

https://github.com/rwjblue/git-repo-info/blob/master/index.js#L25

https://github.com/rwjblue/git-repo-info/pull/26/files#diff-168726dbe96b3ce427e7fedce31bb0bcR25



#### About `commondir` 

https://git-scm.com/docs/gitrepository-layout

> commondir
>
> If this file exists, \$GIT_COMMON_DIR (see [git[1\]](https://git-scm.com/docs/git)) will be set to the path specified in this file if it is not explicitly set. If the specified path is relative, it is relative to $GIT_DIR. The repository with commondir is incomplete without the repository pointed by "commondir".
