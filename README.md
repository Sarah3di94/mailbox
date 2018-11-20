# Mail Box

Small wrapper around MJML and Nodemailer for (awesome) HTML emails.


## Installation

1. Install [Node.js](https://nodejs.org)
2. Install Mail Box CLI:

```bash
# npm
npm install -g @mvsde/mailbox

# Yarn
yarn global add @mvsde/mailbox
```


## New Project

```bash
mailbox create [folder]

# Optional name
mailbox create [folder] --name <project-name>
```

The folder defaults to the current directory (`.`) and the name to `mailbox-project`.


## Configuration

Create the optional file `mjml.config.js` in the project root to customize MJML settings.

```js
module.exports = {
  fonts: { /* … */ },
  keepComments: true,
  beautify: false,
  minify: false,
  validationLevel: 'soft'
}
```

The MJML documentation provides a short [description for all available options](https://mjml.io/documentation/#inside-node-js).


## Project Setup

### Layouts

The file `src/layouts/default.mjml` serves as a base layout for an HTML email. It uses  [MJML (Mailjet Markup Language)](https://mjml.io/documentation/) for simpler email markup.

### Includes

The `src/includes`-folder is optional, it can be renamed or removed altogether. The idea behind this folder is to have one location for reusable chunks of markup. With [`<mj-include>`](https://mjml.io/documentation/#mj-include) they can be included in layouts or other includes.

### Attachments

Files in the folder `src/attachments` can be referenced in a test setup. Nodemailer attaches these to the mail and provides a `cid` so images can be loaded from the attachments. The contents of the attachment folder will be copied as-is to the output during build time.

### Tests

The `tests` folder has to contain at least a `default.json` file which serves as the base test setup. You can create more JSON test files, but they always need a `default.json` to extend.

The test file content is passed to Nunjucks as a context. This allows the use of [Nunjucks templating features](https://mozilla.github.io/nunjucks/templating.html) to enhance the development and testing phase.

The special `attachments`-key in a test file will be transformed to allow static file linking during development and `cid`-attachment linking in test emails.

```json
{
  "attachments": {
    "name": "filename.ext"
  }
}
```

The attachment is available as `{{ attachments.name }}` within the email template. The value is the filename of the attachment relative to the `src/attachments` directory.


## Development

You can start a development server with auto-reload using the following command:

```bash
mailbox dev [layout]

# Optional test data
mailbox dev [layout] --test <test-data>
```

The layout defaults to `default` (the `src/layouts/default.json` file). The Nunjucks context isn't populated with test data by default. You can specifiy test data with `--test default`.


## Test

To send a test email use the following command:

```bash
mailbox test [layout] --to <email-address>

# Optional sender address
mailbox test [layout] --to <email-address> --from <email-address>

# Optional different test data
mailbox test [layout] --to <email-address> --test <test-data>
```

Both layout and test default to `default` (the `src/layouts/default.json` and `test/default.json` files). A recipient email address has to be specified with `--to info@example.com`, the sender email is optional and defaults to `test@example.com`. Test data other than default can be specified with `--test another-test`.


## Build

To generate production ready files use this command:

```bash
mailbox build [layout]

# Optional different output location
mailbox build [layout] --output <path>
```

The layout defaults to `default` (the `src/layouts/default.json` file). The output path can be changed with `--output path/to/output.html`. The full filepath has to be specified.
