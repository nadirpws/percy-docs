# percy-docs

Source for https://percy.io/docs. All docs are Markdown files in the `/docs` folder.

## Install dependencies

```
yarn
```

## Running the docs in development

First ensure you've setup [percy-web](https://github.com/percy/percy-web).

Next you'll need to link `percy-docs` to `percy-web` so that `percy-web` uses your local version of
`percy-docs`.

Here's how you do that:

```bash
cd path/to/percy-docs
yarn link
cd path/to/percy-web
yarn link percy-docs
yarn
yarn start
```

Now visit [http://dev.percy.local:4200/docs](http://dev.percy.local:4200/docs) and fully styled
local docs should appear.
