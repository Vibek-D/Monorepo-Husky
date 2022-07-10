1) In the rootb directory "npm install -D lerna" as a dev dependency

2) Run "npx lerna init" which creates the package folder

3) In the lerna.json file, if we add version: "0.0.1" which means that the version is for all the packages adn if we update
the version: "0.0.2" it will effect all the packages versions, but if we want to release independent package versions, then 
we need to add version: "independent"

4) Install husky using "npm install -D husky" which checks if the commit message is proper pre-commit, runs npm test/other 
commands etc pre-commit, which is specified in the "husky/pre-commit" file

5) Now in the "package.json" file add a script, "prepare": "husky install", which installs husky folder with pre-commit 
scripts. Here the "prepare" script is a post install script which runs after we pull the repo from github in out local machine
and run "yarn install"

6) Install commitizen using "npm install -D commitizen" which gives us proper commit messages

7) Initialize the commitizen to replace the regular "git commit" -> "git cz" using "npx commitizen init
cz-conventional-changelog --save-dev --save-exact" if using npm, and use "commitizen init cz-conventional-changelog --yarn 
--dev --exact" if using yarn

8) So now husky will have a "commit-msg" script which will match the "commitizen" commit message format and if it validates 
only then it will add the commit

9) Add the command "lerna run --concurrency 1 --stream precommit --since HEAD --exclude-dependents" in the "husky/pre-commit"
which uses lerna to recursively run the "precommit" script in each of the indivisual package's package.json file and then runs
the "husky/commit-msg" file

Note: So now in all the packages, the pre-commit hook will run and then the commit-msg hook will run using husky when we commit
using "git cz" (which is commitizen basically). So using just one command all our mono repos will be tested and the committed

Note: If any command shows an error showing that "command [something] noyt found, install that npm package globally using"
say "npm install --g lerna"