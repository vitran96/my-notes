[[NodeJS]] tooling to integrate with [[Git]] hook to parse and handle commit message

# Install
```shell
npm install -D @commitlint/cli @commitlint/config-conventional
```

# Sample
```javascript title="commitlint.config.js"
// https://commitlint.js.org/reference/rules.html
// Below is for <tickets> <type>[<scope>]: <subject>\n<body>\n<footer>
module.exports = {
  /*
   * Resolve and load @commitlint/config-conventional from node_modules.
   * Referenced packages must be installed
   */
  extends: ["@commitlint/config-conventional"],
  /*
   * Resolve and load conventional-changelog-atom from node_modules.
   * Referenced packages must be installed
     Eg: parserPreset: "conventional-changelog-atom",
   */
  parserPreset: {
	parserOpts: {
	  headerPattern: /^(([A-Z]+-\d+\s)*)?(\w+)(\(\w*\))?:\s(.+)$/,
	  headerCorrespondence: ['references', 'type', 'scope', 'subject'],
	  issuePrefixes: ['#'],
	},
  }
  /*
   * Resolve and load @commitlint/format from node_modules.
   * Referenced package must be installed
   */
  //formatter: "@commitlint/format",
  /*
   * Any rules defined here will override rules from @commitlint/config-conventional
   */
  // We can add more rule with plugin
  // [ { rules: { "rule-name-1": async (parsed, when, value) => { ... } } } ]
  //plugins: []
  rules: {
	"references-empty": [1, "never"],
    "type-enum": [2, "always", ["foo"]],
    "scope-enum": [2, "always", ["bar"]],
  },
  /*
   * Array of functions that return true if commitlint should ignore the given message.
   * Given array is merged with predefined functions, which consist of matchers like:
   *
   * - 'Merge pull request', 'Merge X into Y' or 'Merge branch X'
   * - 'Revert X'
   * - 'v1.2.3' (ie semver matcher)
   * - 'Automatic merge X' or 'Auto-merged X into Y'
   *
   * To see full list, check https://github.com/conventional-changelog/commitlint/blob/master/%40commitlint/is-ignored/src/defaults.ts.
   * To disable those ignores and run rules always, set `defaultIgnores: false` as shown below.
   */
  //ignores: [(commit) => commit === ""],
  /*
   * Whether commitlint uses the default ignore rules, see the description above.
   */
  //defaultIgnores: true,
  /*
   * Custom URL to show upon failure
   */
  //helpUrl:
  //  "https://github.com/conventional-changelog/commitlint/#what-is-commitlint",
  /*
   * Custom prompt configs
   */
  //prompt: {
  //  messages: {},
  //  questions: {
  //    type: {
  //      description: "please input type:",
  //    },
  //  },
  //},
};
```