---
{
    "prev": "tutorials/todo-app-chapter-1"
}
---

# Todo App: Chapter 2
::: subtitle
Building the UI
:::

In this tutorial series we'll be running through how to build a small, full-stack, Todo App. In [Chapter 1][1] we learned how to setup a basic (unauthenticated) API with 5 routes provided by the Origami Resource. In this chapter we'll focus on building a UI with Origami's [Zen Components][2] and bundling our app with [Rollup][3] and [Sass][4].

This example app can be installed with the CLI using:
```bash
origami example todo-app
```

> ## Requirements
> This tutorial requires:
> - [Todo App - Chapter 1](/tutorials/todo-app-chapter-1) (setting up the API)
> - [The Origami CLI](https://github.com/origami-cms/cli)
> - [Postman](https://www.getpostman.com) (or something similar)

---

## Setting up the build process

Our front-end will consist of 3 main things:
- Our styling (Sass compiled into CSS)
- Our HTML (`./public/index.html`)
- Our JS (bundled from `./src` with Rollup)

### 1. Getting Sass working

For our styles, we're going to use the [zen-css][5] library compiled with Sass, however feel free to use any library/framework you're comfortable with.
Let's install these with:
```bash
yarn add -D node-sass zen-css
```

Then, create a file under `./src/sass/todo-app.scss` with:

```scss {data-filename=./src/sass/todo-app.scss}
@import 'zen-css/base';
```

This will import the `zen-css` base library. To build this, open your `package.json` and add into `scripts`:

```js {data-filename=./package.json}
{
    // ...
    scripts: {
        "build:sass": "node-sass src/sass/ -o public/ --include-path ./node_modules"
    }
    // ...
}
```

If you now run `yarn build:sass` you should see the `todo-app.css` sitting in your `public` folder.


### 2. Serving static files in Origami

You may be asking how we serve static files in Origami. Simply add a path to the `server.static` option in your `.origami` file.
EG:
```js {data-filename=.origami}
{
    // ...
    "server": {
        "static": "./public"
    }
    // ...
}
```

Now, anything in this directory will be served directly from your app. Try loading [http://localhost:9999/todo-app.css](http://localhost:9999/todo-app.css) in your browser.

For the HTML file, add it under `./public/index.html` so it's the default page that Origami loads.

```html {.line-numbers data-filename=./public/index.html}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Origami example - Todo App</title>
    <link rel="stylesheet" href="/todo-app.css">
</head>
<body>
    <template id="todo-item">
        <li class="todo-item">
            <zen-checkbox></zen-checkbox>
            <zen-input></zen-input>
            <zen-icon type="cross"></zen-icon>
        </li>
    </template>

    <div class="todo center card">
        <h3 class="text-center">Todo List</h3>
        <ul class="todo-items"></ul>
        <zen-input placeholder="Your next task…"></zen-input>
    </div>
    <zen-icon-set></zen-icon-set>

    <script src="/todo-app.min.js"></script>
</body>
</html>
```

### 3. Javascript
Finally, we need to setup bundling for our Javascript, then we'll be ready to build our app. There are many great compilers and bundlers, but for simplicity Rollup was chosen in this series (you could very easily use Webpack or another tool). Let's install rollup and some plugins for handling node modules with:
```bash
yarn add -D rollup rollup-plugin-commonjs rollup-plugin-node-resolve
```

We're also going use be using `origami-zen` for our component library, so feel free to install that too with:
```bash
yarn add -D origami-zen
```

The two files we'll need is the `./rollup.config.js` and our `./src/todo-app.js` (file to bundle). Lets add these:

```js {.line-numbers data-filename=./rollup.config.js}
import resolve from 'rollup-plugin-node-resolve';
import commonjs from 'rollup-plugin-commonjs';

export default {
    entry: 'src/todo-app.js',
    dest: 'public/todo-app.min.js',
    format: 'iife',

    plugins: [resolve(), commonjs()]
};
```

```js {.line-numbers data-filename=./src/todo-app.js}
// Import only the components we need from Zen
import 'origami-zen/components/IconSet/IconSet';
import 'origami-zen/components/Icon/Icon';
import 'origami-zen/components/Checkbox/Checkbox';
import 'origami-zen/components/Input/Input';
```



[1]: /docs/tutorials/todo-app-chapter-1
[2]: https://github.com/origami-cms/zen
[3]: https://rollupjs.org
[4]: https://sass-lang.com/
[5]: https://github.com/origami-cms/zen-css
