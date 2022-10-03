# kong-bug-demo

This demonstrates an issue in the golang [kong](https://github.com/alecthomas/kong) argument 
processing library (which, by the way, is an excellent library!)

# Summary

If you have multiple subcommands, which have constrained arguments (e.g. `existingdir` with default 
values) those constraints must pass even if the subcommand is not invoked.

# Kong Code

```go
type Options struct {
	Cmd1         Subcommand1 `cmd:""`
	Cmd2         Subcommand2 `cmd:""`
}

type Subcommand1 struct {
	Dir1 string `group:"dir" help:"get a directory" type:"existingdir" default:"dir1"`
}

type Subcommand2 struct {
	Dir2 string `group:"dir" help:"get a directory" type:"existingdir" default:"dir2"`
}

```

# Output

```shell
$ mkdir -p dir1 &&  go run main.go cmd-1 --dir-1 dir1
--dir-2: stat /Users/dsasser/personal/kong-bug-demo/dir2: no such file or directory
exit status 1
```

# Expected Output

The expectation is that the program would run, as the constraints on the operable subcommand have been satisfied.

Note that if the `default` value is left off the parameters in the subcommands, the program runs as expected.