---
sidebar_label: Go
---
import CodeBlock from '@theme/CodeBlock';
import conn_test from '!!raw-loader!../../../_clients/go/tests/conn_test.go'


# Go

Up at the top of the file, directly below the front matter import CodeBlock and then import your example/test files:
```
import CodeBlock from '@theme/CodeBlock';
import conn_test from '!!raw-loader!../../../_clients/go/tests/conn_test.go'
```

Now you can refer to the included file as `conn_test` and create a code block
like so:
```
<CodeBlock language="go">{conn_test}</CodeBlock>
```

:::note
I will work on a system to import named blocks from the source files 
(denoted by named start and end comments), but I am pretty consumed working on integration docs and then rewriting a good part of the docs to be Cloud First.
:::

Result:
<CodeBlock language="go">{conn_test}</CodeBlock>

This is the script I use before working on each PR
```
#!/usr/bin/zsh
cd $DOCS/ClickHouse
git fetch ClickHouse master && git reset --hard ClickHouse/master
git switch master
git pull ClickHouse master
git push origin master

cd $DOCS/clickhouse-docs
git fetch ClickHouse main && git reset --hard ClickHouse/main
git switch main
git pull ClickHouse main
git push origin main

cd $DOCS/clickhouse-go
git fetch ClickHouse main && git reset --hard ClickHouse/main
git switch main
git pull ClickHouse main
git push origin main

${HOME}/scripts/update-CHdocs

echo "# git switch -c <issue number and name> ClickHouse/main"
```

This is `update-CHdocs`, which gets called by above.  The interesting lines 
for you are at the end, they copy the examples and tests from the `clickhouse-go` repo into `clickhouse-docs/docs/_clients/go`

```
#!/usr/bin/zsh
source ~/.zshrc
cp -r $DOCS/ClickHouse/docs/en/development $DOCS/clickhouse-docs/docs/en/
cp -r $DOCS/ClickHouse/docs/en/engines $DOCS/clickhouse-docs/docs/en/
cp -r $DOCS/ClickHouse/docs/en/getting-started $DOCS/clickhouse-docs/docs/en/
cp -r $DOCS/ClickHouse/docs/en/interfaces $DOCS/clickhouse-docs/docs/en/
cp -r $DOCS/ClickHouse/docs/en/operations $DOCS/clickhouse-docs/docs/en/
cp -r $DOCS/ClickHouse/docs/en/sql-reference $DOCS/clickhouse-docs/docs/en/

cp -r $DOCS/ClickHouse/docs/ru/* $DOCS/clickhouse-docs/docs/ru/
cp -r $DOCS/ClickHouse/docs/zh $DOCS/clickhouse-docs/docs/
cp -r $DOCS/clickhouse-go/examples $DOCS/clickhouse-docs/docs/_clients/go/
cp -r $DOCS/clickhouse-go/tests $DOCS/clickhouse-docs/docs/_clients/go/
```

