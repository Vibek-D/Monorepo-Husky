# Monorepo-Husky-Lerna
A monorepo using lerna and using husky to run precommit hooks and then commit validation testing with commit hooks in all the monorepo packages

## Steps to be noted for using husky with lerna
1) In the rootb directory "npm install -D lerna" as a dev dependency

2) Run "npx lerna init" which creates the package folder

3) In the lerna.json file, if we add version: "0.0.1" which means that the version is for all the packages adn if we update the version: "0.0.2" it will effect all the packages versions, but if we want to release independent package versions, then we need to add version: "independent"

4) Install husky using "npm install -D husky" which checks if the commit message is proper pre-commit, runs npm test/other commands etc pre-commit, which is specified in the "husky/pre-commit" file

5) Now in the "package.json" file add a script, "prepare": "husky install", which installs husky folder with pre-commit scripts. Here the "prepare" script is a post install script which runs after we pull the repo from github in out local machine and run "yarn install"

6) Install commitizen using "npm install -D commitizen" which gives us proper commit messages

7) Initialize the commitizen to replace the regular "git commit" -> "git cz" using "npx commitizen init
cz-conventional-changelog --save-dev --save-exact" if using npm, and use "commitizen init cz-conventional-changelog --yarn --dev --exact" if using yarn

8) So now husky will have a "commit-msg" script which will match the "commitizen" commit message format and if it validates only then it will add the commit

9) 
  a) Add the command "lerna run --concurrency 1 --stream precommit --since HEAD --exclude-dependents" in the "husky/pre-commit" which uses lerna to recursively run the "precommit" script in each of the indivisual package's package.json file and then runs the "husky/commit-msg" file. So basically when we do "git cz"(equivalent to git commit, since we are using commitizen package), it runs the
  commands in the pre-commit  

  b) We can use a) or we can add a script in the global package.json like "test": "lerna run test" which uses lerna to run the "test"
  script in each of the package's package.json file's test script. Then we just need to replace the a) command "lerna run --concurrency 1 --stream precommit --since HEAD --exclude-dependents" with "npm run test". And now when we commit, husky will run the "pre-commit" hook file and it will find the "npm run test" command and it will run the test script in the global package json which runs "lerna run test" which runs the test script in all of the indivisual test script in the packages package.json file

10) If you have node_modules inside each monorepo, delete the node_modules and run "npx lerna bootstrap --hoist". It willcheck all the dependencies in the package.json of each package(monorepo) and will hoist it to the global node_modules and keep the dependencies common
accross all the monorepos

Note: So now in all the packages, the pre-commit hook will run and then the commit-msg hook will run using husky when we commit using "git cz" (which is commitizen basically). So using just one command all our mono repos will be tested and the committed

Note: If any command shows an error showing that "command [something] noyt found, install that npm package globally using" say "npm install --g lerna"

Note: To install the same package say "lodash", we can use the command "npx lerna add lodash", whcih will add lodash in the global node_modules and we can use it in the indivisual packages. But for that we have to add

"workspaces": [ "packages/*" ] in global package.json

"npmClient": "yarn" and "useWorkspaces": true in lerna.json

And now when we install say "lodash" using "npx lerna add lodash", it will install a same version lodash to all the monorepos