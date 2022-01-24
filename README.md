# build-mc-project
A GitHub action that builds a Minecraft project using Dash.

## Usage
1. Create a compiler config that uses the `rewriteForPackaging` plugin instead of `simpleRewrite`:

```json
{
	"plugins": [
		[
			"typeScript",
			{
				"inlineSourceMap": true
			}
		],
		"entityIdentifierAlias",
		"customEntityComponents",
		"customItemComponents",
		"customBlockComponents",
		"customCommands",
		"moLang",
		[
			"rewriteForPackaging",
			{
				"packName": "myProject",
				"format": "mctemplate" // Or "mcaddon"/"mcworld"
			}
		]
	]
}
```

2. Create a file for your release workflow that looks like this:

```yml
name: 'build-project'

on:
    release:
        types:
            - created

jobs:
    build:
        runs-on: ubuntu-latest

        steps:
            - name: Checkout project
              uses: actions/checkout@v2
            - name: Building project
              uses: bridge-core/build-mc-project@main
              with:
                  project-name: MyProject
                  package-type: mctemplate
                  compiler-config: .bridge/compiler/build.json
            - name: Release
              uses: softprops/action-gh-release@v1
              if: startsWith(github.ref, 'refs/tags/')
              with:
                  files: MyProject.mctemplate
              env:
                  GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

3. Upon creating a new GitHub release, bridge. will now automatically build a new version of the project and attach it as a release artifact.
