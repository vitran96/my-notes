A [[Git]] hook tool
# Install
```shell
npm i -d lefthook
```

# Sample config
```yml
pre-commit:
	parallel: true
	commands:
		server-check:
			env:
				LEFTHOOK_OUTPUT: meta,summary,success,failure,execution,execution_info,skips
			run: lefthook run server-check
		server-check:
			env:
				LEFTHOOK_OUTPUT: meta,summary,success,failure,execution,execution_info,skips
			run: lefthook run web-check
			
server-check:
	piped: true
	commands:
		server-format:
			root: server
			run: mvn spotless:apply
		server-lint:
			root: server
			run: mvn spotless:check
web-check:
	piped: true
	commands:
		web-format:
			root: web
			run: npm run format
		web-lint:
			root: web
			run: npm run lint
			
pre-push:
	parallel: true
	commands:
		server-validate:
			env:
				LEFTHOOK_OUTPUT: meta,summary,success,failure,execution,execution_info,skips
			run: lefthook run server-validate
		web-validate:
			env:
				LEFTHOOK_OUTPUT: meta,summary,success,failure,execution,execution_info,skips
			run: lefthook run web-validate

server-validate:
	piped: true
	commands:
		server-test:
			root: server
			run: mvn test
		server-coco:
			root: server
			run: mvn jacoco:check
web-validate:
	piped: true
	commands:
		web-test:
			root: web
			run: npm test

commit-msg:
	commands:
		commit-lint:
			run: npx commitlint $1
```