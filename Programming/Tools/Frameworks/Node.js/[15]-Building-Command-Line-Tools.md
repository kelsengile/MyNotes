[Previous](./[14]-Working-With-Databases.md) | [Table of Contents](./[0]-Introduction-to-Node.js.md) | [Next](./[16]-Debugging-And-Logging.md)

*Tooling And Projects*

# Lesson 15 - Building Command-Line Tools

A command-line interface (CLI) is a program you run in the terminal. Node.js is a popular choice for building them. This lesson covers parsing arguments, asking questions, formatting output, and distributing your tool.

## 15.1 Parsing arguments

A good CLI accepts **positional arguments** (`copy a.txt b.txt`), **flags** (`--verbose`, `-v`), and **options with values** (`--output=out.txt`).

Using the built-in `util.parseArgs`:

```js
#!/usr/bin/env node
import { parseArgs } from "node:util";

const { values, positionals } = parseArgs({
  options: {
    help: { type: "boolean", short: "h" },
    output: { type: "string", short: "o", default: "out.txt" },
    count: { type: "string", short: "n" },
  },
  allowPositionals: true,
});

if (values.help || positionals.length === 0) {
  console.log("Usage: mytool <input> [-o output] [-n count]");
  process.exit(values.help ? 0 : 1);
}

console.log("Input:", positionals[0]);
console.log("Output:", values.output);
```

For tools with many commands (like `git commit`, `git push`), libraries such as **commander**, **yargs**, or **citty** handle subcommands, automatic `--help`, and validation.

```js
// npm install commander
import { Command } from "commander";

const program = new Command();
program.name("mytool").version("1.0.0");

program.command("greet <name>")
  .option("-l, --loud", "shout")
  .action((name, opts) => {
    const msg = `Hello, ${name}`;
    console.log(opts.loud ? msg.toUpperCase() : msg);
  });

program.parse();
```

---

## 15.2 Interactive prompts

Some tools ask the user questions. The built-in `readline` module handles this.

```js
import readline from "node:readline/promises";
import { stdin as input, stdout as output } from "node:process";

const rl = readline.createInterface({ input, output });

const name = await rl.question("What is your name? ");
const answer = await rl.question("Continue? (y/n) ");

console.log(`Hello ${name}`);
rl.close();

if (answer.toLowerCase() !== "y") process.exit(0);
```

For richer prompts (lists, checkboxes, passwords, confirmations), use a library such as `@inquirer/prompts` or `@clack/prompts`.

Good practice:

- Provide flags for every question so the tool can also run **non-interactively** in scripts and CI.
- Check `process.stdin.isTTY` to see whether a real person is typing.

---

## 15.3 Output formatting

Send **normal results** to `stdout` and **errors and messages** to `stderr`. This lets users redirect output without mixing in error text.

```js
console.log("result data");       // stdout
console.error("Something broke"); // stderr
```

Adding color with ANSI escape codes (or libraries like `chalk` or `picocolors`):

```js
const red = (s) => `\x1b[31m${s}\x1b[0m`;
const green = (s) => `\x1b[32m${s}\x1b[0m`;

console.log(green("Success"));
console.error(red("Failed"));
```

Node.js also includes `util.styleText("green", "Success")` in recent versions.

Helpful output tools:

- `console.table(array)` prints arrays of objects as a table.
- Spinners and progress bars (`ora`, `cli-progress`) show long tasks are working.
- Respect the `NO_COLOR` environment variable and disable color when output is piped.
- Offer a `--json` flag so other programs can read your output reliably.
- Use proper exit codes: `0` for success and non-zero for failure (see Lesson 8).

---

## 15.4 Distributing a CLI

Step 1: add a **shebang** line at the top of your entry file so Unix systems know to run it with Node.js:

```js
#!/usr/bin/env node
```

Step 2: declare the command name in `package.json` using the `bin` field:

```json
{
  "name": "mytool",
  "version": "1.0.0",
  "type": "module",
  "bin": {
    "mytool": "./cli.js"
  },
  "files": ["cli.js"]
}
```

Step 3: test it locally. In your project folder run:

```bash
npm link
mytool --help
```

Step 4: publish with `npm publish` (see Lesson 3). Users can then:

```bash
npm install -g mytool     # install globally
npx mytool --help         # or run without installing
```

Tips: keep your tool's startup fast, include a clear `README` with usage examples, and set the `engines` field so users on very old Node.js versions get a helpful warning.

[Previous](./[14]-Working-With-Databases.md) | [Table of Contents](./[0]-Introduction-to-Node.js.md) | [Next](./[16]-Debugging-And-Logging.md)